# Hands-On Lab: Implementing CI/CD with Azure DevOps

## Objective
Set up a complete CI/CD pipeline for a web application using Azure DevOps, including source control, build automation, release management, and monitoring.

## Prerequisites
- An Azure account with an active subscription
- An Azure DevOps organization
- Basic familiarity with web development (.NET Core, Node.js, or any web framework)
- Visual Studio Code or another code editor
- Git client installed locally

## Estimated Time
2-3 hours

## Architecture Overview
In this lab, you will create:
- A Git repository for your application code
- A CI build pipeline using YAML
- A multi-stage CD pipeline for different environments
- Integration with Azure App Service
- Monitoring with Application Insights

## Steps

### Part 1: Set Up Azure DevOps Project and Repository

1. Create a new Azure DevOps project:
   - Sign in to Azure DevOps (https://dev.azure.com)
   - Create a new project named "WebAppCICD"
   - Make it private
   - Initialize with Git version control

2. Clone the repository to your local machine:
   - On the Repos page, click "Clone" and copy the repository URL
   - Open a terminal/command prompt on your local machine
   - Run the following commands, replacing `<repo-url>` with your copied URL:
     ```bash
     git clone <repo-url>
     cd WebAppCICD
     ```

3. Create a simple web application:
   - For .NET Core:
     ```bash
     dotnet new webapp -n WebApp
     mv WebApp/* .
     rmdir WebApp
     ```
   - For Node.js:
     ```bash
     npm init -y
     npm install express --save
     ```
     Create a file named `app.js` with the following content:
     ```javascript
     const express = require('express');
     const app = express();
     const port = process.env.PORT || 3000;

     app.get('/', (req, res) => {
       res.send('Hello Azure DevOps CI/CD!');
     });

     app.listen(port, () => {
       console.log(`Server running on port ${port}`);
     });
     ```

4. Add a README.md file with project information:
   ```markdown
   # Web Application CI/CD Demo

   This is a demonstration project for implementing CI/CD pipelines with Azure DevOps.

   ## Pipeline Features
   - CI with automated building and testing
   - Multi-stage deployment with approval gates
   - Integration with Azure App Service
   - Application monitoring
   ```

5. Commit and push your code:
   ```bash
   git add .
   git commit -m "Initial commit with web application"
   git push
   ```

### Part 2: Create the CI Pipeline

1. Create a new pipeline:
   - In Azure DevOps, navigate to Pipelines
   - Click "Create Pipeline"
   - Select "Azure Repos Git" as your code location
   - Select your repository

2. Create a YAML pipeline file:
   - Choose "Starter pipeline"
   - Replace the YAML content with the appropriate configuration for your application type

   For .NET Core:
   ```yaml
   trigger:
   - main

   pool:
     vmImage: 'ubuntu-latest'

   variables:
     buildConfiguration: 'Release'

   steps:
   - task: UseDotNet@2
     inputs:
       packageType: 'sdk'
       version: '6.0.x'
       installationPath: $(Agent.ToolsDirectory)/dotnet

   - task: DotNetCoreCLI@2
     displayName: 'Restore project dependencies'
     inputs:
       command: 'restore'
       projects: '**/*.csproj'

   - task: DotNetCoreCLI@2
     displayName: 'Build the project'
     inputs:
       command: 'build'
       projects: '**/*.csproj'
       arguments: '--configuration $(buildConfiguration)'

   - task: DotNetCoreCLI@2
     displayName: 'Run unit tests'
     inputs:
       command: 'test'
       projects: '**/*Tests/*.csproj'
       arguments: '--configuration $(buildConfiguration)'

   - task: DotNetCoreCLI@2
     displayName: 'Publish the project'
     inputs:
       command: 'publish'
       projects: '**/*.csproj'
       publishWebProjects: true
       arguments: '--configuration $(buildConfiguration) --output $(Build.ArtifactStagingDirectory)'
       zipAfterPublish: true

   - task: PublishBuildArtifacts@1
     displayName: 'Publish artifacts'
     inputs:
       pathToPublish: '$(Build.ArtifactStagingDirectory)'
       artifactName: 'WebApp'
   ```

   For Node.js:
   ```yaml
   trigger:
   - main

   pool:
     vmImage: 'ubuntu-latest'

   steps:
   - task: NodeTool@0
     inputs:
       versionSpec: '14.x'
     displayName: 'Install Node.js'

   - script: |
       npm install
     displayName: 'Install dependencies'

   - script: |
       npm test
     displayName: 'Run tests'
     continueOnError: true

   - task: ArchiveFiles@2
     inputs:
       rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
       includeRootFolder: false
       archiveType: 'zip'
       archiveFile: '$(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip'
       replaceExistingArchive: true

   - task: PublishBuildArtifacts@1
     inputs:
       pathToPublish: '$(Build.ArtifactStagingDirectory)'
       artifactName: 'NodeApp'
   ```

3. Save the pipeline and run it:
   - Click "Save and run"
   - Commit to the main branch
   - Watch the pipeline execute

4. Review the build results:
   - Examine each step's output
   - Check the published artifacts
   - Review any warnings or errors

### Part 3: Create Azure Resources

1. Create a resource group:
   - Sign in to the Azure Portal (https://portal.azure.com)
   - Navigate to Resource Groups
   - Click "Create"
   - Name: "WebAppCICD-rg"
   - Region: Choose a region close to you
   - Click "Review + create" then "Create"

2. Create App Service Plans for different environments:
   - Navigate to App Service Plans
   - Click "Create"
   - For Dev environment:
     - Name: "dev-appservice-plan"
     - Resource Group: "WebAppCICD-rg"
     - OS: Windows or Linux (match your application)
     - Region: Same as resource group
     - Pricing tier: B1 (Basic)
   - Create another plan for Production:
     - Name: "prod-appservice-plan"
     - Resource Group: "WebAppCICD-rg"
     - OS: Windows or Linux (match your application)
     - Region: Same as resource group
     - Pricing tier: S1 (Standard)

3. Create Web Apps for each environment:
   - Navigate to App Services
   - Click "Create"
   - For Dev environment:
     - Name: "webappcicd-dev-[uniquename]" (must be globally unique)
     - Resource Group: "WebAppCICD-rg"
     - Publish: Code
     - Runtime stack: .NET Core or Node.js (match your application)
     - App Service Plan: dev-appservice-plan
   - Create another Web App for Production with similar settings but using:
     - Name: "webappcicd-prod-[uniquename]"
     - App Service Plan: prod-appservice-plan

4. Create Application Insights for monitoring:
   - Navigate to Application Insights
   - Click "Create"
   - Name: "webappcicd-insights"
   - Resource Group: "WebAppCICD-rg"
   - Region: Same as resource group
   - Application Type: Web
   - Click "Review + create" then "Create"
   - After creation, note the Instrumentation Key

### Part 4: Create the CD Pipeline

1. Create a new release pipeline:
   - In Azure DevOps, navigate to Pipelines > Releases
   - Click "New pipeline"
   - Select "Empty job" template

2. Add the build artifact:
   - Click "Add an artifact"
   - Source type: Build
   - Project: Your project
   - Source (build pipeline): Select your CI pipeline
   - Default version: Latest
   - Source alias: _WebApp (or _NodeApp)
   - Click "Add"

3. Configure the Dev stage:
   - Rename the default stage to "Dev"
   - Click on the "1 job, 0 task" link
   - Click the "+" on Agent job to add a task
   - Search for "Azure App Service Deploy" and add it
   - Configure the task:
     - Azure subscription: Select your subscription (create a service connection if needed)
     - App Service type: Web App
     - App Service name: webappcicd-dev-[uniquename]
     - Package or folder: $(System.DefaultWorkingDirectory)/_WebApp/WebApp/*.zip (adjust path based on your artifact name)

4. Add Application Insights integration:
   - Add another task: "Application Insights Configuration"
   - Configure:
     - Azure subscription: Your subscription
     - Application Insights resource: webappcicd-insights
     - Application Type: Web application

5. Add a Production stage:
   - Click the "Clone" button on the Dev stage
   - Rename the cloned stage to "Production"
   - Update the App Service name to your production Web App

6. Configure Pre-deployment approvals for Production:
   - Click on the Pre-deployment conditions icon for Production stage
   - Enable pre-deployment approvals
   - Approvers: Add your username
   - Click "Save"

7. Configure environment triggers:
   - Click on the lightning bolt icon on the artifact
   - Enable continuous deployment trigger
   - Click "Save"

8. Create the release:
   - Click "Create release"
   - Select the latest build
   - Click "Create"

9. Monitor the release:
   - Watch the deployment to Dev environment
   - After successful Dev deployment, approve the Production deployment
   - Monitor the Production deployment

### Part 5: Add Quality Gates

1. Add automated tests for pre-deployment validation:
   - Edit your release pipeline
   - Add a task before the App Service Deploy task in Dev stage
   - Add the "Visual Studio Test" task for .NET or "npm" task for Node.js
   - Configure to run your tests

2. Add security scan task:
   - Install WhiteSource Bolt or OWASP ZAP extension from the marketplace
   - Add the security scan task to your Dev stage
   - Configure to scan your application

3. Add approval gates:
   - Click on Pre-deployment conditions for Production
   - Under Gates, click "Add"
   - Add "Query Work Items" gate
   - Configure to check if there are no active bugs

4. Add post-deployment checks:
   - Click on Post-deployment conditions for both stages
   - Add an Azure Monitor gate
   - Configure to check for successful requests

5. Save and create a new release to test the gates.

### Part 6: Monitor the Application

1. Configure Application Insights in your application:
   - For .NET Core, add the Application Insights SDK to your project
   - For Node.js, add the Application Insights package and configure it with your key

2. Create a dashboard in Azure:
   - Navigate to Dashboard in Azure Portal
   - Create a new dashboard named "WebApp Monitoring"
   - Add Application Insights metrics:
     - Request rate
     - Failed requests
     - Server response time
     - Server exceptions

3. Set up alerts:
   - In Application Insights, navigate to Alerts
   - Create a new alert rule for:
     - High failure rate (> 5%)
     - Long response time (> 3 seconds)
     - Exception rate increase

4. Make a change to trigger the pipeline:
   - Update your application code
   - Commit and push the changes
   - Monitor the CI/CD pipeline execution
   - Verify the deployment to Dev and Production
   - Check the monitoring dashboard

## Clean Up Resources

When you're done experimenting, delete the resource group to avoid incurring charges:

1. Navigate to Resource Groups in the Azure portal
2. Select the "WebAppCICD-rg" resource group
3. Click "Delete resource group"
4. Enter the resource group name to confirm deletion
5. Click "Delete"

## Key Takeaways

- You've implemented a complete CI/CD pipeline with Azure DevOps
- You've set up multiple environments with appropriate security gates
- You've integrated monitoring and quality checks
- You've experienced the automation of the build and release process
- You've implemented best practices for DevOps

## Next Steps

- Implement Infrastructure as Code using ARM templates or Terraform
- Add container support using Docker and Azure Container Registry
- Implement feature flags using Azure App Configuration
- Set up automated load testing for performance validation
- Implement blue-green deployments or canary releases