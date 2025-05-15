# Serverless Application Project

## Architecture Overview

This project implements a modern event-driven serverless application in Azure, consisting of:

1. **Azure Functions**: For backend processing, event handling, and API endpoints
2. **Azure Static Web Apps**: For hosting the frontend application with integrated Azure Functions API
3. **Azure Event Grid**: For event routing between components
4. **Azure Cosmos DB**: For scalable, globally distributed data storage
5. **Azure Blob Storage**: For file storage and event triggering
6. **Azure Logic Apps**: For workflow orchestration and external service integration
7. **Azure API Management**: For API management, security, and analytics

Supporting components include:
- Azure Key Vault for secret management
- Application Insights for monitoring
- Azure Cognitive Services for intelligent features
- Azure Service Bus for reliable message processing
- Azure Event Hubs for high-throughput event ingestion

![Architecture Diagram](architecture.png)

## Business Requirements

A fictional media company, Contoso Media, needs a scalable content management and processing platform with the following requirements:

1. Automated processing of uploaded media (images, videos, documents)
2. Content analysis for automatic tagging and categorization
3. API access for third-party integration
4. Real-time notification when content is processed
5. Pay-per-use cost model with minimal infrastructure management
6. Global content delivery with low latency
7. Secure access control with role-based permissions
8. Ability to handle unpredictable traffic spikes during major events

## Implementation Guide

### Part 1: Resource Setup

1. Create Resource Group:
   ```bash
   az group create --name contoso-media-serverless-rg --location eastus
   ```

2. Create Storage Account for media uploads and function app:
   ```bash
   az storage account create --name contosomediasa --resource-group contoso-media-serverless-rg --location eastus --sku Standard_LRS --kind StorageV2 --enable-hierarchical-namespace false
   
   # Create containers for media files
   az storage container create --name uploads --account-name contosomediasa --public-access blob
   az storage container create --name processed --account-name contosomediasa --public-access blob
   ```

3. Create Cosmos DB account and database:
   ```bash
   az cosmosdb create --name contoso-media-cosmos --resource-group contoso-media-serverless-rg --kind GlobalDocumentDB --default-consistency-level Session
   
   # Create database
   az cosmosdb sql database create --account-name contoso-media-cosmos --resource-group contoso-media-serverless-rg --name MediaContent
   
   # Create containers
   az cosmosdb sql container create --account-name contoso-media-cosmos --database-name MediaContent --resource-group contoso-media-serverless-rg --name Metadata --partition-key-path "/contentId" --throughput 400
   az cosmosdb sql container create --account-name contoso-media-cosmos --database-name MediaContent --resource-group contoso-media-serverless-rg --name Workflows --partition-key-path "/workflowId" --throughput 400
   az cosmosdb sql container create --account-name contoso-media-cosmos --database-name MediaContent --resource-group contoso-media-serverless-rg --name Users --partition-key-path "/userId" --throughput 400
   ```

4. Create Key Vault:
   ```bash
   az keyvault create --name contoso-media-kv --resource-group contoso-media-serverless-rg --location eastus --sku standard
   ```

5. Create Service Bus for reliable message processing:
   ```bash
   az servicebus namespace create --name contoso-media-sb --resource-group contoso-media-serverless-rg --location eastus --sku Standard
   
   # Create queues and topics
   az servicebus queue create --name media-processing-queue --namespace-name contoso-media-sb --resource-group contoso-media-serverless-rg --max-delivery-count 10
   az servicebus topic create --name media-events --namespace-name contoso-media-sb --resource-group contoso-media-serverless-rg
   
   # Create subscriptions
   az servicebus topic subscription create --name notification-subscription --namespace-name contoso-media-sb --resource-group contoso-media-serverless-rg --topic-name media-events
   az servicebus topic subscription create --name analytics-subscription --namespace-name contoso-media-sb --resource-group contoso-media-serverless-rg --topic-name media-events
   ```

6. Create Event Grid Topic:
   ```bash
   az eventgrid topic create --name contoso-media-events --resource-group contoso-media-serverless-rg --location eastus
   ```

7. Create Cognitive Services:
   ```bash
   # Create Computer Vision for image analysis
   az cognitiveservices account create --name contoso-vision --resource-group contoso-media-serverless-rg --kind ComputerVision --sku S1 --location eastus
   
   # Create Content Moderator for content review
   az cognitiveservices account create --name contoso-moderator --resource-group contoso-media-serverless-rg --kind ContentModerator --sku S0 --location eastus
   ```

8. Create Application Insights:
   ```bash
   az monitor app-insights component create --app contoso-media-insights --resource-group contoso-media-serverless-rg --location eastus --application-type web
   ```

### Part 2: Azure Functions Implementation

1. Create Function App:
   ```bash
   # Create consumption plan
   az functionapp create \
     --name contoso-media-functions \
     --resource-group contoso-media-serverless-rg \
     --storage-account contosomediasa \
     --consumption-plan-location eastus \
     --runtime dotnet \
     --functions-version 4 \
     --app-insights contoso-media-insights
   ```

2. Create managed identity for the Function App and grant access to resources:
   ```bash
   # Enable managed identity
   az functionapp identity assign --name contoso-media-functions --resource-group contoso-media-serverless-rg
   
   # Get the principal ID
   FUNCTION_PRINCIPAL_ID=$(az functionapp identity show --name contoso-media-functions --resource-group contoso-media-serverless-rg --query principalId --output tsv)
   
   # Grant access to Key Vault
   az keyvault set-policy --name contoso-media-kv --object-id $FUNCTION_PRINCIPAL_ID --secret-permissions get list
   
   # Store secrets in Key Vault
   COSMOS_CONNECTION=$(az cosmosdb keys list --name contoso-media-cosmos --resource-group contoso-media-serverless-rg --type connection-strings --query connectionStrings[0].connectionString --output tsv)
   az keyvault secret set --vault-name contoso-media-kv --name CosmosDBConnection --value "$COSMOS_CONNECTION"
   
   SERVICE_BUS_CONNECTION=$(az servicebus namespace authorization-rule keys list --namespace-name contoso-media-sb --resource-group contoso-media-serverless-rg --name RootManageSharedAccessKey --query primaryConnectionString --output tsv)
   az keyvault secret set --vault-name contoso-media-kv --name ServiceBusConnection --value "$SERVICE_BUS_CONNECTION"
   
   COMPUTER_VISION_KEY=$(az cognitiveservices account keys list --name contoso-vision --resource-group contoso-media-serverless-rg --query key1 --output tsv)
   az keyvault secret set --vault-name contoso-media-kv --name ComputerVisionKey --value "$COMPUTER_VISION_KEY"
   
   CONTENT_MODERATOR_KEY=$(az cognitiveservices account keys list --name contoso-moderator --resource-group contoso-media-serverless-rg --query key1 --output tsv)
   az keyvault secret set --vault-name contoso-media-kv --name ContentModeratorKey --value "$CONTENT_MODERATOR_KEY"
   ```

3. Configure Function App settings:
   ```bash
   az functionapp config appsettings set --name contoso-media-functions --resource-group contoso-media-serverless-rg --settings \
     "KeyVaultName=contoso-media-kv" \
     "StorageConnection__accountName=contosomediasa" \
     "ComputerVisionEndpoint=$(az cognitiveservices account show --name contoso-vision --resource-group contoso-media-serverless-rg --query properties.endpoint --output tsv)" \
     "ContentModeratorEndpoint=$(az cognitiveservices account show --name contoso-moderator --resource-group contoso-media-serverless-rg --query properties.endpoint --output tsv)"
   ```

4. Create Function implementations:

   a. Blob Storage Trigger Function (ProcessUploadedMedia):
   ```csharp
   [FunctionName("ProcessUploadedMedia")]
   public static async Task Run(
       [BlobTrigger("uploads/{name}", Connection = "StorageConnection")] Stream inputBlob,
       [Blob("processed/{name}", FileAccess.Write, Connection = "StorageConnection")] Stream outputBlob,
       [ServiceBus("media-processing-queue", Connection = "ServiceBusConnection")] IAsyncCollector<MediaProcessingMessage> outputMessages,
       string name,
       ILogger log)
   {
       log.LogInformation($"Processing blob\n Name: {name} \n Size: {inputBlob.Length} bytes");
       
       // Copy to processed container
       await inputBlob.CopyToAsync(outputBlob);
       
       // Send message to queue for further processing
       await outputMessages.AddAsync(new MediaProcessingMessage 
       { 
           BlobName = name, 
           ContentType = Path.GetExtension(name).ToLowerInvariant(), 
           UploadTime = DateTime.UtcNow 
       });
   }
   ```

   b. Service Bus Queue Trigger Function (AnalyzeMediaContent):
   ```csharp
   [FunctionName("AnalyzeMediaContent")]
   public static async Task Run(
       [ServiceBusTrigger("media-processing-queue", Connection = "ServiceBusConnection")] MediaProcessingMessage message,
       [Blob("processed/{BlobName}", FileAccess.Read, Connection = "StorageConnection")] Stream mediaBlob,
       [CosmosDB("MediaContent", "Metadata", Connection = "CosmosDBConnection", CreateIfNotExists = true, PartitionKey = "{contentId}")] IAsyncCollector<dynamic> metadataOutput,
       [ServiceBus("media-events", Connection = "ServiceBusConnection")] IAsyncCollector<MediaAnalyzedEvent> outputEvents,
       ILogger log)
   {
       log.LogInformation($"Analyzing media content: {message.BlobName}");
       
       // Analyze content based on type
       ContentAnalysisResult analysis;
       if (IsImage(message.ContentType))
       {
           analysis = await AnalyzeImage(mediaBlob);
       }
       else if (IsVideo(message.ContentType))
       {
           analysis = await AnalyzeVideo(mediaBlob);
       }
       else if (IsDocument(message.ContentType))
       {
           analysis = await AnalyzeDocument(mediaBlob);
       }
       else
       {
           analysis = new ContentAnalysisResult
           {
               ContentId = Guid.NewGuid().ToString(),
               Tags = new[] { "unknown" },
               IsApproved = true
           };
       }
       
       // Store metadata in Cosmos DB
       await metadataOutput.AddAsync(new
       {
           id = analysis.ContentId,
           contentId = analysis.ContentId,
           blobName = message.BlobName,
           contentType = message.ContentType,
           tags = analysis.Tags,
           isApproved = analysis.IsApproved,
           uploadTime = message.UploadTime,
           processingTime = DateTime.UtcNow,
           analysisData = analysis.AnalysisData
       });
       
       // Publish event
       await outputEvents.AddAsync(new MediaAnalyzedEvent
       {
           ContentId = analysis.ContentId,
           BlobName = message.BlobName,
           Tags = analysis.Tags,
           IsApproved = analysis.IsApproved,
           ProcessingTime = DateTime.UtcNow
       });
   }
   
   private static async Task<ContentAnalysisResult> AnalyzeImage(Stream imageStream)
   {
       // Implementation using Computer Vision API
       // ...
   }
   ```

   c. HTTP Trigger Function (GetContentMetadata):
   ```csharp
   [FunctionName("GetContentMetadata")]
   public static async Task<IActionResult> Run(
       [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = "content/{contentId}")] HttpRequest req,
       [CosmosDB("MediaContent", "Metadata", Connection = "CosmosDBConnection", Id = "{contentId}", PartitionKey = "{contentId}")] dynamic contentMetadata,
       string contentId,
       ILogger log)
   {
       log.LogInformation($"Retrieving metadata for content: {contentId}");
       
       if (contentMetadata == null)
       {
           return new NotFoundResult();
       }
       
       return new OkObjectResult(contentMetadata);
   }
   ```

   d. Timer Trigger Function (CleanupOldContent):
   ```csharp
   [FunctionName("CleanupOldContent")]
   public static async Task Run(
       [TimerTrigger("0 0 0 * * *")] TimerInfo timer, // Run daily at midnight
       [CosmosDB("MediaContent", "Metadata", Connection = "CosmosDBConnection")] CosmosClient cosmosClient,
       [Blob("processed", Connection = "StorageConnection")] BlobContainerClient blobContainerClient,
       ILogger log)
   {
       log.LogInformation($"Cleanup function executed at: {DateTime.Now}");
       
       // Get date 30 days ago
       var cutoffDate = DateTime.UtcNow.AddDays(-30);
       
       // Find old content in Cosmos DB
       var container = cosmosClient.GetDatabase("MediaContent").GetContainer("Metadata");
       var query = new QueryDefinition("SELECT * FROM c WHERE c.uploadTime < @cutoffDate")
           .WithParameter("@cutoffDate", cutoffDate.ToString("o"));
       
       var iterator = container.GetItemQueryIterator<dynamic>(query);
       
       var deletedCount = 0;
       while (iterator.HasMoreResults)
       {
           var results = await iterator.ReadNextAsync();
           foreach (var item in results)
           {
               // Delete blob
               await blobContainerClient.DeleteBlobIfExistsAsync(item.blobName.ToString());
               
               // Delete metadata
               await container.DeleteItemAsync<dynamic>(item.id.ToString(), new PartitionKey(item.contentId.ToString()));
               
               deletedCount++;
           }
       }
       
       log.LogInformation($"Deleted {deletedCount} old content items");
   }
   ```

### Part 3: Logic App Implementation

1. Create Logic App for notifications:
   ```bash
   az logic-app create --name contoso-media-notifications --resource-group contoso-media-serverless-rg --location eastus
   ```

2. Configure Logic App workflow (using Azure Portal):

   a. Trigger: When a new event is received (Service Bus subscription)
   b. Parse JSON: Parse the event data
   c. Condition: Check if the content is approved
   d. If approved:
      - Get metadata from Cosmos DB
      - Send email notification
      - Post message to Teams channel
   e. If not approved:
      - Add message to review queue
      - Send alert to content moderation team

### Part 4: Static Web App Implementation

1. Create Static Web App:
   ```bash
   az staticwebapp create --name contoso-media-portal --resource-group contoso-media-serverless-rg --location eastus --sku Standard
   ```

2. Deploy web application from GitHub:
   
   a. Create GitHub repository for the web app
   b. Implement React/Angular/Vue application
   c. Set up authentication and authorization
   d. Implement content management interface
   e. Configure CI/CD workflow

### Part 5: API Management Integration

1. Create API Management instance:
   ```bash
   az apim create --name contoso-media-apim --resource-group contoso-media-serverless-rg --publisher-name "Contoso Media" --publisher-email "admin@contosomedia.com" --no-wait --sku-name Consumption
   ```

2. Import Function App API:
   ```bash
   # Get function app host key
   FUNCTION_KEY=$(az functionapp keys list --name contoso-media-functions --resource-group contoso-media-serverless-rg --query functionKeys.default --output tsv)
   
   # Import API
   az apim api import --resource-group contoso-media-serverless-rg --service-name contoso-media-apim --path media --api-id media --specification-url "https://contoso-media-functions.azurewebsites.net/api/swagger.json?code=$FUNCTION_KEY" --specification-format OpenApi
   ```

3. Configure API policies:

   a. Rate limiting policy:
   ```xml
   <policies>
     <inbound>
       <rate-limit calls="5" renewal-period="60" />
       <quota calls="100" renewal-period="86400" />
     </inbound>
   </policies>
   ```

   b. JWT validation policy:
   ```xml
   <policies>
     <inbound>
       <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized">
         <openid-config url="https://login.microsoftonline.com/common/.well-known/openid-configuration" />
         <audiences>
           <audience>audience-value</audience>
         </audiences>
       </validate-jwt>
     </inbound>
   </policies>
   ```

### Part 6: Event Grid Integration

1. Create Event Grid subscription for blob events:
   ```bash
   # Get storage account resource ID
   STORAGE_ID=$(az storage account show --name contosomediasa --resource-group contoso-media-serverless-rg --query id --output tsv)
   
   # Create Event Grid subscription
   az eventgrid event-subscription create \
     --name BlobCreatedSubscription \
     --source-resource-id $STORAGE_ID \
     --endpoint-type azurefunction \
     --endpoint $(az functionapp function show --name contoso-media-functions --resource-group contoso-media-serverless-rg --function-name ProcessUploadedMediaViaEventGrid --query id --output tsv) \
     --included-event-types Microsoft.Storage.BlobCreated \
     --subject-begins-with /blobServices/default/containers/uploads
   ```

2. Implement EventGrid trigger function:
   ```csharp
   [FunctionName("ProcessUploadedMediaViaEventGrid")]
   public static async Task Run(
       [EventGridTrigger] EventGridEvent eventGridEvent,
       [Blob("{data.url}", FileAccess.Read)] Stream inputBlob,
       [ServiceBus("media-processing-queue", Connection = "ServiceBusConnection")] IAsyncCollector<MediaProcessingMessage> outputMessages,
       ILogger log)
   {
       log.LogInformation($"Event Grid trigger function processed an event: {eventGridEvent.EventType}");
       
       var storageEvent = JsonConvert.DeserializeObject<StorageBlobCreatedEventData>(eventGridEvent.Data.ToString());
       string blobName = GetBlobNameFromUrl(storageEvent.Url);
       
       // Send message to queue for further processing
       await outputMessages.AddAsync(new MediaProcessingMessage 
       { 
           BlobName = blobName, 
           ContentType = Path.GetExtension(blobName).ToLowerInvariant(), 
           UploadTime = eventGridEvent.EventTime 
       });
   }
   ```

### Part 7: Security Implementation

1. Configure CORS for the Function App:
   ```bash
   az functionapp cors add --name contoso-media-functions --resource-group contoso-media-serverless-rg --allowed-origins "https://contoso-media-portal.azurestaticapps.net"
   ```

2. Set up authentication for Static Web App:
   ```bash
   az staticwebapp auth update --name contoso-media-portal --resource-group contoso-media-serverless-rg --providers github
   ```

3. Implement role-based authorization:
   ```json
   {
     "routes": [
       {
         "route": "/api/content/*",
         "allowedRoles": ["authenticated"]
       },
       {
         "route": "/api/admin/*",
         "allowedRoles": ["administrator"]
       },
       {
         "route": "/*",
         "allowedRoles": ["anonymous", "authenticated"]
       }
     ]
   }
   ```

4. Configure Private Endpoints for sensitive services:
   ```bash
   # Create virtual network for private endpoints
   az network vnet create --name contoso-media-vnet --resource-group contoso-media-serverless-rg --location eastus --address-prefix 10.0.0.0/16
   
   az network vnet subnet create --name endpoint-subnet --resource-group contoso-media-serverless-rg --vnet-name contoso-media-vnet --address-prefix 10.0.0.0/24 --disable-private-endpoint-network-policies true
   
   # Create private endpoint for Cosmos DB
   az network private-endpoint create \
     --name cosmos-private-endpoint \
     --resource-group contoso-media-serverless-rg \
     --vnet-name contoso-media-vnet \
     --subnet endpoint-subnet \
     --private-connection-resource-id $(az cosmosdb show --name contoso-media-cosmos --resource-group contoso-media-serverless-rg --query id -o tsv) \
     --group-id Sql
   ```

### Part 8: Monitoring Implementation

1. Set up Application Insights dashboards:
   ```bash
   az portal dashboard create --name contoso-media-dashboard --resource-group contoso-media-serverless-rg --location eastus
   ```

2. Create monitoring alerts:
   ```bash
   # Create action group
   az monitor action-group create --name contoso-media-alerts --resource-group contoso-media-serverless-rg --short-name cma --email-receiver name=admin email=admin@contosomedia.com
   
   # Create alert for function errors
   az monitor metrics alert create \
     --name "Function Errors" \
     --resource-group contoso-media-serverless-rg \
     --scopes $(az functionapp show --name contoso-media-functions --resource-group contoso-media-serverless-rg --query id -o tsv) \
     --condition "count FunctionExecutionCount > 10 where (Status contains 'Failed')" \
     --window-size 5m \
     --evaluation-frequency 1m \
     --action $(az monitor action-group show --name contoso-media-alerts --resource-group contoso-media-serverless-rg --query id -o tsv)
   ```

## Infrastructure as Code

The entire infrastructure can be deployed using ARM templates or Bicep. The key template files include:

1. [main.bicep](deploy/main.bicep) - Main deployment template
2. [functions.bicep](deploy/functions.bicep) - Function App resources
3. [storage.bicep](deploy/storage.bicep) - Storage resources
4. [cosmos.bicep](deploy/cosmos.bicep) - Cosmos DB resources
5. [messaging.bicep](deploy/messaging.bicep) - Service Bus and Event Grid resources
6. [cognitive.bicep](deploy/cognitive.bicep) - Cognitive Services resources
7. [monitoring.bicep](deploy/monitoring.bicep) - Monitoring resources

Deploy the infrastructure:
```bash
az deployment group create --resource-group contoso-media-serverless-rg --template-file deploy/main.bicep --parameters environment=production
```

## Monitoring and Management

1. **Application Monitoring**:
   - Use Application Insights live metrics to monitor function execution
   - Set up custom dashboards for key performance indicators
   - Configure smart detection to identify anomalies
   - Implement correlation IDs for end-to-end request tracking

2. **Cost Monitoring**:
   - Set up Azure Cost Management for resource usage tracking
   - Create budget alerts for spending thresholds
   - Analyze cost by resource type and time period
   - Implement tagged resources for cost allocation

3. **Performance Monitoring**:
   - Monitor function execution time and memory usage
   - Track Cosmos DB request units consumption
   - Monitor Service Bus message throughput
   - Analyze API response times

4. **Error Handling**:
   - Implement robust error handling in functions
   - Configure dead-letter queues for failed messages
   - Set up alerts for high error rates
   - Create automated workflows for common error scenarios

## Cost Optimization

1. **Consumption-Based Resources**:
   - Use consumption plan for Function Apps to pay only for execution time
   - Implement autoscale for Cosmos DB
   - Configure Service Bus auto-inflate
   - Use proper storage tiers for hot and cool data

2. **Function Optimization**:
   - Optimize function execution time to reduce costs
   - Use durable functions for complex workflows
   - Implement efficient data access patterns
   - Leverage Azure CDN for static content delivery

3. **Storage Optimization**:
   - Implement data lifecycle management policies
   - Use blob tiers based on access frequency
   - Optimize data access patterns to reduce I/O operations
   - Compress data when appropriate

## Security Considerations

1. **Data Protection**:
   - Enable encryption at rest for all services
   - Implement HTTPS for all communications
   - Use private endpoints for sensitive services
   - Implement data backup and disaster recovery

2. **Authentication and Authorization**:
   - Use Azure AD for authentication
   - Implement role-based access control
   - Secure APIs with JWT validation
   - Use managed identities for service-to-service authentication

3. **Network Security**:
   - Implement IP restrictions for function apps
   - Use Virtual Network integration where possible
   - Configure service endpoints for PaaS services
   - Monitor and log all network traffic

4. **Secret Management**:
   - Store all secrets in Azure Key Vault
   - Use managed identities to access Key Vault
   - Implement secret rotation policies
   - Monitor and audit secret access

## Next Steps

1. Implement CI/CD with GitHub Actions or Azure DevOps
2. Add advanced analytics with Azure Synapse Analytics
3. Implement A/B testing for UI features
4. Add multi-region deployment for global availability
5. Implement advanced content analysis with custom AI models