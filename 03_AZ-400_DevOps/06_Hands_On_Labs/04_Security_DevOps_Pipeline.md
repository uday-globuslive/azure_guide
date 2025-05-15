# Implementing Security in Azure DevOps Pipelines

This hands-on lab guides you through implementing security practices in Azure DevOps pipelines to create a secure CI/CD process.

## Lab Overview

Duration: 90 minutes

In this lab, you will:
- Configure security scanning in Azure DevOps pipelines
- Implement secret management with Azure Key Vault
- Set up vulnerability scanning for container images
- Implement policy as code with Azure Policy
- Configure compliance scanning and reporting

## Prerequisites

- An Azure subscription
- Azure DevOps organization and project
- Basic understanding of YAML pipelines
- Sample application code (provided in this lab)

## Lab Environment Setup

1. Clone the sample repository:

```bash
git clone https://github.com/your-org/secure-devops-sample
cd secure-devops-sample
```

2. Sign in to Azure:

```bash
az login
```

3. Create resource group for this lab:

```bash
az group create --name secure-devops-rg --location eastus
```

## Exercise 1: Integrating Code Security Scanning

### Task 1: Configure CodeQL Analysis

1. Create a `.github/workflows/codeql-analysis.yml` file in your project:

```yaml
name: "CodeQL"

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '30 1 * * 0'

jobs:
  analyze:
    name: Analyze
    runs-on: ubuntu-latest

    strategy:
      fail-fast: false
      matrix:
        language: [ 'javascript', 'python' ]

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Initialize CodeQL
      uses: github/codeql-action/init@v1
      with:
        languages: ${{ matrix.language }}

    - name: Perform CodeQL Analysis
      uses: github/codeql-action/analyze@v1
```

2. For Azure DevOps, add the following to your `azure-pipelines.yml`:

```yaml
- task: PublishSecurityAnalysisLogs@3
  displayName: 'Publish Security Analysis Logs'
  inputs:
    ArtifactName: 'CodeAnalysisLogs'
    ArtifactType: 'Container'
    AllTools: true
    ToolLogsNotFoundAction: 'Standard'
```

### Task 2: Implement SonarCloud Analysis

1. Create a new SonarCloud account and project

2. Add the SonarCloud extension from the Azure DevOps marketplace

3. Add the following to your pipeline:

```yaml
- task: SonarCloudPrepare@1
  inputs:
    SonarCloud: 'SonarCloud'
    organization: '$(SonarOrganization)'
    scannerMode: 'MSBuild'
    projectKey: '$(SonarProjectKey)'
    projectName: '$(SonarProjectName)'
    extraProperties: |
      sonar.exclusions=**/node_modules/**
      sonar.coverage.exclusions=**/*.test.js,**/*.spec.js

- task: SonarCloudAnalyze@1

- task: SonarCloudPublish@1
  inputs:
    pollingTimeoutSec: '300'
```

4. Configure quality gates in SonarCloud to enforce security standards

## Exercise 2: Integrating Azure Key Vault for Secret Management

### Task 1: Create and Configure Azure Key Vault

1. Create a Key Vault:

```bash
az keyvault create --name secure-devops-kv --resource-group secure-devops-rg --location eastus
```

2. Add secrets to the Key Vault:

```bash
az keyvault secret set --vault-name secure-devops-kv --name "DbConnectionString" --value "Server=myServerAddress;Database=myDataBase;User Id=myUsername;Password=myPassword;"
az keyvault secret set --vault-name secure-devops-kv --name "ApiKey" --value "your-api-key-here"
```

### Task 2: Configure Azure DevOps to Access Key Vault

1. Create a service connection to Azure in Azure DevOps

2. Update your pipeline to access Key Vault:

```yaml
- task: AzureKeyVault@2
  inputs:
    azureSubscription: 'Your-Azure-Subscription'
    KeyVaultName: 'secure-devops-kv'
    SecretsFilter: 'DbConnectionString,ApiKey'
    RunAsPreJob: true

- script: |
    echo "Using connection string: $(DbConnectionString)"
    echo "Using API key: $(ApiKey)"
  displayName: 'Use secrets securely'
```

## Exercise 3: Container Image Vulnerability Scanning

### Task 1: Set Up Container Scanning

1. Create an Azure Container Registry:

```bash
az acr create --resource-group secure-devops-rg --name securedevopsacr --sku Standard
```

2. Enable vulnerability scanning:

```bash
az acr security enable-vulnerability-scanning --registry securedevopsacr
```

3. Add container scanning to your pipeline:

```yaml
- task: Docker@2
  displayName: 'Build and push container image'
  inputs:
    containerRegistry: 'securedevopsacr'
    repository: 'secureapp'
    command: 'buildAndPush'
    Dockerfile: '**/Dockerfile'
    tags: '$(Build.BuildNumber)'

- task: AzureContainerScan@0
  inputs:
    azureSubscriptionEndpoint: 'Your-Azure-Subscription'
    containerRegistryName: 'securedevopsacr'
    repository: 'secureapp'
    tag: '$(Build.BuildNumber)'
    severityThreshold: 'High'
```

### Task 2: Implement Policy for Image Deployment

1. Create a policy that prevents deployment of vulnerable images:

```bash
az policy definition create --name 'deny-vulnerable-images' \
                          --display-name 'Deny deployment of vulnerable container images' \
                          --description 'This policy denies deployment of container images with high severity vulnerabilities' \
                          --rules 'path-to-your-policy-rules.json' \
                          --mode All
```

2. Assign the policy to your resource group:

```bash
az policy assignment create --name 'deny-vulnerable-images-assignment' \
                           --policy 'deny-vulnerable-images' \
                           --scope '/subscriptions/{subscriptionId}/resourceGroups/secure-devops-rg'
```

## Exercise 4: Implementing Compliance Checks

### Task 1: Set Up Compliance Scanning

1. Add a compliance scanning task to your pipeline:

```yaml
- task: securityComplianceScanner@2
  inputs:
    scanType: 'Configuration'
    targetType: 'Source'
    targetPath: '$(System.DefaultWorkingDirectory)'
    reportPath: '$(System.DefaultWorkingDirectory)/compliance-report.json'
```

### Task 2: Generate Compliance Reports

1. Add a task to publish compliance reports:

```yaml
- task: PublishBuildArtifacts@1
  inputs:
    pathToPublish: '$(System.DefaultWorkingDirectory)/compliance-report.json'
    artifactName: 'ComplianceReports'
```

## Exercise 5: Implementing Branch Policies

1. In Azure DevOps, navigate to your repository settings

2. Under Branches, select your main branch and configure policies:
   - Require a minimum number of reviewers (set to 2)
   - Check for linked work items
   - Require build verification
   - Enable comment resolution

3. Create a feature branch and experience the workflow:

```bash
git checkout -b feature/secure-login
# Make changes
git add .
git commit -m "Implement secure login feature"
git push -u origin feature/secure-login
```

4. Create a pull request and observe policy enforcement

## Exercise 6: Continuous Security Monitoring

1. Set up Azure Security Center for your resources:

```bash
az security center auto-provisioning --name on --subscription $subscriptionId
```

2. Create a dashboard for security monitoring:

```bash
az portal dashboard create --resource-group secure-devops-rg --name "SecurityMonitoring" --location eastus --input-path ./security-dashboard-template.json
```

## Review and Cleanup

1. Review the security scan results

2. Analyze the compliance reports

3. Clean up resources:

```bash
az group delete --name secure-devops-rg --yes --no-wait
```

## Key Learnings

- Security should be integrated throughout the DevOps pipeline
- Secret management is crucial for secure applications
- Container security requires scanning, policy enforcement, and monitoring
- Compliance must be continuously verified and documented
- Branch policies help enforce secure development practices

## Next Steps

- Implement infrastructure as code scanning
- Configure advanced threat detection
- Set up security regression testing
- Implement runtime application security monitoring