# Microservices Architecture Project

## Architecture Overview

This project implements a modern microservices architecture on Azure, consisting of:

1. **Container Orchestration**: Azure Kubernetes Service (AKS) for hosting microservices
2. **API Management**: Azure API Management for API façade and gateway
3. **Service Mesh**: Istio/Linkerd for service-to-service communication
4. **Data Stores**: 
   - Azure Cosmos DB (multi-model database)
   - Azure Cache for Redis (caching)
   - Azure Database for PostgreSQL (relational data)
5. **Event-Driven Architecture**:
   - Azure Event Grid for event routing
   - Azure Service Bus for reliable messaging
   - Azure Event Hubs for event streaming
6. **DevOps Pipeline**:
   - Azure DevOps for CI/CD
   - Container Registry for image management
   - Helm for deployment management

Supporting components include:
- Azure Key Vault for secret management
- Application Insights for distributed tracing
- Azure Monitor for observability
- Azure Front Door for global load balancing
- Azure Active Directory for identity management

![Architecture Diagram](architecture.png)

## Business Requirements

A fictional e-commerce company, NorthwindTraders, needs a scalable, resilient platform with the following requirements:

1. Microservices-based product catalog, inventory, ordering, and customer management
2. High scalability to handle seasonal traffic spikes (10x normal volume)
3. Global customer base requiring low latency across multiple regions
4. Strong isolation between services for independent deployment and scaling
5. Resilience to service failures with circuit breaking and fallbacks
6. Real-time inventory updates and order processing
7. Comprehensive monitoring with distributed tracing
8. DevOps practices for continuous deployment
9. Cost optimization through efficient resource usage

## Implementation Guide

### Part 1: Infrastructure Setup

1. Create Resource Group:
   ```bash
   az group create --name northwind-microservices-rg --location eastus
   ```

2. Create Virtual Network:
   ```bash
   az network vnet create --name northwind-vnet --resource-group northwind-microservices-rg --address-prefix 10.0.0.0/16
   
   az network vnet subnet create --name aks-subnet --resource-group northwind-microservices-rg --vnet-name northwind-vnet --address-prefix 10.0.0.0/22
   
   az network vnet subnet create --name db-subnet --resource-group northwind-microservices-rg --vnet-name northwind-vnet --address-prefix 10.0.4.0/24
   
   az network vnet subnet create --name apim-subnet --resource-group northwind-microservices-rg --vnet-name northwind-vnet --address-prefix 10.0.5.0/24
   ```

3. Create Azure Container Registry:
   ```bash
   az acr create --name northwindacr --resource-group northwind-microservices-rg --sku Premium --location eastus --admin-enabled false
   ```

4. Create AKS Cluster:
   ```bash
   az aks create \
     --resource-group northwind-microservices-rg \
     --name northwind-aks \
     --node-count 3 \
     --enable-managed-identity \
     --network-plugin azure \
     --vnet-subnet-id $(az network vnet subnet show --resource-group northwind-microservices-rg --vnet-name northwind-vnet --name aks-subnet --query id -o tsv) \
     --docker-bridge-address 172.17.0.1/16 \
     --dns-service-ip 10.2.0.10 \
     --service-cidr 10.2.0.0/24 \
     --enable-addons monitoring \
     --node-vm-size Standard_DS3_v2 \
     --enable-cluster-autoscaler \
     --min-count 3 \
     --max-count 10 \
     --zones 1 2 3
   
   # Attach ACR to AKS
   az aks update --name northwind-aks --resource-group northwind-microservices-rg --attach-acr northwindacr
   ```

5. Create Cosmos DB:
   ```bash
   az cosmosdb create --name northwind-cosmos --resource-group northwind-microservices-rg --kind MongoDB --capabilities EnableMongo EnableAggregationPipeline
   
   az cosmosdb mongodb database create --account-name northwind-cosmos --resource-group northwind-microservices-rg --name ProductCatalog
   
   az cosmosdb mongodb database create --account-name northwind-cosmos --resource-group northwind-microservices-rg --name CustomerProfile
   
   az cosmosdb mongodb collection create --account-name northwind-cosmos --resource-group northwind-microservices-rg --database-name ProductCatalog --name Products --shard productId
   ```

6. Create Azure Cache for Redis:
   ```bash
   az redis create --name northwind-redis --resource-group northwind-microservices-rg --location eastus --sku Premium --vm-size P1 --shard-count 2
   ```

7. Create PostgreSQL Flexible Server:
   ```bash
   az postgres flexible-server create \
     --name northwind-postgres \
     --resource-group northwind-microservices-rg \
     --location eastus \
     --admin-user dbadmin \
     --admin-password "$(openssl rand -base64 16)" \
     --sku-name Standard_D4s_v3 \
     --tier GeneralPurpose \
     --version 14 \
     --storage-size 256 \
     --high-availability ZoneRedundant
   
   # Create database for Order service
   az postgres flexible-server db create --resource-group northwind-microservices-rg --server-name northwind-postgres --database-name OrderDb
   
   # Create database for Inventory service
   az postgres flexible-server db create --resource-group northwind-microservices-rg --server-name northwind-postgres --database-name InventoryDb
   ```

8. Create Service Bus:
   ```bash
   az servicebus namespace create --name northwind-servicebus --resource-group northwind-microservices-rg --location eastus --sku Premium
   
   # Create topics for various domain events
   az servicebus topic create --name order-events --namespace-name northwind-servicebus --resource-group northwind-microservices-rg
   az servicebus topic create --name inventory-events --namespace-name northwind-servicebus --resource-group northwind-microservices-rg
   az servicebus topic create --name product-events --namespace-name northwind-servicebus --resource-group northwind-microservices-rg
   
   # Create subscriptions for services
   az servicebus topic subscription create --name order-service --namespace-name northwind-servicebus --resource-group northwind-microservices-rg --topic-name inventory-events
   az servicebus topic subscription create --name notification-service --namespace-name northwind-servicebus --resource-group northwind-microservices-rg --topic-name order-events
   ```

9. Create Event Hubs for high-volume events:
   ```bash
   az eventhubs namespace create --name northwind-eventhub --resource-group northwind-microservices-rg --location eastus --sku Standard
   
   # Create event hub for clickstream data
   az eventhubs eventhub create --name clickstream-events --namespace-name northwind-eventhub --resource-group northwind-microservices-rg --partition-count 32
   
   # Create event hub for telemetry data
   az eventhubs eventhub create --name telemetry-events --namespace-name northwind-eventhub --resource-group northwind-microservices-rg --partition-count 16
   ```

10. Create Key Vault:
    ```bash
    az keyvault create --name northwind-keyvault --resource-group northwind-microservices-rg --location eastus --sku standard
    ```

11. Create API Management:
    ```bash
    az apim create --name northwind-apim --resource-group northwind-microservices-rg --publisher-name "Northwind Traders" --publisher-email "admin@northwindtraders.com" --sku-name Premium --sku-capacity 1 --location eastus --virtual-network External --subnet $(az network vnet subnet show --resource-group northwind-microservices-rg --vnet-name northwind-vnet --name apim-subnet --query id -o tsv)
    ```

12. Set up Application Insights:
    ```bash
    az monitor app-insights component create --app northwind-insights --resource-group northwind-microservices-rg --location eastus --kind web
    ```

13. Create Azure Front Door:
    ```bash
    az afd profile create --resource-group northwind-microservices-rg --profile-name northwind-frontdoor --sku Premium_AzureFrontDoor
    
    # Add endpoint
    az afd endpoint create --resource-group northwind-microservices-rg --profile-name northwind-frontdoor --endpoint-name northwind-global --enabled-state Enabled
    ```

### Part 2: Service Mesh Setup

1. Install Istio on AKS:
   ```bash
   # Get credentials for AKS
   az aks get-credentials --resource-group northwind-microservices-rg --name northwind-aks
   
   # Install Istio using Helm
   helm repo add istio https://istio-release.storage.googleapis.com/charts
   helm repo update
   
   kubectl create namespace istio-system
   
   helm install istio-base istio/base -n istio-system
   helm install istiod istio/istiod -n istio-system --wait
   
   # Install Istio Ingress Gateway
   kubectl create namespace istio-ingress
   kubectl label namespace istio-ingress istio-injection=enabled
   helm install istio-ingress istio/gateway -n istio-ingress
   ```

2. Install Kiali Dashboard:
   ```bash
   kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.15/samples/addons/kiali.yaml
   kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.15/samples/addons/prometheus.yaml
   kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.15/samples/addons/grafana.yaml
   kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.15/samples/addons/jaeger.yaml
   ```

3. Configure mTLS:
   ```bash
   kubectl apply -f - <<EOF
   apiVersion: security.istio.io/v1beta1
   kind: PeerAuthentication
   metadata:
     name: default
     namespace: istio-system
   spec:
     mtls:
       mode: STRICT
   EOF
   ```

### Part 3: Microservices Deployment

1. Prepare Kubernetes Namespaces:
   ```bash
   kubectl create namespace northwind-prod
   kubectl label namespace northwind-prod istio-injection=enabled
   
   kubectl create namespace northwind-dev
   kubectl label namespace northwind-dev istio-injection=enabled
   ```

2. Deploy Product Catalog Service:
   ```bash
   # Create ConfigMap
   kubectl apply -f - <<EOF
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: product-catalog-config
     namespace: northwind-prod
   data:
     COSMOS_DB_HOST: northwind-cosmos.mongo.cosmos.azure.com
     COSMOS_DB_DATABASE: ProductCatalog
     REDIS_HOST: northwind-redis.redis.cache.windows.net
     SERVICE_BUS_TOPIC: product-events
   EOF
   
   # Create Deployment
   kubectl apply -f - <<EOF
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: product-catalog
     namespace: northwind-prod
   spec:
     replicas: 3
     selector:
       matchLabels:
         app: product-catalog
     template:
       metadata:
         labels:
           app: product-catalog
       spec:
         containers:
         - name: product-catalog
           image: northwindacr.azurecr.io/product-catalog:latest
           ports:
           - containerPort: 8080
           resources:
             requests:
               memory: "256Mi"
               cpu: "100m"
             limits:
               memory: "512Mi"
               cpu: "500m"
           envFrom:
           - configMapRef:
               name: product-catalog-config
           env:
           - name: COSMOS_DB_KEY
             valueFrom:
               secretKeyRef:
                 name: cosmos-secrets
                 key: primaryKey
           readinessProbe:
             httpGet:
               path: /health
               port: 8080
             initialDelaySeconds: 10
             periodSeconds: 5
         imagePullSecrets:
         - name: acr-secret
   EOF
   
   # Create Service
   kubectl apply -f - <<EOF
   apiVersion: v1
   kind: Service
   metadata:
     name: product-catalog
     namespace: northwind-prod
   spec:
     selector:
       app: product-catalog
     ports:
     - port: 80
       targetPort: 8080
   EOF
   
   # Create Virtual Service (Istio)
   kubectl apply -f - <<EOF
   apiVersion: networking.istio.io/v1alpha3
   kind: VirtualService
   metadata:
     name: product-catalog
     namespace: northwind-prod
   spec:
     hosts:
     - "product-catalog.northwindtraders.com"
     - "product-catalog"
     gateways:
     - istio-ingress/northwind-gateway
     - mesh
     http:
     - route:
       - destination:
           host: product-catalog
           port:
             number: 80
   EOF
   ```

3. Deploy Order Service:
   ```bash
   # Create ConfigMap
   kubectl apply -f - <<EOF
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: order-service-config
     namespace: northwind-prod
   data:
     POSTGRES_HOST: northwind-postgres.postgres.database.azure.com
     POSTGRES_DB: OrderDb
     SERVICE_BUS_TOPIC: order-events
   EOF
   
   # Create Deployment
   kubectl apply -f - <<EOF
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: order-service
     namespace: northwind-prod
   spec:
     replicas: 3
     selector:
       matchLabels:
         app: order-service
     template:
       metadata:
         labels:
           app: order-service
       spec:
         containers:
         - name: order-service
           image: northwindacr.azurecr.io/order-service:latest
           ports:
           - containerPort: 8080
           resources:
             requests:
               memory: "256Mi"
               cpu: "100m"
             limits:
               memory: "512Mi"
               cpu: "500m"
           envFrom:
           - configMapRef:
               name: order-service-config
           env:
           - name: POSTGRES_PASSWORD
             valueFrom:
               secretKeyRef:
                 name: postgres-secrets
                 key: password
           readinessProbe:
             httpGet:
               path: /health
               port: 8080
             initialDelaySeconds: 10
             periodSeconds: 5
         imagePullSecrets:
         - name: acr-secret
   EOF
   
   # Create Service
   kubectl apply -f - <<EOF
   apiVersion: v1
   kind: Service
   metadata:
     name: order-service
     namespace: northwind-prod
   spec:
     selector:
       app: order-service
     ports:
     - port: 80
       targetPort: 8080
   EOF
   ```

4. Deploy Inventory Service:
   ```bash
   # Create ConfigMap
   kubectl apply -f - <<EOF
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: inventory-service-config
     namespace: northwind-prod
   data:
     POSTGRES_HOST: northwind-postgres.postgres.database.azure.com
     POSTGRES_DB: InventoryDb
     SERVICE_BUS_TOPIC: inventory-events
     SERVICE_BUS_SUBSCRIPTION: inventory-updates
   EOF
   
   # Create Deployment
   kubectl apply -f - <<EOF
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: inventory-service
     namespace: northwind-prod
   spec:
     replicas: 3
     selector:
       matchLabels:
         app: inventory-service
     template:
       metadata:
         labels:
           app: inventory-service
       spec:
         containers:
         - name: inventory-service
           image: northwindacr.azurecr.io/inventory-service:latest
           ports:
           - containerPort: 8080
           resources:
             requests:
               memory: "256Mi"
               cpu: "100m"
             limits:
               memory: "512Mi"
               cpu: "500m"
           envFrom:
           - configMapRef:
               name: inventory-service-config
           env:
           - name: POSTGRES_PASSWORD
             valueFrom:
               secretKeyRef:
                 name: postgres-secrets
                 key: password
           readinessProbe:
             httpGet:
               path: /health
               port: 8080
             initialDelaySeconds: 10
             periodSeconds: 5
         imagePullSecrets:
         - name: acr-secret
   EOF
   
   # Create Service
   kubectl apply -f - <<EOF
   apiVersion: v1
   kind: Service
   metadata:
     name: inventory-service
     namespace: northwind-prod
   spec:
     selector:
       app: inventory-service
     ports:
     - port: 80
       targetPort: 8080
   EOF
   ```

5. Deploy Customer Profile Service:
   ```bash
   # Create ConfigMap
   kubectl apply -f - <<EOF
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: customer-profile-config
     namespace: northwind-prod
   data:
     COSMOS_DB_HOST: northwind-cosmos.mongo.cosmos.azure.com
     COSMOS_DB_DATABASE: CustomerProfile
     REDIS_HOST: northwind-redis.redis.cache.windows.net
   EOF
   
   # Create Deployment
   kubectl apply -f - <<EOF
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: customer-profile
     namespace: northwind-prod
   spec:
     replicas: 3
     selector:
       matchLabels:
         app: customer-profile
     template:
       metadata:
         labels:
           app: customer-profile
       spec:
         containers:
         - name: customer-profile
           image: northwindacr.azurecr.io/customer-profile:latest
           ports:
           - containerPort: 8080
           resources:
             requests:
               memory: "256Mi"
               cpu: "100m"
             limits:
               memory: "512Mi"
               cpu: "500m"
           envFrom:
           - configMapRef:
               name: customer-profile-config
           env:
           - name: COSMOS_DB_KEY
             valueFrom:
               secretKeyRef:
                 name: cosmos-secrets
                 key: primaryKey
           readinessProbe:
             httpGet:
               path: /health
               port: 8080
             initialDelaySeconds: 10
             periodSeconds: 5
         imagePullSecrets:
         - name: acr-secret
   EOF
   
   # Create Service
   kubectl apply -f - <<EOF
   apiVersion: v1
   kind: Service
   metadata:
     name: customer-profile
     namespace: northwind-prod
   spec:
     selector:
       app: customer-profile
     ports:
     - port: 80
       targetPort: 8080
   EOF
   ```

6. Deploy Gateway Configuration:
   ```bash
   kubectl apply -f - <<EOF
   apiVersion: networking.istio.io/v1alpha3
   kind: Gateway
   metadata:
     name: northwind-gateway
     namespace: istio-ingress
   spec:
     selector:
       istio: ingressgateway
     servers:
     - port:
         number: 80
         name: http
         protocol: HTTP
       hosts:
       - "api.northwindtraders.com"
       - "product-catalog.northwindtraders.com"
       - "orders.northwindtraders.com"
       - "inventory.northwindtraders.com"
       - "customers.northwindtraders.com"
   EOF
   ```

7. Configure Circuit Breakers and Timeouts:
   ```bash
   kubectl apply -f - <<EOF
   apiVersion: networking.istio.io/v1alpha3
   kind: DestinationRule
   metadata:
     name: order-service
     namespace: northwind-prod
   spec:
     host: order-service
     trafficPolicy:
       connectionPool:
         tcp:
           maxConnections: 100
         http:
           http1MaxPendingRequests: 10
           maxRequestsPerConnection: 10
       outlierDetection:
         consecutive5xxErrors: 5
         interval: 10s
         baseEjectionTime: 30s
       loadBalancer:
         simple: ROUND_ROBIN
   EOF
   ```

### Part 4: API Management Integration

1. Create API definitions in APIM:
   ```bash
   # Get the Istio Ingress IP
   INGRESS_IP=$(kubectl -n istio-ingress get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
   
   # Import APIs
   az apim api import --resource-group northwind-microservices-rg --service-name northwind-apim --path products --specification-format OpenAPI --specification-url "http://$INGRESS_IP/product-catalog/swagger/swagger.json" --api-id product-catalog
   
   az apim api import --resource-group northwind-microservices-rg --service-name northwind-apim --path orders --specification-format OpenAPI --specification-url "http://$INGRESS_IP/order-service/swagger/swagger.json" --api-id order-service
   
   az apim api import --resource-group northwind-microservices-rg --service-name northwind-apim --path inventory --specification-format OpenAPI --specification-url "http://$INGRESS_IP/inventory-service/swagger/swagger.json" --api-id inventory-service
   
   az apim api import --resource-group northwind-microservices-rg --service-name northwind-apim --path customers --specification-format OpenAPI --specification-url "http://$INGRESS_IP/customer-profile/swagger/swagger.json" --api-id customer-profile
   ```

2. Configure Rate Limiting and Caching:
   ```bash
   # Apply rate limiting policy to product API
   az apim api policy set --resource-group northwind-microservices-rg --service-name northwind-apim --api-id product-catalog --policy-format xml --value @- <<EOF
   <policies>
       <inbound>
           <base />
           <rate-limit calls="5" renewal-period="10" />
           <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none">
               <vary-by-header>Accept</vary-by-header>
               <vary-by-header>Accept-Charset</vary-by-header>
           </cache-lookup>
       </inbound>
       <backend>
           <base />
       </backend>
       <outbound>
           <base />
           <cache-store duration="3600" />
       </outbound>
       <on-error>
           <base />
       </on-error>
   </policies>
   EOF
   
   # Apply JWT validation policy for order API
   az apim api policy set --resource-group northwind-microservices-rg --service-name northwind-apim --api-id order-service --policy-format xml --value @- <<EOF
   <policies>
       <inbound>
           <base />
           <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized">
               <openid-config url="https://login.microsoftonline.com/common/.well-known/openid-configuration" />
               <required-claims>
                   <claim name="aud">
                       <value>api://northwind-api</value>
                   </claim>
               </required-claims>
           </validate-jwt>
       </inbound>
       <backend>
           <base />
       </backend>
       <outbound>
           <base />
       </outbound>
       <on-error>
           <base />
       </on-error>
   </policies>
   EOF
   ```

### Part 5: Azure Front Door Integration

1. Register API Management endpoint with Front Door:
   ```bash
   # Create origin group
   az afd origin-group create --resource-group northwind-microservices-rg --profile-name northwind-frontdoor --origin-group-name api-group --probe-protocol Http --probe-path /status-0123456789abcdef --probe-interval 120 --probe-request-type HEAD
   
   # Add APIM as origin
   az afd origin create --resource-group northwind-microservices-rg --profile-name northwind-frontdoor --origin-group-name api-group --origin-name api-origin --host-name northwind-apim.azure-api.net --http-port 80 --https-port 443 --priority 1 --weight 1000 --enabled-state Enabled
   
   # Create route
   az afd route create --resource-group northwind-microservices-rg --profile-name northwind-frontdoor --endpoint-name northwind-global --route-name api-route --origin-group api-group --forwarding-protocol HttpsOnly --supported-protocols Http Https --custom-domains
   ```

### Part 6: DevOps Pipeline Setup

1. Create Azure DevOps Pipeline for Product Catalog Service:
   ```yaml
   # azure-pipelines.yaml
   trigger:
     branches:
       include:
         - main
     paths:
       include:
         - src/services/product-catalog/**
   
   variables:
     azureSubscription: 'NorthwindAzure'
     containerRegistry: 'northwindacr.azurecr.io'
     dockerfilePath: 'src/services/product-catalog/Dockerfile'
     imageRepository: 'product-catalog'
     buildContext: 'src/services/product-catalog'
     tag: '$(Build.BuildId)'
     kubernetesServiceConnection: 'NorthwindAKS'
     namespace: northwind-prod
   
   stages:
   - stage: Build
     jobs:
     - job: BuildAndPush
       pool:
         vmImage: 'ubuntu-latest'
       steps:
       - task: Docker@2
         displayName: Build and push image
         inputs:
           command: buildAndPush
           containerRegistry: $(containerRegistry)
           repository: $(imageRepository)
           dockerfile: $(dockerfilePath)
           buildContext: $(buildContext)
           tags: |
             $(tag)
             latest
       
       - task: HelmDeploy@0
         displayName: 'Helm package'
         inputs:
           command: package
           chartPath: 'deploy/helm/product-catalog'
           destination: '$(Build.ArtifactStagingDirectory)'
           updateDependency: true
   
       - publish: '$(Build.ArtifactStagingDirectory)'
         artifact: 'charts'
   
   - stage: Deploy
     dependsOn: Build
     jobs:
     - deployment: DeployToAKS
       pool:
         vmImage: 'ubuntu-latest'
       environment: 'production'
       strategy:
         runOnce:
           deploy:
             steps:
             - download: current
               artifact: 'charts'
             
             - task: HelmDeploy@0
               displayName: 'Helm upgrade'
               inputs:
                 connectionType: 'Kubernetes Service Connection'
                 kubernetesServiceConnection: $(kubernetesServiceConnection)
                 namespace: $(namespace)
                 command: upgrade
                 chartType: FilePath
                 chartPath: '$(Pipeline.Workspace)/charts/product-catalog-0.1.0.tgz'
                 releaseName: product-catalog
                 overrideValues: 'image.repository=$(containerRegistry)/$(imageRepository),image.tag=$(tag)'
                 install: true
                 waitForExecution: true
   ```

### Part 7: Monitoring and Observability

1. Configure Distributed Tracing:
   ```bash
   # Create ConfigMap for OpenTelemetry Collector
   kubectl apply -f - <<EOF
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: otel-collector-conf
     namespace: northwind-prod
   data:
     otel-collector-config: |
       receivers:
         otlp:
           protocols:
             grpc:
               endpoint: 0.0.0.0:4317
             http:
               endpoint: 0.0.0.0:4318
       
       processors:
         batch:
       
       exporters:
         azuremonitor:
           instrumentation_key: <INSTRUMENTATION_KEY>
       
       service:
         pipelines:
           traces:
             receivers: [otlp]
             processors: [batch]
             exporters: [azuremonitor]
   EOF
   
   # Deploy OpenTelemetry Collector
   kubectl apply -f - <<EOF
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: otel-collector
     namespace: northwind-prod
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: otel-collector
     template:
       metadata:
         labels:
           app: otel-collector
       spec:
         containers:
         - name: otel-collector
           image: otel/opentelemetry-collector-contrib:latest
           ports:
           - containerPort: 4317
           - containerPort: 4318
           volumeMounts:
           - name: otel-collector-config-vol
             mountPath: /conf
           args:
           - "--config=/conf/otel-collector-config"
         volumes:
         - name: otel-collector-config-vol
           configMap:
             name: otel-collector-conf
             items:
               - key: otel-collector-config
                 path: otel-collector-config
   EOF
   
   # Create Service for OpenTelemetry Collector
   kubectl apply -f - <<EOF
   apiVersion: v1
   kind: Service
   metadata:
     name: otel-collector
     namespace: northwind-prod
   spec:
     ports:
     - name: otlp-grpc
       port: 4317
       targetPort: 4317
     - name: otlp-http
       port: 4318
       targetPort: 4318
     selector:
       app: otel-collector
   EOF
   ```

2. Set up Azure Monitor Dashboard:
   ```bash
   # Deploy Azure Monitor Dashboard
   az portal dashboard create --resource-group northwind-microservices-rg --name northwind-monitoring --location eastus --public-access-enabled false
   ```

## Infrastructure as Code

The entire infrastructure is defined using Azure Bicep templates:

1. [main.bicep](deploy/main.bicep) - Main template orchestrating all components
2. [aks.bicep](deploy/aks.bicep) - AKS cluster definition
3. [db.bicep](deploy/db.bicep) - Database resources (Cosmos DB, PostgreSQL)
4. [messaging.bicep](deploy/messaging.bicep) - Message brokers (Service Bus, Event Hub)
5. [networking.bicep](deploy/networking.bicep) - Virtual Network and network security
6. [monitoring.bicep](deploy/monitoring.bicep) - Monitoring resources
7. [apim.bicep](deploy/apim.bicep) - API Management definition
8. [frontdoor.bicep](deploy/frontdoor.bicep) - Front Door configuration

Deploy the infrastructure:
```bash
az deployment group create --resource-group northwind-microservices-rg --template-file deploy/main.bicep
```

## Microservices Application Code

The application consists of multiple microservices:

1. **Product Catalog Service**:
   - Technology: .NET 6 WebAPI
   - Storage: Cosmos DB (MongoDB API)
   - Caching: Redis
   - Messaging: Publishes product events to Service Bus
   - Features: Product browsing, search, details, categorization

2. **Order Service**:
   - Technology: Java Spring Boot
   - Storage: PostgreSQL
   - Messaging: Publishes order events to Service Bus
   - Features: Order creation, status updates, history

3. **Inventory Service**:
   - Technology: Node.js Express
   - Storage: PostgreSQL
   - Messaging: Publishes/subscribes to inventory events
   - Features: Stock tracking, reservations, allocation

4. **Customer Profile Service**:
   - Technology: Python FastAPI
   - Storage: Cosmos DB (MongoDB API)
   - Features: Customer information, preferences, payment methods

5. **Notification Service**:
   - Technology: .NET 6 Functions
   - Messaging: Subscribes to various service events
   - Features: Email, push, and in-app notifications

6. **Analytics Service**:
   - Technology: Python Flask
   - Storage: Azure Synapse Analytics
   - Messaging: Consumes events from Event Hub
   - Features: Reporting, BI, trend analysis

## Monitoring and Management

1. **Distributed Tracing**:
   - OpenTelemetry instrumentation for all services
   - Integration with Application Insights
   - Correlation ID propagation across service boundaries
   - End-to-end transaction visibility

2. **Log Management**:
   - Centralized logging with Azure Monitor
   - Structured logging in JSON format
   - Log queries and alerts
   - Custom dashboards for operational monitoring

3. **Metrics**:
   - Service-level metrics (response time, throughput, error rate)
   - Business metrics (orders, revenue, user activity)
   - Resource utilization metrics (CPU, memory, I/O)
   - Custom Grafana dashboards for visualization

4. **Alerting**:
   - Service health alerts
   - Performance threshold alerts
   - Business KPI alerts
   - Automated incident response

## Security Implementation

1. **Authentication and Authorization**:
   - Azure AD for identity management
   - JWT token validation
   - OAuth 2.0 and OpenID Connect
   - Role-based access control

2. **Network Security**:
   - Private endpoints for PaaS services
   - Network security groups
   - Service mesh mTLS
   - API Management IP restrictions

3. **Data Protection**:
   - Encryption at rest and in transit
   - Data masking for sensitive information
   - Key rotation policies
   - Secure secret management with Key Vault

4. **Compliance and Governance**:
   - Azure Policy for compliance enforcement
   - Resource tagging for governance
   - RBAC for resource access
   - Audit logging for all operations

## Operational Procedures

1. **Deployment Strategy**:
   - Blue-green deployments
   - Canary releases for high-risk changes
   - Automated rollbacks
   - Feature flags for gradual rollout

2. **Scaling Procedures**:
   - Horizontal pod autoscaling
   - Vertical scaling for databases
   - Scheduled scaling for predictable loads
   - Burst handling procedures

3. **Backup and Recovery**:
   - Automated database backups
   - Point-in-time recovery
   - Cross-region replication
   - Disaster recovery testing

4. **Incident Management**:
   - Incident response procedures
   - On-call rotation
   - Post-incident reviews
   - Knowledge base maintenance

## Cost Optimization

1. **Resource Optimization**:
   - Right-sizing AKS nodes
   - Auto-scaling based on actual usage
   - Dev/test environments on lower tiers
   - Reserved Instances for predictable workloads

2. **Operational Costs**:
   - Automated scaling during off-hours
   - Consumption-based services where possible
   - Storage tiering for cold data
   - Monitoring and optimizing network egress

3. **Continuous Optimization**:
   - Regular cost reviews
   - Azure Advisor recommendations
   - Cost allocation with tagging
   - Performance/cost tradeoff analysis

## Next Steps

1. Implement GitOps with Flux or ArgoCD for deployment automation
2. Set up chaos engineering to test resilience
3. Implement multi-region deployment for global reach
4. Add A/B testing capabilities for feature experimentation
5. Implement AI-powered analytics for business insights