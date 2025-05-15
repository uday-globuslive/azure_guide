# Hands-On Lab: Deploying a Web App with Azure App Service

## Objective
Deploy a simple web application using Azure App Service and explore its features.

## Prerequisites
- An Azure account with an active subscription
- Git installed on your local machine
- Basic familiarity with the Azure portal

## Estimated Time
30-45 minutes

## Steps

### 1. Create a Resource Group

1. In the Azure portal, search for "Resource groups" in the top search bar
2. Click "+ Create" to create a new resource group
3. Enter the following information:
   - Subscription: Select your subscription
   - Resource group: Enter "AppServiceLab"
   - Region: Select a region close to your location
4. Click "Review + create" and then "Create" after validation passes

### 2. Create an App Service Web App

1. In the Azure portal, search for "App Services" in the top search bar
2. Click "+ Create" and select "Web App"
3. Enter the following information:
   - Subscription: Select your subscription
   - Resource group: Select "AppServiceLab"
   - Name: Enter a globally unique name (e.g., "mywebapp" + random numbers)
   - Publish: Code
   - Runtime stack: Select ".NET Core 3.1" (or another stack you're familiar with)
   - Operating System: Windows
   - Region: Use the same region as your resource group
   - App Service Plan: Create a new one with these settings:
     - Name: "AppServicePlan"
     - Sku and size: Standard S1
4. Click "Review + create" and then "Create" after validation passes

### 3. Deploy a Sample Application

#### Option 1: Deploy from GitHub

1. Once deployment is complete, click "Go to resource"
2. In the left navigation, under "Deployment", click "Deployment Center"
3. Select "GitHub" as your source
4. Follow the prompts to authenticate with GitHub if needed
5. Select the following options:
   - Organization: Your GitHub username or organization
   - Repository: Select "Azure-Samples/app-service-web-dotnet-get-started" (or another sample repository compatible with your chosen runtime stack)
   - Branch: main
6. Click "Save" to start the deployment

#### Option 2: Use the Built-in Deployment Option

1. Once deployment is complete, click "Go to resource"
2. On the app service overview page, scroll down and click on "Browse" to view your web app
3. You'll see a default page indicating your app is running
4. Go back to the app service page and click on "Deployment Center" in the left navigation
5. Select "Local Git" and click "Continue"
6. Click "Finish" to set up the local Git repository
7. Note the Git Clone URI provided on the screen
8. Open a terminal or command prompt on your local machine
9. Clone a simple web application from GitHub:
   ```bash
   git clone https://github.com/Azure-Samples/html-docs-hello-world.git
   cd html-docs-hello-world
   ```
10. Add your Azure repository as a remote and push to it:
   ```bash
   git remote add azure <GIT_CLONE_URI>
   git push azure main
   ```
11. When prompted, enter the deployment credentials provided in the Deployment Center

### 4. View and Test Your Web Application

1. After the deployment completes, go back to the app service overview page
2. Click on "Browse" at the top of the page to open your web application
3. Your application should now be running and accessible from the internet

### 5. Explore App Service Features

#### Scaling

1. In the left navigation, under "Settings", click "Scale up (App Service plan)"
2. Explore the different pricing tiers available
3. Click on "Scale out (App Service plan)" in the left navigation
4. Configure auto-scaling by clicking "Enable autoscale" and setting conditions

#### Monitoring

1. In the left navigation, click "Metrics" under "Monitoring"
2. Explore the available metrics for your application
3. Click on "Log stream" under "Monitoring" to view real-time logs
4. Visit your website and observe the logs as you browse

#### Deployment Slots

1. In the left navigation, click "Deployment slots" under "Deployment"
2. Click "+ Add Slot"
3. Enter "staging" as the name and click "Add"
4. After the slot is created, click on it to navigate to its management page
5. Explore how you can deploy to this staging slot without affecting the production site

### 6. Configure Application Settings

1. In the left navigation of your main app (not the slot), click "Configuration" under "Settings"
2. Under "Application settings", click "+ New application setting"
3. Add a new setting:
   - Name: "ENVIRONMENT"
   - Value: "Production"
4. Click "OK" and then "Save" at the top
5. These settings will be available as environment variables to your application

## Clean Up Resources

When you're done experimenting, you can delete the resource group to clean up all resources:

1. Navigate to "Resource groups" in the Azure portal
2. Select "AppServiceLab"
3. Click "Delete resource group"
4. Enter the resource group name to confirm deletion
5. Click "Delete"

## Key Takeaways

- You've learned how to create an App Service web app in Azure
- You've deployed a sample application to the web app
- You've explored key features like scaling, monitoring, and deployment slots
- You've configured application settings
- You've learned how to clean up resources when they're no longer needed

## Next Steps

- Deploy your own web application to Azure App Service
- Set up continuous deployment from GitHub or Azure DevOps
- Explore custom domains and SSL
- Learn about App Service authentication and authorization
- Try App Service with different runtime stacks