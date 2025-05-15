# Hands-On Lab: Infrastructure as Code with Azure

## Objective
Implement Infrastructure as Code (IaC) using Azure Resource Manager (ARM) templates and Bicep to automate the deployment and management of Azure resources in a repeatable, consistent manner.

## Prerequisites
- An Azure account with an active subscription
- Azure CLI installed locally
- Visual Studio Code with ARM Tools and Bicep extensions
- Basic understanding of JSON and YAML formats
- Completion of AZ-104 labs (recommended)

## Estimated Time
2-3 hours

## Architecture Overview

In this lab, you will:
- Create ARM templates for a multi-tier application
- Refactor the ARM templates to Bicep for improved readability
- Parameterize the templates for different environments
- Implement resource dependencies and template functions
- Set up a CI/CD pipeline for infrastructure deployment
- Implement policy as code for governance

## Steps

### Part 1: Set Up Your Environment

1. Sign in to Azure using the Azure CLI:
   ```bash
   az login
   ```

2. Create a resource group for this lab:
   ```bash
   az group create --name IaC-Lab-RG --location eastus
   ```

3. Create a folder structure for your templates:
   ```bash
   mkdir -p IaC-Lab/templates
   cd IaC-Lab
   ```

4. Initialize a Git repository:
   ```bash
   git init
   echo "# Infrastructure as Code Lab" > README.md
   git add README.md
   git commit -m "Initial commit"
   ```

### Part 2: Create Your First ARM Template

1. Create a basic template for a storage account:
   
   Create a file named `templates/storage.json` with the following content:
   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "storageAccountName": {
         "type": "string",
         "metadata": {
           "description": "Name of the storage account"
         }
       },
       "location": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
           "description": "Location for the storage account"
         }
       },
       "sku": {
         "type": "string",
         "defaultValue": "Standard_LRS",
         "allowedValues": [
           "Standard_LRS",
           "Standard_GRS",
           "Standard_ZRS",
           "Premium_LRS"
         ],
         "metadata": {
           "description": "Storage account SKU"
         }
       }
     },
     "resources": [
       {
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2021-04-01",
         "name": "[parameters('storageAccountName')]",
         "location": "[parameters('location')]",
         "sku": {
           "name": "[parameters('sku')]"
         },
         "kind": "StorageV2",
         "properties": {
           "supportsHttpsTrafficOnly": true,
           "accessTier": "Hot",
           "minimumTlsVersion": "TLS1_2"
         }
       }
     ],
     "outputs": {
       "storageAccountId": {
         "type": "string",
         "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
       },
       "storageAccountEndpoint": {
         "type": "string",
         "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]"
       }
     }
   }
   ```

2. Create a parameters file for the storage account:
   
   Create a file named `templates/storage.parameters.json` with the following content:
   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "storageAccountName": {
         "value": "iaclab[uniquestring(resourceGroup().id)]"
       },
       "sku": {
         "value": "Standard_LRS"
       }
     }
   }
   ```

3. Deploy the ARM template:
   ```bash
   az deployment group create \
     --resource-group IaC-Lab-RG \
     --template-file templates/storage.json \
     --parameters storageAccountName=iaclab$(date +%s)
   ```

4. Verify the deployment:
   ```bash
   az storage account list \
     --resource-group IaC-Lab-RG \
     --query "[].{Name:name, Location:location, Kind:kind, Sku:sku.name}" \
     --output table
   ```

### Part 3: Create a Multi-Resource Template

1. Create a template for a web application environment:
   
   Create a file named `templates/webapp.json` with the following content:
   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "appServicePlanName": {
         "type": "string",
         "metadata": {
           "description": "Name of the App Service Plan"
         }
       },
       "webAppName": {
         "type": "string",
         "metadata": {
           "description": "Name of the Web App"
         }
       },
       "location": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
           "description": "Location for all resources"
         }
       },
       "sku": {
         "type": "string",
         "defaultValue": "F1",
         "allowedValues": [
           "F1",
           "D1",
           "B1",
           "B2",
           "B3",
           "S1",
           "S2",
           "S3",
           "P1v2",
           "P2v2",
           "P3v2"
         ],
         "metadata": {
           "description": "App Service Plan SKU"
         }
       },
       "linuxFxVersion": {
         "type": "string",
         "defaultValue": "DOTNETCORE|6.0",
         "metadata": {
           "description": "Runtime stack for the Web App"
         }
       },
       "environment": {
         "type": "string",
         "defaultValue": "dev",
         "allowedValues": [
           "dev",
           "test",
           "prod"
         ],
         "metadata": {
           "description": "Environment name"
         }
       }
     },
     "variables": {
       "appInsightsName": "[concat(parameters('webAppName'), '-insights')]",
       "tagsObject": {
         "Environment": "[parameters('environment')]",
         "Project": "IaC-Lab",
         "DeployedBy": "ARM"
       }
     },
     "resources": [
       {
         "type": "Microsoft.Web/serverfarms",
         "apiVersion": "2021-02-01",
         "name": "[parameters('appServicePlanName')]",
         "location": "[parameters('location')]",
         "tags": "[variables('tagsObject')]",
         "sku": {
           "name": "[parameters('sku')]"
         },
         "kind": "linux",
         "properties": {
           "reserved": true
         }
       },
       {
         "type": "Microsoft.Web/sites",
         "apiVersion": "2021-02-01",
         "name": "[parameters('webAppName')]",
         "location": "[parameters('location')]",
         "tags": "[variables('tagsObject')]",
         "dependsOn": [
           "[resourceId('Microsoft.Web/serverfarms', parameters('appServicePlanName'))]"
         ],
         "properties": {
           "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('appServicePlanName'))]",
           "httpsOnly": true,
           "siteConfig": {
             "linuxFxVersion": "[parameters('linuxFxVersion')]",
             "minTlsVersion": "1.2",
             "ftpsState": "Disabled"
           }
         }
       },
       {
         "type": "Microsoft.Insights/components",
         "apiVersion": "2020-02-02",
         "name": "[variables('appInsightsName')]",
         "location": "[parameters('location')]",
         "tags": "[variables('tagsObject')]",
         "kind": "web",
         "properties": {
           "Application_Type": "web",
           "Request_Source": "rest",
           "Flow_Type": "Redfield",
           "publicNetworkAccessForIngestion": "Enabled",
           "publicNetworkAccessForQuery": "Enabled"
         }
       },
       {
         "type": "Microsoft.Web/sites/config",
         "apiVersion": "2021-02-01",
         "name": "[concat(parameters('webAppName'), '/appsettings')]",
         "dependsOn": [
           "[resourceId('Microsoft.Web/sites', parameters('webAppName'))]",
           "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
         ],
         "properties": {
           "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', variables('appInsightsName')), '2020-02-02').InstrumentationKey]",
           "ApplicationInsightsAgent_EXTENSION_VERSION": "~3",
           "ENVIRONMENT": "[parameters('environment')]"
         }
       }
     ],
     "outputs": {
       "webAppUrl": {
         "type": "string",
         "value": "[concat('https://', reference(resourceId('Microsoft.Web/sites', parameters('webAppName'))).defaultHostName)]"
       },
       "appInsightsKey": {
         "type": "string",
         "value": "[reference(resourceId('Microsoft.Insights/components', variables('appInsightsName')), '2020-02-02').InstrumentationKey]"
       }
     }
   }
   ```

2. Create parameters files for different environments:
   
   Create a file named `templates/webapp.parameters.dev.json`:
   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "appServicePlanName": {
         "value": "iac-lab-plan-dev"
       },
       "webAppName": {
         "value": "iac-lab-app-dev"
       },
       "sku": {
         "value": "B1"
       },
       "environment": {
         "value": "dev"
       }
     }
   }
   ```
   
   Create a file named `templates/webapp.parameters.prod.json`:
   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "appServicePlanName": {
         "value": "iac-lab-plan-prod"
       },
       "webAppName": {
         "value": "iac-lab-app-prod"
       },
       "sku": {
         "value": "S1"
       },
       "environment": {
         "value": "prod"
       }
     }
   }
   ```

3. Deploy the web app to dev environment:
   ```bash
   az deployment group create \
     --resource-group IaC-Lab-RG \
     --template-file templates/webapp.json \
     --parameters @templates/webapp.parameters.dev.json
   ```

4. Verify the deployment:
   ```bash
   az webapp list \
     --resource-group IaC-Lab-RG \
     --query "[].{Name:name, Location:location, State:state}" \
     --output table
   ```

### Part 4: Convert ARM Templates to Bicep

1. Install Bicep CLI (if not already installed):
   ```bash
   az bicep install
   ```

2. Convert the storage account template to Bicep:
   ```bash
   az bicep decompile --file templates/storage.json
   ```
   This will create `templates/storage.bicep`.

3. Edit the generated Bicep file to optimize it and take advantage of Bicep's syntax improvements.
   
   Modify `templates/storage.bicep`:
   ```bicep
   @description('Name of the storage account')
   param storageAccountName string

   @description('Location for the storage account')
   param location string = resourceGroup().location

   @description('Storage account SKU')
   @allowed([
     'Standard_LRS'
     'Standard_GRS'
     'Standard_ZRS'
     'Premium_LRS'
   ])
   param sku string = 'Standard_LRS'

   resource storageAccount 'Microsoft.Storage/storageAccounts@2021-04-01' = {
     name: storageAccountName
     location: location
     sku: {
       name: sku
     }
     kind: 'StorageV2'
     properties: {
       supportsHttpsTrafficOnly: true
       accessTier: 'Hot'
       minimumTlsVersion: 'TLS1_2'
     }
   }

   output storageAccountId string = storageAccount.id
   output storageAccountEndpoint string = storageAccount.properties.primaryEndpoints.blob
   ```

4. Convert the web app template to Bicep:
   ```bash
   az bicep decompile --file templates/webapp.json
   ```
   This will create `templates/webapp.bicep`.

5. Edit the Web App Bicep file for further optimization:
   
   Modify `templates/webapp.bicep`:
   ```bicep
   @description('Name of the App Service Plan')
   param appServicePlanName string

   @description('Name of the Web App')
   param webAppName string

   @description('Location for all resources')
   param location string = resourceGroup().location

   @description('App Service Plan SKU')
   @allowed([
     'F1'
     'D1'
     'B1'
     'B2'
     'B3'
     'S1'
     'S2'
     'S3'
     'P1v2'
     'P2v2'
     'P3v2'
   ])
   param sku string = 'F1'

   @description('Runtime stack for the Web App')
   param linuxFxVersion string = 'DOTNETCORE|6.0'

   @description('Environment name')
   @allowed([
     'dev'
     'test'
     'prod'
   ])
   param environment string = 'dev'

   var appInsightsName = '${webAppName}-insights'
   var tags = {
     Environment: environment
     Project: 'IaC-Lab'
     DeployedBy: 'Bicep'
   }

   resource appServicePlan 'Microsoft.Web/serverfarms@2021-02-01' = {
     name: appServicePlanName
     location: location
     tags: tags
     sku: {
       name: sku
     }
     kind: 'linux'
     properties: {
       reserved: true
     }
   }

   resource webApp 'Microsoft.Web/sites@2021-02-01' = {
     name: webAppName
     location: location
     tags: tags
     properties: {
       serverFarmId: appServicePlan.id
       httpsOnly: true
       siteConfig: {
         linuxFxVersion: linuxFxVersion
         minTlsVersion: '1.2'
         ftpsState: 'Disabled'
       }
     }
   }

   resource appInsights 'Microsoft.Insights/components@2020-02-02' = {
     name: appInsightsName
     location: location
     tags: tags
     kind: 'web'
     properties: {
       Application_Type: 'web'
       Request_Source: 'rest'
       Flow_Type: 'Redfield'
       publicNetworkAccessForIngestion: 'Enabled'
       publicNetworkAccessForQuery: 'Enabled'
     }
   }

   resource webAppSettings 'Microsoft.Web/sites/config@2021-02-01' = {
     name: '${webApp.name}/appsettings'
     properties: {
       APPINSIGHTS_INSTRUMENTATIONKEY: appInsights.properties.InstrumentationKey
       ApplicationInsightsAgent_EXTENSION_VERSION: '~3'
       ENVIRONMENT: environment
     }
   }

   output webAppUrl string = 'https://${webApp.properties.defaultHostName}'
   output appInsightsKey string = appInsights.properties.InstrumentationKey
   ```

6. Deploy the Bicep template:
   ```bash
   az deployment group create \
     --resource-group IaC-Lab-RG \
     --template-file templates/webapp.bicep \
     --parameters @templates/webapp.parameters.prod.json
   ```

7. Verify the deployment:
   ```bash
   az webapp list \
     --resource-group IaC-Lab-RG \
     --query "[].{Name:name, Location:location, State:state}" \
     --output table
   ```

### Part 5: Create Modular Templates

1. Create a modules folder for reusable components:
   ```bash
   mkdir -p templates/modules
   ```

2. Create a storage module:
   
   Create a file named `templates/modules/storage.bicep`:
   ```bicep
   @description('Name of the storage account')
   param storageAccountName string

   @description('Location for the storage account')
   param location string = resourceGroup().location

   @description('Storage account SKU')
   @allowed([
     'Standard_LRS'
     'Standard_GRS'
     'Standard_ZRS'
     'Premium_LRS'
   ])
   param sku string = 'Standard_LRS'

   @description('Tags to apply to the resource')
   param tags object = {}

   resource storageAccount 'Microsoft.Storage/storageAccounts@2021-04-01' = {
     name: storageAccountName
     location: location
     tags: tags
     sku: {
       name: sku
     }
     kind: 'StorageV2'
     properties: {
       supportsHttpsTrafficOnly: true
       accessTier: 'Hot'
       minimumTlsVersion: 'TLS1_2'
     }
   }

   output storageAccountId string = storageAccount.id
   output storageAccountName string = storageAccount.name
   output blobEndpoint string = storageAccount.properties.primaryEndpoints.blob
   ```

3. Create an app service module:
   
   Create a file named `templates/modules/appservice.bicep`:
   ```bicep
   @description('Name of the App Service Plan')
   param appServicePlanName string

   @description('Name of the Web App')
   param webAppName string

   @description('Location for all resources')
   param location string = resourceGroup().location

   @description('App Service Plan SKU')
   param sku string = 'F1'

   @description('Runtime stack for the Web App')
   param linuxFxVersion string = 'DOTNETCORE|6.0'

   @description('Tags to apply to the resources')
   param tags object = {}

   @description('App settings for the Web App')
   param appSettings object = {}

   resource appServicePlan 'Microsoft.Web/serverfarms@2021-02-01' = {
     name: appServicePlanName
     location: location
     tags: tags
     sku: {
       name: sku
     }
     kind: 'linux'
     properties: {
       reserved: true
     }
   }

   resource webApp 'Microsoft.Web/sites@2021-02-01' = {
     name: webAppName
     location: location
     tags: tags
     properties: {
       serverFarmId: appServicePlan.id
       httpsOnly: true
       siteConfig: {
         linuxFxVersion: linuxFxVersion
         minTlsVersion: '1.2'
         ftpsState: 'Disabled'
       }
     }
   }

   resource webAppSettings 'Microsoft.Web/sites/config@2021-02-01' = {
     name: '${webApp.name}/appsettings'
     properties: appSettings
   }

   output webAppId string = webApp.id
   output webAppName string = webApp.name
   output defaultHostName string = webApp.properties.defaultHostName
   ```

4. Create an app insights module:
   
   Create a file named `templates/modules/appinsights.bicep`:
   ```bicep
   @description('Name of the Application Insights resource')
   param appInsightsName string

   @description('Location for the resource')
   param location string = resourceGroup().location

   @description('Tags to apply to the resource')
   param tags object = {}

   resource appInsights 'Microsoft.Insights/components@2020-02-02' = {
     name: appInsightsName
     location: location
     tags: tags
     kind: 'web'
     properties: {
       Application_Type: 'web'
       Request_Source: 'rest'
       Flow_Type: 'Redfield'
       publicNetworkAccessForIngestion: 'Enabled'
       publicNetworkAccessForQuery: 'Enabled'
     }
   }

   output appInsightsId string = appInsights.id
   output appInsightsName string = appInsights.name
   output instrumentationKey string = appInsights.properties.InstrumentationKey
   ```

5. Create a main template that uses the modules:
   
   Create a file named `templates/main.bicep`:
   ```bicep
   @description('Environment name')
   @allowed([
     'dev'
     'test'
     'prod'
   ])
   param environment string = 'dev'

   @description('Location for all resources')
   param location string = resourceGroup().location

   @description('Name prefix for all resources')
   param namePrefix string = 'iac-lab'

   // Variables for resource naming
   var storageAccountName = '${namePrefix}stor${environment}${uniqueString(resourceGroup().id)}'
   var appServicePlanName = '${namePrefix}-plan-${environment}'
   var webAppName = '${namePrefix}-app-${environment}'
   var appInsightsName = '${namePrefix}-insights-${environment}'

   // Common tags for all resources
   var tags = {
     Environment: environment
     Project: 'IaC-Lab'
     DeployedBy: 'Bicep'
   }

   // App settings based on environment
   var appSettings = {
     dev: {
       'ASPNETCORE_ENVIRONMENT': 'Development'
       'EnableDebug': 'true'
     }
     test: {
       'ASPNETCORE_ENVIRONMENT': 'Staging'
       'EnableDebug': 'false'
     }
     prod: {
       'ASPNETCORE_ENVIRONMENT': 'Production'
       'EnableDebug': 'false'
     }
   }

   // SKUs based on environment
   var skus = {
     dev: {
       appServicePlan: 'B1'
       storage: 'Standard_LRS'
     }
     test: {
       appServicePlan: 'B2'
       storage: 'Standard_LRS'
     }
     prod: {
       appServicePlan: 'S1'
       storage: 'Standard_GRS'
     }
   }

   // Module deployments
   module storage 'modules/storage.bicep' = {
     name: 'storageDeployment'
     params: {
       storageAccountName: storageAccountName
       location: location
       sku: skus[environment].storage
       tags: tags
     }
   }

   module appInsights 'modules/appinsights.bicep' = {
     name: 'appInsightsDeployment'
     params: {
       appInsightsName: appInsightsName
       location: location
       tags: tags
     }
   }

   module appService 'modules/appservice.bicep' = {
     name: 'appServiceDeployment'
     params: {
       appServicePlanName: appServicePlanName
       webAppName: webAppName
       location: location
       sku: skus[environment].appServicePlan
       linuxFxVersion: 'DOTNETCORE|6.0'
       tags: tags
       appSettings: union(appSettings[environment], {
         'APPINSIGHTS_INSTRUMENTATIONKEY': appInsights.outputs.instrumentationKey
         'ApplicationInsightsAgent_EXTENSION_VERSION': '~3'
         'ENVIRONMENT': environment
         'StorageConnectionString': 'DefaultEndpointsProtocol=https;AccountName=${storage.outputs.storageAccountName};EndpointSuffix=${environment().suffixes.storage}'
       })
     }
   }

   // Outputs
   output webAppUrl string = 'https://${appService.outputs.defaultHostName}'
   output storageAccountName string = storage.outputs.storageAccountName
   output appInsightsName string = appInsights.outputs.appInsightsName
   ```

6. Deploy the modular template:
   ```bash
   az deployment group create \
     --resource-group IaC-Lab-RG \
     --template-file templates/main.bicep \
     --parameters environment=dev namePrefix=iacmodule
   ```

7. Deploy the same template for production:
   ```bash
   az deployment group create \
     --resource-group IaC-Lab-RG \
     --template-file templates/main.bicep \
     --parameters environment=prod namePrefix=iacmodule
   ```

### Part 6: Set Up a CI/CD Pipeline for Infrastructure Deployment

1. Create a YAML pipeline file for Azure DevOps:
   
   Create a file named `iac-pipeline.yml`:
   ```yaml
   trigger:
     branches:
       include:
         - main
     paths:
       include:
         - templates/**

   pool:
     vmImage: 'ubuntu-latest'

   parameters:
     - name: environments
       type: object
       default:
         - name: 'dev'
           deployAfter: []
         - name: 'test'
           deployAfter: ['dev']
         - name: 'prod'
           deployAfter: ['test']

   stages:
   - ${{ each env in parameters.environments }}:
     - stage: Deploy_${{ env.name }}
       displayName: 'Deploy to ${{ env.name }}'
       dependsOn: ${{ env.deployAfter }}
       jobs:
       - job: ValidateAndDeploy
         steps:
         - task: AzureCLI@2
           displayName: 'Validate Bicep template'
           inputs:
             azureSubscription: 'Your-Azure-Connection'
             scriptType: 'bash'
             scriptLocation: 'inlineScript'
             inlineScript: |
               az bicep build --file templates/main.bicep

         - task: AzureCLI@2
           displayName: 'What-If Deployment'
           inputs:
             azureSubscription: 'Your-Azure-Connection'
             scriptType: 'bash'
             scriptLocation: 'inlineScript'
             inlineScript: |
               az deployment group what-if \
                 --resource-group IaC-Lab-RG-${{ env.name }} \
                 --template-file templates/main.bicep \
                 --parameters environment=${{ env.name }} namePrefix=iaccicd

         - ${{ if ne(env.name, 'dev') }}:
           - task: ManualValidation@0
             displayName: 'Approve deployment to ${{ env.name }}'
             inputs:
               notifyUsers: '$(approvers)'
               instructions: 'Please validate the What-If deployment output and approve to proceed with the actual deployment to ${{ env.name }}.'
               timeoutInMinutes: 60

         - task: AzureCLI@2
           displayName: 'Deploy Infrastructure'
           inputs:
             azureSubscription: 'Your-Azure-Connection'
             scriptType: 'bash'
             scriptLocation: 'inlineScript'
             inlineScript: |
               az deployment group create \
                 --resource-group IaC-Lab-RG-${{ env.name }} \
                 --template-file templates/main.bicep \
                 --parameters environment=${{ env.name }} namePrefix=iaccicd
   ```

2. Set up Azure Policy as Code:
   
   Create a file named `templates/policy/naming-convention.bicep`:
   ```bicep
   @description('The prefix that will be allowed for resources')
   param allowedPrefix string = 'iac'

   @description('The effect of the policy (Audit, Deny, Disabled)')
   @allowed([
     'Audit'
     'Deny'
     'Disabled'
   ])
   param effect string = 'Audit'

   var policyName = 'require-resource-prefix'
   var policyDisplayName = 'Resources should use allowed naming prefix'
   var policyDescription = 'This policy ensures all resources use the allowed naming prefix'

   resource policy 'Microsoft.Authorization/policyDefinitions@2021-06-01' = {
     name: policyName
     properties: {
       displayName: policyDisplayName
       description: policyDescription
       policyType: 'Custom'
       mode: 'Indexed'
       parameters: {
         prefix: {
           type: 'String'
           metadata: {
             displayName: 'Required Prefix'
             description: 'The prefix that will be required on resources'
           }
         }
         effect: {
           type: 'String'
           metadata: {
             displayName: 'Effect'
             description: 'The effect of the policy'
           }
           allowedValues: [
             'Audit'
             'Deny'
             'Disabled'
           ]
           defaultValue: effect
         }
       }
       policyRule: {
         if: {
           not: {
             field: 'name'
             like: '[concat(parameters(\'prefix\'), \'*\')]'
           }
         }
         then: {
           effect: '[parameters(\'effect\')]'
         }
       }
     }
   }

   resource assignment 'Microsoft.Authorization/policyAssignments@2021-06-01' = {
     name: '${policyName}-assignment'
     properties: {
       displayName: '${policyDisplayName} Assignment'
       description: policyDescription
       policyDefinitionId: policy.id
       parameters: {
         prefix: {
           value: allowedPrefix
         }
         effect: {
           value: effect
         }
       }
     }
   }
   ```

3. Deploy the policy:
   ```bash
   az deployment sub create \
     --location eastus \
     --template-file templates/policy/naming-convention.bicep \
     --parameters allowedPrefix=iac effect=Audit
   ```

### Part 7: Test and Validate Infrastructure

1. Test the deployed infrastructure:
   - Navigate to the App Service in the Azure Portal
   - Check that it's running and has the correct settings
   - Verify the Application Insights is collecting data
   - Check the Storage Account is properly configured

2. Implement a monitoring solution:
   
   Create a file named `templates/monitoring.bicep`:
   ```bicep
   @description('Log Analytics workspace name')
   param workspaceName string

   @description('Location for all resources')
   param location string = resourceGroup().location

   @description('Tags to apply to the resources')
   param tags object = {}

   @description('Web App resource ID to monitor')
   param webAppId string

   resource logAnalyticsWorkspace 'Microsoft.OperationalInsights/workspaces@2021-06-01' = {
     name: workspaceName
     location: location
     tags: tags
     properties: {
       sku: {
         name: 'PerGB2018'
       }
       retentionInDays: 30
     }
   }

   resource webAppDiagnostics 'Microsoft.Insights/diagnosticSettings@2021-05-01-preview' = {
     name: 'webapp-diagnostics'
     scope: resourceId('Microsoft.Web/sites', last(split(webAppId, '/')))
     properties: {
       workspaceId: logAnalyticsWorkspace.id
       logs: [
         {
           category: 'AppServiceHTTPLogs'
           enabled: true
         }
         {
           category: 'AppServiceConsoleLogs'
           enabled: true
         }
         {
           category: 'AppServiceAppLogs'
           enabled: true
         }
         {
           category: 'AppServiceAuditLogs'
           enabled: true
         }
       ]
       metrics: [
         {
           category: 'AllMetrics'
           enabled: true
         }
       ]
     }
   }

   resource alert 'Microsoft.Insights/metricAlerts@2018-03-01' = {
     name: 'high-cpu-alert'
     location: 'global'
     properties: {
       description: 'Alert when CPU usage is above 80%'
       severity: 2
       enabled: true
       scopes: [
         webAppId
       ]
       evaluationFrequency: 'PT5M'
       windowSize: 'PT15M'
       criteria: {
         'odata.type': 'Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria'
         allOf: [
           {
             name: 'CPU Usage'
             metricName: 'CpuPercentage'
             operator: 'GreaterThan'
             threshold: 80
             timeAggregation: 'Average'
             criterionType: 'StaticThresholdCriterion'
           }
         ]
       }
       actions: []
     }
   }

   output workspaceId string = logAnalyticsWorkspace.id
   ```

3. Deploy the monitoring solution:
   ```bash
   # First get the Web App ID
   WEB_APP_ID=$(az webapp show --resource-group IaC-Lab-RG --name iacmodule-app-dev --query id --output tsv)
   
   # Deploy the monitoring solution
   az deployment group create \
     --resource-group IaC-Lab-RG \
     --template-file templates/monitoring.bicep \
     --parameters workspaceName=iac-lab-logs webAppId=$WEB_APP_ID
   ```

## Clean Up Resources

When you're done experimenting, delete the resource group to avoid incurring charges:

1. Delete all resources:
   ```bash
   az group delete --name IaC-Lab-RG --yes --no-wait
   ```

## Key Takeaways

- You've learned how to create and manage Azure resources using Infrastructure as Code
- You've worked with ARM templates and converted them to Bicep for improved readability
- You've created modular templates for reuse across different environments
- You've parameterized templates to support multiple environments
- You've set up a CI/CD pipeline for infrastructure deployment
- You've implemented policy as code for governance
- You've created a monitoring solution for deployed resources

## Next Steps

- Explore advanced Bicep features like loops, conditions, and modules
- Implement more complex resource dependencies
- Set up cross-resource group deployments
- Implement secure DevOps practices for IaC
- Integrate with other IaC tools like Terraform
- Implement GitOps for infrastructure management
- Implement cost management and optimization strategies