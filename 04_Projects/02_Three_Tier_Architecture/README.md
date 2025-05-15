# Three-Tier Architecture Project

## Architecture Overview

This project implements a traditional three-tier architecture in Azure, consisting of:

1. **Presentation Tier**: Azure App Service with multiple instances behind Azure Application Gateway with WAF
2. **Application Tier**: Azure Kubernetes Service (AKS) hosting business logic microservices
3. **Data Tier**: Azure SQL Database with geo-replication and Azure Cache for Redis

Supporting components include:
- Azure Front Door for global load balancing and CDN
- Azure Key Vault for secret management
- Azure Container Registry for container images
- Application Insights and Azure Monitor for monitoring
- Azure API Management for API façade
- Virtual Network with network security groups
- Azure Bastion for secure management access

![Architecture Diagram](architecture.png)

## Business Requirements

A fictional financial services company, Woodgrove Financial, needs a scalable, secure platform for their investment portfolio management system with the following requirements:

1. Support for thousands of concurrent users across multiple regions
2. Strict security and compliance requirements (PCI-DSS, GDPR)
3. High availability (99.99% uptime) and disaster recovery
4. Real-time data processing for investment decisions
5. Ability to scale during market trading hours
6. Secure access to sensitive financial data
7. Comprehensive audit logging and monitoring
8. Integration with third-party financial data providers

## Implementation Guide

### Part 1: Network Infrastructure Setup

1. Create Resource Group:
   ```bash
   az group create --name woodgrove-financial-rg --location eastus
   ```

2. Create Virtual Network infrastructure:
   ```bash
   # Create main virtual network
   az network vnet create --name woodgrove-vnet --resource-group woodgrove-financial-rg --location eastus --address-prefix 10.0.0.0/16
   
   # Create subnets for each tier and services
   az network vnet subnet create --name web-subnet --resource-group woodgrove-financial-rg --vnet-name woodgrove-vnet --address-prefix 10.0.1.0/24
   
   az network vnet subnet create --name app-subnet --resource-group woodgrove-financial-rg --vnet-name woodgrove-vnet --address-prefix 10.0.2.0/24
   
   az network vnet subnet create --name data-subnet --resource-group woodgrove-financial-rg --vnet-name woodgrove-vnet --address-prefix 10.0.3.0/24
   
   az network vnet subnet create --name appgw-subnet --resource-group woodgrove-financial-rg --vnet-name woodgrove-vnet --address-prefix 10.0.4.0/24
   
   az network vnet subnet create --name bastion-subnet --resource-group woodgrove-financial-rg --vnet-name woodgrove-vnet --address-prefix 10.0.5.0/24 --name AzureBastionSubnet
   
   # Create network security groups for each subnet
   az network nsg create --name web-nsg --resource-group woodgrove-financial-rg --location eastus
   az network nsg create --name app-nsg --resource-group woodgrove-financial-rg --location eastus
   az network nsg create --name data-nsg --resource-group woodgrove-financial-rg --location eastus
   
   # Associate NSGs with subnets
   az network vnet subnet update --name web-subnet --resource-group woodgrove-financial-rg --vnet-name woodgrove-vnet --network-security-group web-nsg
   az network vnet subnet update --name app-subnet --resource-group woodgrove-financial-rg --vnet-name woodgrove-vnet --network-security-group app-nsg
   az network vnet subnet update --name data-subnet --resource-group woodgrove-financial-rg --vnet-name woodgrove-vnet --network-security-group data-nsg
   ```

3. Create Azure Bastion for secure management:
   ```bash
   az network public-ip create --name bastion-pip --resource-group woodgrove-financial-rg --location eastus --sku Standard
   
   az network bastion create --name woodgrove-bastion --resource-group woodgrove-financial-rg --vnet-name woodgrove-vnet --public-ip-address bastion-pip
   ```

### Part 2: Data Tier Setup

1. Create Azure SQL Database with geo-replication:
   ```bash
   # Create primary SQL server
   az sql server create --name woodgrove-sql-primary --resource-group woodgrove-financial-rg --location eastus --admin-user sqladmin --admin-password "YourSecurePasswordHere!"
   
   # Create secondary SQL server
   az sql server create --name woodgrove-sql-secondary --resource-group woodgrove-financial-rg --location westus --admin-user sqladmin --admin-password "YourSecurePasswordHere!"
   
   # Create database on primary server
   az sql db create --name woodgrove-db --resource-group woodgrove-financial-rg --server woodgrove-sql-primary --service-objective S3 --zone-redundant true
   
   # Configure geo-replication
   az sql failover-group create --name woodgrove-failover-group --resource-group woodgrove-financial-rg --server woodgrove-sql-primary --partner-server woodgrove-sql-secondary --add-db woodgrove-db
   
   # Configure firewall to only allow Azure services
   az sql server vnet-rule create --name AllowVnetAccess --resource-group woodgrove-financial-rg --server woodgrove-sql-primary --vnet-name woodgrove-vnet --subnet data-subnet
   ```

2. Set up Azure Cache for Redis:
   ```bash
   az redis create --name woodgrove-redis --resource-group woodgrove-financial-rg --location eastus --vm-size P1 --sku Premium --subnet-id $(az network vnet subnet show --resource-group woodgrove-financial-rg --vnet-name woodgrove-vnet --name data-subnet --query id -o tsv)
   ```

3. Create Data Storage Account:
   ```bash
   az storage account create --name woodgrovestorage --resource-group woodgrove-financial-rg --location eastus --sku Standard_GRS --kind StorageV2 --https-only true --default-action Deny
   
   # Create container for financial data
   az storage container create --name financial-data --account-name woodgrovestorage
   
   # Add network rule to allow access from app subnet
   az storage account network-rule add --account-name woodgrovestorage --subnet app-subnet --vnet-name woodgrove-vnet --resource-group woodgrove-financial-rg
   ```

### Part 3: Application Tier Setup

1. Create Azure Kubernetes Service:
   ```bash
   # Create AKS cluster in the app subnet
   az aks create \
     --resource-group woodgrove-financial-rg \
     --name woodgrove-aks \
     --node-count 3 \
     --network-plugin azure \
     --vnet-subnet-id $(az network vnet subnet show --resource-group woodgrove-financial-rg --vnet-name woodgrove-vnet --name app-subnet --query id -o tsv) \
     --docker-bridge-address 172.17.0.1/16 \
     --dns-service-ip 10.2.0.10 \
     --service-cidr 10.2.0.0/24 \
     --enable-managed-identity \
     --node-vm-size Standard_DS3_v2 \
     --enable-cluster-autoscaler \
     --min-count 3 \
     --max-count 10 \
     --enable-addons monitoring
   ```

2. Create Azure Container Registry:
   ```bash
   az acr create --name woodgrovefinancialacr --resource-group woodgrove-financial-rg --sku Premium --location eastus
   
   # Grant AKS access to ACR
   az aks update --name woodgrove-aks --resource-group woodgrove-financial-rg --attach-acr woodgrovefinancialacr
   ```

3. Set up API Management:
   ```bash
   az apim create --name woodgrove-apim --resource-group woodgrove-financial-rg --publisher-name "Woodgrove Financial" --publisher-email "admin@woodgrovefinancial.com" --sku-name Premium --sku-capacity 1 --location eastus --virtual-network External --subnet $(az network vnet subnet show --resource-group woodgrove-financial-rg --vnet-name woodgrove-vnet --name app-subnet --query id -o tsv)
   ```

### Part 4: Presentation Tier Setup

1. Create App Service Plan and Web Apps:
   ```bash
   # Create App Service Plan
   az appservice plan create --name woodgrove-asp --resource-group woodgrove-financial-rg --sku P2V2 --number-of-workers 3 --location eastus
   
   # Create Web App
   az webapp create --name woodgrove-webapp --resource-group woodgrove-financial-rg --plan woodgrove-asp --runtime "DOTNET|6.0"
   
   # Configure the web app for VNet integration
   az webapp vnet-integration add --name woodgrove-webapp --resource-group woodgrove-financial-rg --vnet woodgrove-vnet --subnet web-subnet
   ```

2. Create Application Gateway with WAF:
   ```bash
   # Create public IP for App Gateway
   az network public-ip create --name appgw-pip --resource-group woodgrove-financial-rg --location eastus --allocation-method Static --sku Standard
   
   # Create App Gateway with WAF
   az network application-gateway create \
     --name woodgrove-appgw \
     --resource-group woodgrove-financial-rg \
     --location eastus \
     --sku WAF_v2 \
     --public-ip-address appgw-pip \
     --vnet-name woodgrove-vnet \
     --subnet appgw-subnet \
     --capacity 2 \
     --waf-policy
   
   # Configure backend pool
   az network application-gateway address-pool create \
     --gateway-name woodgrove-appgw \
     --resource-group woodgrove-financial-rg \
     --name WebAppBackendPool \
     --servers woodgrove-webapp.azurewebsites.net
   
   # Create HTTP settings
   az network application-gateway http-settings create \
     --gateway-name woodgrove-appgw \
     --resource-group woodgrove-financial-rg \
     --name WebAppHttpSettings \
     --port 443 \
     --protocol Https \
     --cookie-based-affinity Disabled \
     --host-name woodgrove-webapp.azurewebsites.net
   
   # Create frontend port
   az network application-gateway frontend-port create \
     --gateway-name woodgrove-appgw \
     --resource-group woodgrove-financial-rg \
     --name HttpsPort \
     --port 443
   
   # Create listener
   az network application-gateway http-listener create \
     --gateway-name woodgrove-appgw \
     --resource-group woodgrove-financial-rg \
     --name HttpsListener \
     --frontend-port HttpsPort \
     --frontend-ip appGatewayFrontendIP \
     --ssl-cert CertName \
     --host-name portal.woodgrovefinancial.com
   
   # Create routing rule
   az network application-gateway rule create \
     --gateway-name woodgrove-appgw \
     --resource-group woodgrove-financial-rg \
     --name DefaultRule \
     --http-listener HttpsListener \
     --address-pool WebAppBackendPool \
     --http-settings WebAppHttpSettings
   ```

3. Set up Azure Front Door:
   ```bash
   az network front-door create \
     --name woodgrove-frontdoor \
     --resource-group woodgrove-financial-rg \
     --backend-address appgw-pip.eastus.cloudapp.azure.com \
     --accepted-protocols Https \
     --forwarding-protocol HttpsOnly \
     --certificate-name-check Enabled
   ```

### Part 5: Security and Monitoring Setup

1. Create Key Vault for secrets:
   ```bash
   az keyvault create --name woodgrove-keyvault --resource-group woodgrove-financial-rg --location eastus --enable-purge-protection true --sku Premium
   
   # Store database connection string
   az keyvault secret set --vault-name woodgrove-keyvault --name DbConnectionString --value "Server=woodgrove-failover-group.database.windows.net;Database=woodgrove-db;Authentication=Active Directory Managed Identity;"
   
   # Store Redis connection string
   REDIS_CONN=$(az redis list-keys --name woodgrove-redis --resource-group woodgrove-financial-rg --query primaryKey -o tsv)
   az keyvault secret set --vault-name woodgrove-keyvault --name RedisConnectionString --value "woodgrove-redis.redis.cache.windows.net:6380,password=$REDIS_CONN,ssl=True,abortConnect=False"
   ```

2. Set up Application Insights and Log Analytics:
   ```bash
   # Create Log Analytics Workspace
   az monitor log-analytics workspace create --name woodgrove-logs --resource-group woodgrove-financial-rg --location eastus --sku PerGB2018
   
   # Create Application Insights
   az monitor app-insights component create --app woodgrove-insights --resource-group woodgrove-financial-rg --location eastus --application-type web --workspace $(az monitor log-analytics workspace show --name woodgrove-logs --resource-group woodgrove-financial-rg --query id -o tsv)
   
   # Configure Web App with Application Insights
   INSTRUMENTATION_KEY=$(az monitor app-insights component show --app woodgrove-insights --resource-group woodgrove-financial-rg --query instrumentationKey -o tsv)
   az webapp config appsettings set --name woodgrove-webapp --resource-group woodgrove-financial-rg --settings APPINSIGHTS_INSTRUMENTATIONKEY=$INSTRUMENTATION_KEY ApplicationInsightsAgent_EXTENSION_VERSION=~2
   ```

3. Create monitoring dashboards and alerts:
   ```bash
   # Create action group for alerts
   az monitor action-group create --name woodgrove-critical-alerts --resource-group woodgrove-financial-rg --short-name wgca --email-receiver name=operations email=ops@woodgrovefinancial.com
   
   # Create alert for high CPU on AKS nodes
   az monitor metrics alert create \
     --name "AKS CPU Alert" \
     --resource-group woodgrove-financial-rg \
     --scopes $(az aks show --name woodgrove-aks --resource-group woodgrove-financial-rg --query id -o tsv) \
     --condition "avg Percentage CPU > 80" \
     --window-size 5m \
     --evaluation-frequency 1m \
     --action $(az monitor action-group show --name woodgrove-critical-alerts --resource-group woodgrove-financial-rg --query id -o tsv)
   
   # Create alert for database DTU usage
   az monitor metrics alert create \
     --name "SQL DTU Alert" \
     --resource-group woodgrove-financial-rg \
     --scopes $(az sql db show --name woodgrove-db --server woodgrove-sql-primary --resource-group woodgrove-financial-rg --query id -o tsv) \
     --condition "avg dtu_consumption_percent > 90" \
     --window-size 5m \
     --evaluation-frequency 1m \
     --action $(az monitor action-group show --name woodgrove-critical-alerts --resource-group woodgrove-financial-rg --query id -o tsv)
   ```

### Part 6: Application Deployment

1. Deploy microservices to AKS:
   ```bash
   # Get AKS credentials
   az aks get-credentials --name woodgrove-aks --resource-group woodgrove-financial-rg
   
   # Create namespace for application
   kubectl create namespace woodgrove-app
   
   # Apply Kubernetes manifests for microservices
   kubectl apply -f kubernetes/portfolio-service.yaml -n woodgrove-app
   kubectl apply -f kubernetes/transaction-service.yaml -n woodgrove-app
   kubectl apply -f kubernetes/user-service.yaml -n woodgrove-app
   kubectl apply -f kubernetes/analytics-service.yaml -n woodgrove-app
   
   # Create ingress for API access
   kubectl apply -f kubernetes/ingress.yaml -n woodgrove-app
   ```

2. Deploy web application to App Service:
   ```bash
   # Deploy the web app
   az webapp deployment source config-zip --name woodgrove-webapp --resource-group woodgrove-financial-rg --src webportal.zip
   
   # Configure application settings
   az webapp config appsettings set --name woodgrove-webapp --resource-group woodgrove-financial-rg --settings \
     ApiUrl=https://woodgrove-apim.azure-api.net \
     KeyVaultUri=https://woodgrove-keyvault.vault.azure.net/ \
     WEBSITE_VNET_ROUTE_ALL=1
   
   # Enable managed identity for the web app
   az webapp identity assign --name woodgrove-webapp --resource-group woodgrove-financial-rg
   
   # Grant web app access to Key Vault
   WEBAPP_PRINCIPAL_ID=$(az webapp identity show --name woodgrove-webapp --resource-group woodgrove-financial-rg --query principalId -o tsv)
   az keyvault set-policy --name woodgrove-keyvault --object-id $WEBAPP_PRINCIPAL_ID --secret-permissions get list
   ```

3. Configure API Management:
   ```bash
   # Import APIs from AKS
   az apim api import --path /portfolio --resource-group woodgrove-financial-rg --service-name woodgrove-apim --specification-url https://kubernetes-service-endpoint/portfolio/swagger.json --specification-format OpenApi
   
   az apim api import --path /transactions --resource-group woodgrove-financial-rg --service-name woodgrove-apim --specification-url https://kubernetes-service-endpoint/transactions/swagger.json --specification-format OpenApi
   
   # Apply rate limiting policy
   az apim policy create --api-id portfolio --resource-group woodgrove-financial-rg --service-name woodgrove-apim --policy-id rate-limit --value @policies/rate-limit.xml
   ```

### Part 7: Testing and Validation

1. Test high availability and failover:
   ```bash
   # Test SQL failover
   az sql failover-group failover --name woodgrove-failover-group --resource-group woodgrove-financial-rg --server woodgrove-sql-primary
   
   # Scale AKS horizontally
   az aks scale --name woodgrove-aks --resource-group woodgrove-financial-rg --node-count 5
   ```

2. Perform load testing:
   ```bash
   # Create a load testing resource
   az load test create --name woodgrove-load-test --resource-group woodgrove-financial-rg --location eastus
   
   # Upload test script and run test
   az load test upload-script --name woodgrove-load-test --resource-group woodgrove-financial-rg --file loadtest.jmx
   
   az load test run --name woodgrove-load-test --resource-group woodgrove-financial-rg --test-id test1 --description "Initial load test"
   ```

## Infrastructure as Code

The entire infrastructure is deployed using ARM templates and Bicep. The main deployment files include:

1. [network.bicep](deploy/network.bicep) - Network infrastructure
2. [data-tier.bicep](deploy/data-tier.bicep) - Data tier components
3. [app-tier.bicep](deploy/app-tier.bicep) - Application tier components
4. [web-tier.bicep](deploy/web-tier.bicep) - Presentation tier components
5. [security.bicep](deploy/security.bicep) - Security components
6. [monitoring.bicep](deploy/monitoring.bicep) - Monitoring components
7. [main.bicep](deploy/main.bicep) - Main deployment file

To deploy the entire infrastructure:
```bash
az deployment group create --resource-group woodgrove-financial-rg --template-file deploy/main.bicep --parameters deploy/parameters.json
```

## Monitoring and Management

1. **Health Monitoring**:
   - Azure Monitor for application and infrastructure metrics
   - Application Insights for application performance monitoring
   - Log Analytics for centralized logging and analysis
   - Azure Monitor workbooks for visual dashboards

2. **Security Monitoring**:
   - Azure Security Center for security posture management
   - Azure Sentinel for security information and event management
   - Key Vault diagnostics for audit of secret access
   - Network Watcher for network security monitoring

3. **Performance Monitoring**:
   - Custom dashboards for key performance indicators
   - Synthetic transactions to validate end-to-end performance
   - Database Query Performance Insight for SQL optimization
   - Transaction end-to-end tracing with Application Insights

4. **Operational Procedures**:
   - Regular backup testing and validation
   - Scheduled maintenance windows
   - Patching and upgrade planning
   - Disaster recovery drills

## Cost Optimization

1. **Resource Optimization**:
   - Right-size AKS clusters based on actual usage patterns
   - Auto-scaling for App Service and AKS to match demand
   - Implement Azure Budgets and cost allocation
   - Reserved Instances for predictable workloads

2. **Storage Optimization**:
   - Implement data lifecycle management
   - Choose appropriate storage tiers
   - Compress and archive infrequently accessed data
   - Enable soft delete and versioning for data protection

3. **Networking Optimization**:
   - Optimize Front Door routing for global traffic
   - Implement Redis caching to reduce database load
   - Configure CDN for static content
   - Use ExpressRoute for high-volume, predictable traffic

## Security Considerations

1. **Network Security**:
   - Network segmentation with subnets and NSGs
   - DDoS protection with Azure Front Door
   - Web Application Firewall (WAF) policies
   - Private endpoints for PaaS services

2. **Data Security**:
   - Transparent Data Encryption for SQL Database
   - Data masking and row-level security
   - Always Encrypted for sensitive data
   - Storage encryption and secure transfer

3. **Identity and Access**:
   - Azure AD for identity management
   - Role-Based Access Control (RBAC)
   - Managed identities for secure service-to-service authentication
   - Just-in-time access for administration

4. **Compliance**:
   - Audit logging for all operations
   - Compliance reporting with Azure Security Center
   - Regular security assessments
   - Data residency controls for global operations

## Next Steps

1. Implement Blue-Green deployments for AKS workloads
2. Set up Azure DevOps CI/CD pipelines for application deployment
3. Implement Infrastructure as Code for all resources using Azure DevOps
4. Add advanced analytics with Azure Synapse Analytics
5. Implement multi-region deployment for global availability