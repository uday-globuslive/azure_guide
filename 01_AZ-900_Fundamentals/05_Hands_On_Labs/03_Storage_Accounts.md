# Hands-On Lab: Working with Azure Storage Accounts

## Objective
Create an Azure Storage account and explore its various services including Blob storage, File storage, Table storage, and Queue storage.

## Prerequisites
- An Azure account with an active subscription
- Basic familiarity with the Azure portal
- Azure Storage Explorer installed locally (optional)

## Estimated Time
45-60 minutes

## Steps

### 1. Create a Resource Group

1. In the Azure portal, search for "Resource groups" in the top search bar
2. Click "+ Create" to create a new resource group
3. Enter the following information:
   - Subscription: Select your subscription
   - Resource group: Enter "StorageLabRG"
   - Region: Select a region close to your location
4. Click "Review + create" and then "Create" after validation passes

### 2. Create a Storage Account

1. In the Azure portal, search for "Storage accounts" in the top search bar
2. Click "+ Create" to create a new storage account
3. Enter the following information:
   - Subscription: Select your subscription
   - Resource group: Select "StorageLabRG"
   - Storage account name: Enter a globally unique name (lowercase letters and numbers only)
   - Location: Use the same region as your resource group
   - Performance: Standard
   - Account kind: StorageV2 (general purpose v2)
   - Replication: Locally-redundant storage (LRS)
   - Access tier: Hot
4. Click "Review + create" and then "Create" after validation passes

### 3. Explore Blob Storage

1. Once deployment is complete, click "Go to resource"
2. In the left navigation, under "Data storage", click "Containers"
3. Click "+ Container" to create a new container
4. Enter a name for your container (e.g., "documents") and set the Public access level to "Private"
5. Click "Create"
6. Click on the newly created container
7. Click "Upload" to upload a file from your local machine
8. Select a file (such as a text file or image) and click "Upload"
9. After upload, click on the file to view its properties
10. Explore options like generating a SAS token, downloading, and viewing metadata

### 4. Work with Azure Files

1. Go back to your storage account overview
2. In the left navigation, under "Data storage", click "File shares"
3. Click "+ File share" to create a new file share
4. Enter a name for your file share (e.g., "myfileshare") and set the Quota to 1 GiB
5. Click "Create"
6. Click on the newly created file share
7. Click "Upload" to upload a file from your local machine
8. Select a file and click "Upload"
9. Explore options for creating directories and managing files
10. Click "Connect" to see how to mount the file share on different operating systems

### 5. Explore Table Storage

1. Go back to your storage account overview
2. In the left navigation, under "Data storage", click "Tables"
3. Click "+ Table" to create a new table
4. Enter a name for your table (e.g., "customers") and click "OK"
5. To work with table data, you need to use the Azure Storage Explorer or code
6. (Optional) Open Azure Storage Explorer on your local machine
7. Connect to your Azure account and navigate to your storage account
8. Find your table and add entities by right-clicking and selecting "Add Entity"
9. Add some properties and values to demonstrate the schema-less nature of Table storage

### 6. Work with Queue Storage

1. Go back to your storage account overview
2. In the left navigation, under "Data storage", click "Queues"
3. Click "+ Queue" to create a new queue
4. Enter a name for your queue (e.g., "messages") and click "OK"
5. Click on the newly created queue
6. Click "+ Add message" to add a new message
7. Enter a message text and set the TTL (Time-to-live) to 7 days
8. Click "OK" to add the message
9. The message now appears in your queue, ready to be processed by an application

### 7. Configure Storage Account Settings

1. Go back to your storage account overview
2. Explore the following settings in the left navigation:
   - Under "Security + networking":
     - Click "Networking" and explore the options for restricting access
     - Click "Encryption" to see the encryption options
   - Under "Data management":
     - Click "Lifecycle management" to see how you can automate moving data between tiers
     - Click "Object replication" to learn about replicating data between storage accounts

### 8. Monitor Storage Usage

1. In the left navigation, under "Monitoring", click "Insights"
2. Explore the available metrics for your storage account
3. Click on different tabs to view Failures, Performance, Availability, and Capacity metrics
4. Try changing the time range and granularity of the data

## Clean Up Resources

When you're done experimenting, you can delete the resource group to clean up all resources:

1. Navigate to "Resource groups" in the Azure portal
2. Select "StorageLabRG"
3. Click "Delete resource group"
4. Enter the resource group name to confirm deletion
5. Click "Delete"

## Key Takeaways

- You've learned how to create and configure an Azure Storage account
- You've explored the four main storage services: Blob, File, Table, and Queue
- You've uploaded and managed files in Blob storage and File shares
- You've created and used tables and queues for different storage scenarios
- You've explored security, networking, and monitoring options for storage accounts

## Next Steps

- Explore more advanced features like static website hosting
- Learn about different access tiers for optimizing storage costs
- Set up lifecycle management policies to automatically move or delete data
- Implement Azure CDN with Blob storage for faster content delivery
- Use Azure Storage with Azure Functions or Logic Apps