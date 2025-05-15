# Hands-On Lab: Managing Azure Storage

## Objective
Configure and use various Azure storage services including blob storage, file shares, and storage security features.

## Prerequisites
- An Azure account with an active subscription
- Azure Storage Explorer installed on your local machine
- Azure PowerShell or Azure CLI installed
- Basic understanding of storage concepts

## Estimated Time
1.5-2 hours

## Steps

### Part 1: Create and Configure Storage Accounts

1. Sign in to the Azure Portal:
   ```
   https://portal.azure.com
   ```

2. Create a resource group for storage resources:
   - Name: `StorageLabRG`
   - Region: Select a region suitable for your location

3. Create a general-purpose v2 storage account:
   - Name: Enter a globally unique name (e.g., `storagelabXXXXX` where XXXXX is a random number)
   - Performance: Standard
   - Account kind: StorageV2 (general purpose v2)
   - Replication: Geo-redundant storage (GRS)
   - Access tier: Hot
   - Resource group: `StorageLabRG`
   - Location: Same as resource group

4. Once the storage account is created, explore the settings:
   - Go to "Configuration" in the left menu
   - Change the "Blob access tier" from Hot to Cool
   - Enable "Hierarchical namespace" for Data Lake Storage Gen2 capabilities
   - Save the changes

5. Create a second storage account with different settings:
   - Name: Enter a globally unique name (e.g., `premiumblobXXXXX` where XXXXX is a random number)
   - Performance: Premium
   - Account kind: BlockBlobStorage
   - Replication: Locally-redundant storage (LRS)
   - Resource group: `StorageLabRG`
   - Location: Same as resource group

### Part 2: Work with Blob Storage

1. In the first storage account, navigate to "Containers" under "Data storage"

2. Create a container for public blobs:
   - Name: `public-blobs`
   - Public access level: Blob (anonymous read access for blobs only)

3. Create a container for private blobs:
   - Name: `private-blobs`
   - Public access level: Private (no anonymous access)

4. Upload files to the containers:
   - In the `public-blobs` container, click "Upload" 
   - Upload a sample image file
   - In the `private-blobs` container, upload a text file

5. Access and test the blob URLs:
   - Click on the image file in the `public-blobs` container
   - Copy the URL and paste it in a new browser tab
   - The image should be accessible
   - Repeat for the file in the `private-blobs` container
   - You should receive an error since it's not publicly accessible

6. Create a Shared Access Signature (SAS) for the private blob:
   - Select the file in the `private-blobs` container
   - Click on "Generate SAS"
   - Set permissions: Read
   - Set expiry time: 1 hour from now
   - Click "Generate SAS token and URL"
   - Copy the Blob SAS URL
   - Paste the URL in a new browser tab
   - The file should now be accessible via the SAS URL

### Part 3: Configure Blob Storage Lifecycle Management

1. In the first storage account, navigate to "Lifecycle Management" under "Data management"

2. Create a new lifecycle rule:
   - Rule name: `Archive-Old-Blobs`
   - Rule scope: Apply to all blobs in the storage account
   - Add a condition:
     - This rule applies to base blobs
     - If the base blob was last modified more than 30 days ago
     - Then move to "cool" tier
     - If the base blob was last modified more than 90 days ago
     - Then move to "archive" tier
   - Save the rule

3. Create another lifecycle rule:
   - Rule name: `Delete-Very-Old-Blobs`
   - Rule scope: Apply rule only to blobs with prefix "temp/"
   - Add a condition:
     - This rule applies to base blobs
     - If the base blob was last modified more than 365 days ago
     - Then delete the blob
   - Save the rule

### Part 4: Work with Azure Files

1. In the first storage account, navigate to "File shares" under "Data storage"

2. Create a file share:
   - Name: `documents`
   - Quota: 5 GiB
   - Click "Create"

3. Upload files to the file share:
   - Click on the `documents` file share
   - Click "Upload" and select a document file to upload
   - Create a new directory called "reports"
   - Navigate into the "reports" directory
   - Upload another file

4. Create another file share:
   - Name: `applications`
   - Quota: 10 GiB
   - Click "Create"

5. Connect to the file share from your local machine:
   - Click on the `documents` file share
   - Click "Connect" at the top of the file share page
   - Select the appropriate OS (Windows, Linux, or macOS)
   - Copy the connection script
   - Open a PowerShell or terminal window on your local machine
   - Paste and run the script

6. Use the mounted file share:
   - Navigate to the mounted drive
   - Create a new file
   - Edit an existing file
   - Verify changes appear in the Azure portal

### Part 5: Configure Storage Security

1. Configure network access:
   - Navigate to "Networking" under "Security + networking"
   - Change "Public network access" to "Enabled from selected virtual networks and IP addresses"
   - Add your client IP address
   - Save the changes

2. Configure encryption:
   - Navigate to "Encryption" under "Security + networking"
   - Review the default encryption settings
   - If available in your subscription, enable customer-managed keys
   - Configure the key vault and key settings

3. Enable Azure Defender for Storage:
   - Navigate to "Microsoft Defender for Cloud" in the Azure portal
   - Go to "Environment settings"
   - Select your subscription and the storage account
   - Enable "Microsoft Defender for Storage"

4. Configure CORS (Cross-Origin Resource Sharing):
   - Navigate to the storage account
   - Click on "Resource sharing (CORS)" under "Settings"
   - Add a new CORS rule for Blob service:
     - Allowed origins: `https://example.com` (or your actual domain)
     - Allowed methods: SELECT, GET
     - Allowed headers: *
     - Exposed headers: *
     - Max age: 200
   - Save the rule

### Part 6: Use Azure Storage Explorer

1. Open Azure Storage Explorer on your local computer

2. Connect to your Azure account:
   - Click on the "Connect" icon (plug symbol)
   - Select "Add an Azure Account"
   - Sign in with your Azure credentials
   - Select your subscription

3. Browse storage accounts:
   - Navigate to your subscription
   - Expand "Storage Accounts"
   - Find the storage accounts you created

4. Manage blob containers:
   - Navigate to the blob containers
   - Upload additional files
   - Download files
   - Generate SAS tokens
   - Change access policies

5. Manage file shares:
   - Navigate to the file shares
   - Upload and download files
   - Create directories
   - Edit file properties

### Part 7: Use AzCopy

1. Download and install AzCopy (if not already installed):
   - Windows: Download from Microsoft's website
   - Linux/macOS: Use package manager or download

2. Generate a SAS token for the storage account:
   - Navigate to the storage account
   - Click on "Shared access signature" under "Settings"
   - Set permissions for all services
   - Set expiry time to 1 day from now
   - Generate SAS and connection string
   - Copy the SAS token (without the ?)

3. Use AzCopy to copy files to a blob container:
   - Open a command prompt or terminal
   - Create a local directory with sample files to upload
   - Use AzCopy to upload the files:
     ```
     azcopy copy "local-directory/*" "https://<storage-account-name>.blob.core.windows.net/public-blobs?<SAS-token>" --recursive
     ```

4. Use AzCopy to sync a local directory with a file share:
   - Use the appropriate SAS token for file share
   - Run the sync command:
     ```
     azcopy sync "local-directory" "https://<storage-account-name>.file.core.windows.net/documents?<SAS-token>" --recursive
     ```

5. Use AzCopy to copy between storage accounts:
   - Generate SAS tokens for both storage accounts
   - Run the copy command:
     ```
     azcopy copy "https://<source-storage>.blob.core.windows.net/public-blobs?<SAS-token>" "https://<destination-storage>.blob.core.windows.net/public-blobs-copy?<SAS-token>" --recursive
     ```

### Part 8: Configure Azure Storage Monitoring

1. Configure diagnostics settings:
   - Navigate to the storage account
   - Click on "Diagnostic settings (classic)" under "Monitoring"
   - Create a new diagnostic setting:
     - Name: `StorageDiagnostics`
     - Logs: Select all log categories
     - Metrics: Select all metrics
     - Destination: Send to Log Analytics workspace (create a new one if needed)
   - Save the settings

2. Set up alerts:
   - Navigate to "Alerts" under "Monitoring"
   - Click "Create" and then "Alert rule"
   - Select the storage account as the resource
   - Configure a signal:
     - Signal type: Metrics
     - Signal name: Transactions
   - Set condition:
     - Operator: Greater than
     - Threshold: 1000
     - Aggregation type: Total
     - Aggregation granularity: 5 minutes
   - Create an action group:
     - Action group name: `StorageAlertGroup`
     - Add an email action
   - Set alert details:
     - Alert rule name: `HighTransactionAlert`
     - Description: Alert for high transaction count
   - Create the alert rule

3. Explore metrics:
   - Navigate to "Metrics" under "Monitoring"
   - Add metrics to chart:
     - Transactions
     - Success E2E Latency
     - Availability
   - Adjust the time range and granularity
   - Save the chart to a dashboard

## Clean Up Resources

When you're done experimenting, delete the resource group to avoid incurring charges:

1. Navigate to Resource Groups in the Azure portal
2. Select the `StorageLabRG` resource group
3. Click "Delete resource group"
4. Enter the resource group name to confirm deletion
5. Click "Delete"

## Key Takeaways

- You've learned how to create and configure different types of storage accounts
- You've worked with blob storage, including containers, access levels, and SAS tokens
- You've implemented lifecycle management for automatic tiering and deletion
- You've created and connected to Azure Files shares
- You've configured security features for Azure Storage
- You've used Azure Storage Explorer and AzCopy for management and data transfer
- You've set up monitoring and alerts for storage resources

## Next Steps

- Implement Azure Backup for your storage accounts
- Set up static website hosting in blob storage
- Configure a Content Delivery Network (CDN) with blob storage
- Implement object replication between storage accounts
- Configure Private Endpoints for secure access to storage
- Explore Azure Data Lake Storage Gen2 features with hierarchical namespace
- Implement Azure Files Sync with on-premises servers