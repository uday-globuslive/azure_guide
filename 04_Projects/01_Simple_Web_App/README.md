# Simple Web App Project

## Architecture Overview

This project implements a scalable web application with a database backend. The architecture consists of:
- Azure App Service for hosting the web application
- Azure SQL Database for data storage
- Azure Storage for static content
- Application Insights for monitoring
- Azure Key Vault for secret management
- Azure Content Delivery Network (CDN) for static content delivery

![Architecture Diagram](architecture.png)

## Business Requirements

A fictional company, Contoso Retail, needs a scalable product catalog web application with the following requirements:

1. Public-facing website to display product information
2. Administrative portal for updating product catalog
3. Fast performance across multiple geographic regions
4. High availability with 99.9% uptime
5. Ability to handle seasonal traffic spikes
6. Secure storage of connection strings and credentials
7. Comprehensive monitoring and alerting
8. Cost-effective operation during normal business hours

## Implementation Guide

### Part 1: Set Up Resources

1. Create Resource Group:
   ```bash
   az group create --name contoso-webapp-rg --location eastus
   ```

2. Set up Azure SQL Database:
   ```bash
   az sql server create --name contoso-sql-server --resource-group contoso-webapp-rg --location eastus --admin-user dbadmin --admin-password "YourSecurePassword123!"
   
   az sql db create --name contoso-db --resource-group contoso-webapp-rg --server contoso-sql-server --service-objective S0
   
   # Configure firewall to allow Azure services
   az sql server firewall-rule create --name AllowAzureServices --resource-group contoso-webapp-rg --server contoso-sql-server --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
   ```

3. Create Storage Account for static content:
   ```bash
   az storage account create --name contosostorage --resource-group contoso-webapp-rg --location eastus --sku Standard_LRS --kind StorageV2
   
   # Create a container for product images
   az storage container create --name product-images --account-name contosostorage --public-access blob
   ```

4. Set up Key Vault:
   ```bash
   az keyvault create --name contoso-keyvault --resource-group contoso-webapp-rg --location eastus
   
   # Store database connection string
   az keyvault secret set --vault-name contoso-keyvault --name DbConnection --value "Server=tcp:contoso-sql-server.database.windows.net,1433;Database=contoso-db;User ID=dbadmin;Password=YourSecurePassword123!;Encrypt=true;Connection Timeout=30;"
   
   # Store storage account connection string
   STORAGE_CONNECTION=$(az storage account show-connection-string --name contosostorage --resource-group contoso-webapp-rg --query connectionString --output tsv)
   
   az keyvault secret set --vault-name contoso-keyvault --name StorageConnection --value "$STORAGE_CONNECTION"
   ```

5. Create App Service Plan and Web App:
   ```bash
   az appservice plan create --name contoso-asp --resource-group contoso-webapp-rg --location eastus --sku P1V2
   
   az webapp create --name contoso-webapp --resource-group contoso-webapp-rg --plan contoso-asp --runtime "DOTNET|6.0"
   
   # Enable managed identity for the web app
   az webapp identity assign --name contoso-webapp --resource-group contoso-webapp-rg
   
   # Get the principal ID of the web app
   PRINCIPAL_ID=$(az webapp identity show --name contoso-webapp --resource-group contoso-webapp-rg --query principalId --output tsv)
   
   # Grant the web app access to Key Vault secrets
   az keyvault set-policy --name contoso-keyvault --object-id $PRINCIPAL_ID --secret-permissions get list
   ```

6. Set up Application Insights:
   ```bash
   az monitor app-insights component create --app contoso-insights --resource-group contoso-webapp-rg --location eastus --application-type web
   
   # Get the instrumentation key
   INSTRUMENTATION_KEY=$(az monitor app-insights component show --app contoso-insights --resource-group contoso-webapp-rg --query instrumentationKey --output tsv)
   
   # Configure the web app to use Application Insights
   az webapp config appsettings set --name contoso-webapp --resource-group contoso-webapp-rg --settings APPINSIGHTS_INSTRUMENTATIONKEY=$INSTRUMENTATION_KEY ApplicationInsightsAgent_EXTENSION_VERSION=~2
   ```

7. Set up Azure CDN:
   ```bash
   # Create CDN profile
   az cdn profile create --name contoso-cdn --resource-group contoso-webapp-rg --sku Standard_Microsoft
   
   # Create CDN endpoint for the storage account
   az cdn endpoint create --name contoso-cdn-endpoint --profile-name contoso-cdn --resource-group contoso-webapp-rg --origin-host-header contosostorage.blob.core.windows.net --origin contosostorage.blob.core.windows.net --origin-path "/product-images"
   ```

### Part 2: Deploy the Web Application

1. Clone the sample application repository:
   ```bash
   git clone https://github.com/Azure-Samples/dotnet-core-sql-api
   cd dotnet-core-sql-api
   ```

2. Update application settings to use Key Vault:
   
   Add the following code to startup.cs:
   ```csharp
   // Configure Key Vault
   var keyVaultName = Configuration["KeyVaultName"];
   var keyVaultUri = $"https://{keyVaultName}.vault.azure.net/";
   var azureServiceTokenProvider = new AzureServiceTokenProvider();
   var keyVaultClient = new KeyVaultClient(
       new KeyVaultClient.AuthenticationCallback(
           azureServiceTokenProvider.KeyVaultTokenCallback));
   builder.AddAzureKeyVault(keyVaultUri, keyVaultClient, new DefaultKeyVaultSecretManager());
   ```

3. Configure database connection to use Key Vault reference:
   
   Update appsettings.json:
   ```json
   {
     "KeyVaultName": "contoso-keyvault",
     "ConnectionStrings": {
       "DefaultConnection": "Key Vault Reference: https://contoso-keyvault.vault.azure.net/secrets/DbConnection"
     }
   }
   ```

4. Configure web app settings:
   ```bash
   az webapp config appsettings set --name contoso-webapp --resource-group contoso-webapp-rg --settings KeyVaultName=contoso-keyvault CDN_URL=https://contoso-cdn-endpoint.azureedge.net
   ```

5. Deploy the application:
   ```bash
   # Build and publish the application
   dotnet publish -c Release -o ./publish
   
   # Deploy to Azure
   az webapp deployment source config-zip --name contoso-webapp --resource-group contoso-webapp-rg --src ./publish.zip
   ```

### Part 3: Configure Scaling and High Availability

1. Set up autoscaling:
   ```bash
   az monitor autoscale create --resource-group contoso-webapp-rg --resource contoso-asp --resource-type "Microsoft.Web/serverfarms" --name autoscale-contoso --min-count 2 --max-count 5 --count 2
   
   # Add a scale rule based on CPU usage
   az monitor autoscale rule create --resource-group contoso-webapp-rg --autoscale-name autoscale-contoso --condition "Percentage CPU > 70 avg 5m" --scale out 1
   
   # Add a scale rule to scale in
   az monitor autoscale rule create --resource-group contoso-webapp-rg --autoscale-name autoscale-contoso --condition "Percentage CPU < 30 avg 5m" --scale in 1
   ```

2. Set up scheduled scaling:
   ```bash
   # Scale down during non-business hours
   az monitor autoscale profile create --resource-group contoso-webapp-rg --autoscale-name autoscale-contoso --name "Night and Weekend" --start 2019-11-01T18:00 --end 2019-11-04T08:00 --timezone "Eastern Standard Time" --recurrence weeks 1 --copy-days sat sun --min-count 1 --max-count 2 --count 1
   ```

3. Configure SQL Database geo-replication:
   ```bash
   # Create a failover group
   az sql failover-group create --name contoso-failover --partner-server contoso-sql-server-secondary --resource-group contoso-webapp-rg --server contoso-sql-server --add-db contoso-db
   ```

### Part 4: Set Up Monitoring and Alerting

1. Create availability test:
   ```bash
   az monitor app-insights web-test create --resource-group contoso-webapp-rg --app-insight-name contoso-insights --name "contoso-webapp-availability" --location eastus --web-test-kind ping --frequency 300 --test-definition path/to/webtest.xml
   ```

2. Set up alerts:
   ```bash
   # Create an action group for email notifications
   az monitor action-group create --name contoso-alert-email --resource-group contoso-webapp-rg --action email notificationemail --short-name cae
   
   # Create an alert for high server response time
   az monitor metrics alert create --name "High Response Time" --resource-group contoso-webapp-rg --scopes $(az webapp show --name contoso-webapp --resource-group contoso-webapp-rg --query id -o tsv) --condition "avg RequestTime > 5" --window-size 5m --evaluation-frequency 1m --action $(az monitor action-group show --name contoso-alert-email --resource-group contoso-webapp-rg --query id -o tsv)
   
   # Create an alert for availability test failures
   az monitor metrics alert create --name "Availability Test Failure" --resource-group contoso-webapp-rg --scopes $(az webapp show --name contoso-webapp --resource-group contoso-webapp-rg --query id -o tsv) --condition "avg availabilityResults/availabilityPercentage < 90" --window-size 5m --evaluation-frequency 1m --action $(az monitor action-group show --name contoso-alert-email --resource-group contoso-webapp-rg --query id -o tsv)
   ```

3. Create a dashboard:
   ```bash
   az portal dashboard create --name "Contoso Web App Dashboard" --resource-group contoso-webapp-rg --location eastus --public-access-enabled true --dashboard path/to/dashboard.json
   ```

## Infrastructure as Code

The entire infrastructure can be deployed using the following ARM template:

[See ARM template file](deploy/main.json)

To deploy the ARM template:
```bash
az deployment group create --resource-group contoso-webapp-rg --template-file deploy/main.json --parameters deploy/parameters.json
```

## Monitoring and Management

1. **Application Performance Monitoring**:
   - Use Application Insights to monitor application performance, user behavior, and exceptions
   - Set up custom events and metrics for business-critical flows
   - Create a dashboard with key performance indicators

2. **Infrastructure Monitoring**:
   - Monitor App Service metrics such as CPU, memory, and request count
   - Set up alerts for abnormal resource usage
   - Use Azure Monitor to track dependencies and failures

3. **Security Monitoring**:
   - Enable Azure Security Center for security recommendations
   - Set up Azure Defender for SQL to detect potential threats
   - Review audit logs regularly

4. **Operational Tasks**:
   - Schedule regular backups of SQL Database
   - Implement disaster recovery procedures
   - Test failover scenarios periodically

## Cost Optimization

1. **Resource Optimization**:
   - Use autoscaling to adjust capacity based on demand
   - Implement scheduled scaling to reduce costs during off-hours
   - Consider Reserved Instances for App Service Plan for long-term cost savings

2. **Monitoring Costs**:
   - Set up cost alerts in Azure Cost Management
   - Use resource tags for cost allocation
   - Regularly review unused resources and optimize

3. **Performance Tier Selection**:
   - Select appropriate performance tiers for SQL Database based on workload
   - Consider serverless options for development/test environments
   - Use premium tier only for production environments

## Security Considerations

1. **Data Protection**:
   - Enable Transparent Data Encryption for SQL Database
   - Implement row-level security for multi-tenant data
   - Use Azure Storage encryption for static content

2. **Network Security**:
   - Configure Web Application Firewall (WAF) for the web application
   - Use Service Endpoints or Private Link for database access
   - Implement network security groups to restrict traffic

3. **Identity and Access Management**:
   - Use managed identities for Azure resources
   - Implement Azure AD authentication for the application
   - Apply least privilege principle for all service accounts

4. **Secret Management**:
   - Store all secrets in Key Vault
   - Regularly rotate credentials
   - Use access policies to restrict access to secrets

## Next Steps

1. Enhance the application with authentication using Azure AD B2C
2. Implement a staging slot for zero-downtime deployments
3. Set up a CI/CD pipeline using Azure DevOps
4. Implement a Content Management System for product information