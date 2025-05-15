# Hands-On Lab: Implementing Azure Storage with CDN

## Objective
Set up an Azure Storage account for static website hosting and configure Azure Content Delivery Network (CDN) to improve content delivery performance globally.

## Prerequisites
- An Azure account with an active subscription
- Basic understanding of web content
- Familiarity with Azure portal

## Estimated Time
45-60 minutes

## Steps

### Part 1: Create a Storage Account for Static Website Hosting

1. Sign in to the Azure Portal:
   ```
   https://portal.azure.com
   ```

2. Create a new resource group:
   - Click on "Resource groups" in the left navigation or search for it
   - Click "+ Create"
   - Enter the following details:
     - Subscription: Select your subscription
     - Resource group: Enter "storage-cdn-lab"
     - Region: Select a region close to your location
   - Click "Review + create" and then "Create"

3. Create a storage account:
   - In the Azure portal, search for "Storage accounts"
   - Click "+ Create"
   - Configure the **Basics** tab:
     - Subscription: Select your subscription
     - Resource group: "storage-cdn-lab"
     - Storage account name: Enter a globally unique name (e.g., "staticwebsiteXXXXX" where XXXXX is a random number)
     - Region: Same as your resource group
     - Performance: Standard
     - Redundancy: Locally-redundant storage (LRS)
   - Click "Next: Advanced"
   - Configure the **Advanced** tab:
     - Under "Security", ensure "Enable blob public access" is checked
     - Leave other settings as default
   - Click "Next: Networking"
   - Leave default networking settings (Public endpoint (all networks))
   - Click "Review + create"
   - After validation passes, click "Create"

4. Enable static website hosting:
   - Once deployment is complete, go to the storage account
   - In the left menu, scroll down to the "Data management" section and click "Static website"
   - Change "Static website" to "Enabled"
   - Set the Index document name to "index.html"
   - Set the Error document path to "error.html"
   - Click "Save"
   - Note the "Primary endpoint" URL that appears after saving

5. Upload website content:
   - Create a simple index.html file on your local machine with the following content:
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Azure Static Website</title>
       <style>
           body {
               font-family: Arial, sans-serif;
               margin: 40px;
               text-align: center;
               color: #333;
           }
           .container {
               max-width: 800px;
               margin: 0 auto;
               background-color: #f0f8ff;
               padding: 30px;
               border-radius: 10px;
               box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
           }
           h1 {
               color: #0066cc;
           }
           img {
               max-width: 100%;
               height: auto;
               margin: 20px 0;
               border-radius: 8px;
           }
       </style>
   </head>
   <body>
       <div class="container">
           <h1>Hello from Azure Storage Static Website!</h1>
           <p>This page is being served from Azure Storage with static website hosting enabled.</p>
           <p>Current time: <span id="current-time"></span></p>
           <img src="https://azure.microsoft.com/svghandler/cdn/?width=600&height=315" alt="Azure CDN Image">
       </div>
       <script>
           document.getElementById('current-time').textContent = new Date().toLocaleString();
       </script>
   </body>
   </html>
   ```

   - Create an error.html file on your local machine with the following content:
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Error - Page Not Found</title>
       <style>
           body {
               font-family: Arial, sans-serif;
               margin: 40px;
               text-align: center;
               color: #333;
           }
           .container {
               max-width: 800px;
               margin: 0 auto;
               background-color: #fff0f0;
               padding: 30px;
               border-radius: 10px;
               box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
           }
           h1 {
               color: #cc0000;
           }
       </style>
   </head>
   <body>
       <div class="container">
           <h1>404 - Page Not Found</h1>
           <p>The page you are looking for does not exist.</p>
           <p><a href="index.html">Return to homepage</a></p>
       </div>
   </body>
   </html>
   ```

6. Upload files to the static website:
   - In the Azure portal, navigate to your storage account
   - In the left menu, under "Data storage", click on "Containers"
   - You should see a container named "$web" (created when you enabled static website hosting)
   - Click on the "$web" container
   - Click the "Upload" button
   - Upload your index.html and error.html files
   - Click "Upload" to complete the process

7. Verify your website:
   - Return to the "Static website" option in your storage account's left menu
   - Copy the "Primary endpoint" URL
   - Open the URL in a new browser tab
   - You should see your index.html content displayed
   - Test the error page by appending "/notfound" to the URL – you should see your error.html content

### Part 2: Create an Azure CDN Profile and Endpoint

1. Create a CDN profile:
   - In the Azure portal, search for "CDN profiles" and select it
   - Click "+ Create"
   - Enter the following details:
     - Subscription: Select your subscription
     - Resource group: "storage-cdn-lab"
     - Name: "static-website-cdn"
     - Pricing tier: Standard Microsoft
     - Create a new CDN endpoint: Yes
     - CDN endpoint name: Enter a unique name (e.g., "staticwebsite-endpoint")
     - Origin type: Storage static website
     - Origin hostname: Select your storage static website hostname from the dropdown
   - Click "Create"

2. Wait for deployment:
   - The CDN profile and endpoint creation will take a few minutes
   - Once deployed, navigate to your new CDN profile

3. Configure the endpoint:
   - In the CDN profile, click on the endpoint you created
   - Make note of the endpoint hostname (e.g., "staticwebsite-endpoint.azureedge.net")
   - This is your CDN URL that will deliver your content globally

4. Configure caching rules (optional):
   - In your CDN endpoint page, click on "Caching rules" in the left menu
   - Click "+ Add rule"
   - Configure a caching rule:
     - Name: "Default Caching"
     - Caching behavior: Override
     - Query string caching behavior: Ignore query strings
     - Cache expiration duration: 1 day
   - Click "Add" to create the rule

5. Test the CDN endpoint:
   - Note: It can take up to 10 minutes for the CDN to propagate settings globally
   - Open your CDN endpoint URL (noted in step 3) in a web browser
   - You should see the same content as your static website
   - The content is now being served through the CDN

### Part 3: Configure Custom Domain with HTTPS (Optional)

*Note: This part requires that you own a domain name and can modify its DNS settings*

1. Add a custom domain:
   - In your CDN endpoint page, click on "Custom domain" in the left menu
   - Click "+ Custom domain"
   - Enter your domain information:
     - Custom hostname: Enter your subdomain (e.g., "cdn.yourdomain.com")
     - Click "Add"

2. Configure DNS:
   - In the custom domain page, note the CNAME record you need to create
   - Go to your domain registrar's website
   - Create a CNAME record pointing your subdomain to your CDN endpoint hostname
     - Name/Host: subdomain (e.g., "cdn")
     - Value/Target: Your CDN endpoint (e.g., "staticwebsite-endpoint.azureedge.net")
     - TTL: 3600 (or as recommended)
   - Save the DNS changes

3. Enable HTTPS:
   - After your custom domain is validated (may take a few hours for DNS propagation)
   - In your custom domain settings, click on "Custom domain HTTPS"
   - Change "Custom domain HTTPS" to "On"
   - Certificate management type: CDN managed
   - Click "Save"
   - Note: HTTPS provisioning can take 6-8 hours

4. Test your custom domain:
   - Once HTTPS is enabled, navigate to your custom domain in a browser
   - Verify that your website loads successfully and has a valid HTTPS certificate

### Part 4: Monitor CDN Performance

1. View CDN metrics:
   - In your CDN endpoint page, click on "Metrics" in the left menu
   - Select metrics of interest:
     - Total requests
     - Request count by status code
     - Cache hit ratio
     - Bandwidth
   - Set the time range as needed
   - Observe the performance data

2. Set up alerts (optional):
   - In the metrics page, click "New alert rule"
   - Configure an alert:
     - Condition: Create based on a metric (e.g., if bandwidth exceeds a threshold)
     - Actions: Create an action group for email notifications
     - Alert details: Name and description
   - Click "Create alert rule"

## Clean Up Resources

When you're done experimenting, delete the resource group to avoid incurring charges:

1. Navigate to Resource Groups in the Azure portal
2. Select the "storage-cdn-lab" resource group
3. Click "Delete resource group"
4. Enter the resource group name to confirm deletion
5. Click "Delete"

## Key Takeaways

- You've learned how to create and configure a storage account for static website hosting
- You've implemented Azure CDN to improve content delivery globally
- You've configured caching and other CDN settings
- You've learned how to monitor CDN performance
- (Optional) You've configured a custom domain with HTTPS for your CDN endpoint

## Next Steps

- Experiment with different CDN pricing tiers and features
- Implement rules for content compression and URL rewriting
- Set up geo-filtering to control content access by country
- Configure multi-origin CDN for more complex scenarios
- Implement Azure Front Door for advanced global routing