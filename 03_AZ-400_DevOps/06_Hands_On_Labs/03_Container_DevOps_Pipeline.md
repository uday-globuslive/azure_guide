# Hands-On Lab: Implementing a Container DevOps Pipeline

## Objective
Create a complete CI/CD pipeline for containerized applications using Azure DevOps, Azure Container Registry, and Azure Kubernetes Service.

## Prerequisites
- An Azure account with an active subscription
- Azure DevOps organization and project
- GitHub account
- Basic knowledge of Docker and Kubernetes
- Completion of basic AZ-400 labs

## Estimated Time
2-3 hours

## Architecture Overview

In this lab, you will:
1. Create a containerized application
2. Set up Azure Container Registry
3. Deploy Azure Kubernetes Service
4. Configure CI pipeline to build and push container images
5. Implement CD pipeline for deployment to AKS
6. Add quality gates and security scanning
7. Monitor the deployed application

## Steps

### Part 1: Create Environment Infrastructure

1. Create a resource group:
   ```bash
   az group create --name container-devops-rg --location eastus
   ```

2. Create an Azure Container Registry:
   ```bash
   az acr create --resource-group container-devops-rg --name containerdevopsacr --sku Standard --admin-enabled true
   ```

3. Create an Azure Kubernetes Service cluster:
   ```bash
   az aks create \
     --resource-group container-devops-rg \
     --name container-devops-aks \
     --node-count 2 \
     --enable-addons monitoring \
     --generate-ssh-keys \
     --kubernetes-version 1.25.5 \
     --attach-acr containerdevopsacr
   ```

4. Get AKS credentials:
   ```bash
   az aks get-credentials --resource-group container-devops-rg --name container-devops-aks
   ```

### Part 2: Prepare Sample Application

1. Fork the sample application repository:
   - Go to https://github.com/Azure-Samples/azure-voting-app-redis
   - Click "Fork" to create a copy in your GitHub account

2. Clone the repository to your local machine:
   ```bash
   git clone https://github.com/YOUR-USERNAME/azure-voting-app-redis.git
   cd azure-voting-app-redis
   ```

3. Examine the application structure:
   - The application consists of two services:
     - Front-end web app written in Python
     - Redis instance for data storage
   - Review the `docker-compose.yaml` file
   - Check the `Dockerfile` in the azure-vote directory

4. Add Kubernetes manifests:
   Create a new directory named `kubernetes` and add two files:

   a. `kubernetes/azure-vote-all-in-one.yaml`:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: azure-vote-back
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: azure-vote-back
     template:
       metadata:
         labels:
           app: azure-vote-back
       spec:
         containers:
         - name: azure-vote-back
           image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
           env:
           - name: ALLOW_EMPTY_PASSWORD
             value: "yes"
           resources:
             requests:
               cpu: 100m
               memory: 128Mi
             limits:
               cpu: 250m
               memory: 256Mi
           ports:
           - containerPort: 6379
             name: redis
   ---
   apiVersion: v1
   kind: Service
   metadata:
     name: azure-vote-back
   spec:
     ports:
     - port: 6379
     selector:
       app: azure-vote-back
   ---
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: azure-vote-front
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: azure-vote-front
     template:
       metadata:
         labels:
           app: azure-vote-front
       spec:
         containers:
         - name: azure-vote-front
           image: #{ContainerRegistry}#/azure-vote-front:#{Build.BuildId}#
           resources:
             requests:
               cpu: 100m
               memory: 128Mi
             limits:
               cpu: 250m
               memory: 256Mi
           ports:
           - containerPort: 8080
           env:
           - name: REDIS
             value: "azure-vote-back"
   ---
   apiVersion: v1
   kind: Service
   metadata:
     name: azure-vote-front
   spec:
     type: LoadBalancer
     ports:
     - port: 80
       targetPort: 8080
     selector:
       app: azure-vote-front
   ```

   b. `kubernetes/azure-vote-namespace.yaml`:
   ```yaml
   apiVersion: v1
   kind: Namespace
   metadata:
     name: azure-vote
   ```

5. Add a liveness probe to the frontend deployment:
   Modify the container section in `kubernetes/azure-vote-all-in-one.yaml`:
   ```yaml
   containers:
   - name: azure-vote-front
     image: #{ContainerRegistry}#/azure-vote-front:#{Build.BuildId}#
     resources:
       requests:
         cpu: 100m
         memory: 128Mi
       limits:
         cpu: 250m
         memory: 256Mi
     ports:
     - containerPort: 8080
     env:
     - name: REDIS
       value: "azure-vote-back"
     livenessProbe:
       httpGet:
         path: /
         port: 8080
       initialDelaySeconds: 30
       periodSeconds: 10
   ```

6. Commit and push your changes:
   ```bash
   git add kubernetes/
   git commit -m "Add Kubernetes manifests"
   git push
   ```

### Part 3: Set Up CI Pipeline

1. Create a new pipeline in Azure DevOps:
   - Open your Azure DevOps project
   - Navigate to Pipelines > Pipelines
   - Click "Create Pipeline"
   - Select "GitHub" as the source
   - Select your forked repository
   - You may need to authenticate with GitHub and install the Azure Pipelines app
   - Choose "Starter pipeline"

2. Replace the YAML content with the following:
   ```yaml
   trigger:
     branches:
       include:
       - main
     paths:
       include:
       - azure-vote/
       - kubernetes/
   
   pool:
     vmImage: 'ubuntu-latest'
   
   variables:
     dockerRegistryServiceConnection: 'acr-service-connection'
     imageRepository: 'azure-vote-front'
     containerRegistry: 'containerdevopsacr.azurecr.io'
     dockerfilePath: 'azure-vote/Dockerfile'
     tag: '$(Build.BuildId)'
     imagePullSecret: 'container-devops-acr-auth'
   
   stages:
   - stage: Build
     displayName: Build and push stage
     jobs:  
     - job: Build
       displayName: Build job
       steps:
       - task: Docker@2
         displayName: Build and push an image to container registry
         inputs:
           command: buildAndPush
           repository: $(imageRepository)
           dockerfile: $(dockerfilePath)
           containerRegistry: $(dockerRegistryServiceConnection)
           tags: |
             $(tag)
             latest
       
       - task: CopyFiles@2
         inputs:
           SourceFolder: 'kubernetes'
           Contents: '**'
           TargetFolder: '$(Build.ArtifactStagingDirectory)'
       
       - task: PublishBuildArtifacts@1
         inputs:
           PathtoPublish: '$(Build.ArtifactStagingDirectory)'
           ArtifactName: 'manifests'
           publishLocation: 'Container'
   ```

3. Set up a service connection to ACR:
   - In your Azure DevOps project, go to Project settings > Service connections
   - Click "New service connection"
   - Select "Docker Registry"
   - Choose "Azure Container Registry"
   - Select your subscription and container registry
   - Service connection name: "acr-service-connection"
   - Click "Save"

4. Run the pipeline:
   - Click "Save and run"
   - Commit to the main branch
   - Watch the pipeline execution
   - Verify that the image is built and pushed to ACR

### Part 4: Add Security Scanning to CI Pipeline

1. Update the CI pipeline to include container scanning:
   Update your YAML file by adding this task after the Docker@2 task:
   ```yaml
   - task: AquaSecurityScan@5
     inputs:
       image: '$(containerRegistry)/$(imageRepository):$(tag)'
       register: true
       reportFormat: 'JunitXml'
       outputFormat: 'html'
       htmlReport: '$(Build.ArtifactStagingDirectory)/aqua-scan.html'
       jsonReport: '$(Build.ArtifactStagingDirectory)/aqua-scan.json'
   ```

   Note: This assumes you have the Aqua Security extension installed. If not, you can use another security scanning tool or remove this step.

2. Add a task to publish the scan results:
   ```yaml
   - task: PublishBuildArtifacts@1
     inputs:
       PathtoPublish: '$(Build.ArtifactStagingDirectory)/aqua-scan.html'
       ArtifactName: 'security-scan'
       publishLocation: 'Container'
   ```

3. Run the updated pipeline and review the security scan results.

### Part 5: Create CD Pipeline

1. Create a new YAML file for the release pipeline:
   In your repository, create a file `azure-pipelines-cd.yml`:
   ```yaml
   trigger: none
   
   resources:
     pipelines:
     - pipeline: ci-pipeline
       source: YourCIPipelineName # Replace with your actual CI pipeline name
       trigger: true
   
   pool:
     vmImage: 'ubuntu-latest'
   
   variables:
     k8sNamespace: 'azure-vote'
     vmImageName: 'ubuntu-latest'
     k8sConnection: 'aks-service-connection'
     containerRegistry: 'containerdevopsacr.azurecr.io'
     imageRepository: 'azure-vote-front'
   
   stages:
   - stage: Deploy
     displayName: Deploy to AKS
     jobs:
     - deployment: Deploy
       displayName: Deploy to AKS
       environment: development
       strategy:
         runOnce:
           deploy:
             steps:
             - checkout: none
   
             - download: ci-pipeline
               artifact: manifests
   
             - task: KubernetesManifest@0
               displayName: Create namespace
               inputs:
                 action: createNamespace
                 kubernetesServiceConnection: $(k8sConnection)
                 namespace: $(k8sNamespace)
   
             - task: replacetokens@3
               displayName: Replace tokens in manifest
               inputs:
                 rootDirectory: '$(Pipeline.Workspace)/ci-pipeline/manifests'
                 targetFiles: '**/*.yaml'
                 encoding: 'auto'
                 writeBOM: true
                 actionOnMissing: 'warn'
                 keepToken: false
                 tokenPrefix: '#{'
                 tokenSuffix: '}#'
                 useLegacyPattern: false
                 enableTransforms: false
                 enableTelemetry: true
   
             - task: KubernetesManifest@0
               displayName: Deploy to AKS
               inputs:
                 action: deploy
                 kubernetesServiceConnection: $(k8sConnection)
                 namespace: $(k8sNamespace)
                 manifests: |
                   $(Pipeline.Workspace)/ci-pipeline/manifests/azure-vote-all-in-one.yaml
                 containers: |
                   $(containerRegistry)/$(imageRepository):$(resources.pipeline.ci-pipeline.runID)
   ```

2. Create a service connection to AKS:
   - In your Azure DevOps project, go to Project settings > Service connections
   - Click "New service connection"
   - Select "Kubernetes"
   - Choose "Azure Subscription"
   - Select your subscription, resource group, and cluster
   - Service connection name: "aks-service-connection"
   - Click "Save"

3. Add Replace Tokens extension:
   - Go to the Azure DevOps Marketplace
   - Search for "Replace Tokens"
   - Install "Replace Tokens" by Guillaume Rouchon
   - Select your organization and install

4. Create a new release pipeline:
   - Navigate to Pipelines > Pipelines
   - Click "New pipeline"
   - Select "GitHub" as the source
   - Select your forked repository
   - Select "Existing Azure Pipelines YAML file"
   - Select `/azure-pipelines-cd.yml`
   - Update the CI pipeline name in the YAML if needed
   - Click "Run"

### Part 6: Add Quality Gates to CD Pipeline

1. Update the CD YAML file to include quality gates:
   Add the following stage before the Deploy stage:
   ```yaml
   - stage: Validate
     displayName: Validate manifests
     jobs:
     - job: kubeval
       displayName: Kubeval validation
       steps:
       - download: ci-pipeline
         artifact: manifests
       
       - task: Bash@3
         displayName: Install Kubeval
         inputs:
           targetType: 'inline'
           script: |
             wget https://github.com/instrumenta/kubeval/releases/latest/download/kubeval-linux-amd64.tar.gz
             tar xf kubeval-linux-amd64.tar.gz
             sudo cp kubeval /usr/local/bin
       
       - task: Bash@3
         displayName: Validate Kubernetes manifests
         inputs:
           targetType: 'inline'
           script: |
             find $(Pipeline.Workspace)/ci-pipeline/manifests -name "*.yaml" -print0 | xargs -0 kubeval --strict
   ```

2. Update the CD YAML file to include a post-deployment verification:
   Add the following steps at the end of the deployment strategy:
   ```yaml
   - task: Bash@3
     displayName: Verify deployment
     inputs:
       targetType: 'inline'
       script: |
         kubectl get services azure-vote-front --namespace $(k8sNamespace)
         
         # Wait for the service to get an external IP
         echo "Waiting for external IP..."
         while [ -z "$(kubectl get service azure-vote-front --namespace $(k8sNamespace) -o jsonpath='{.status.loadBalancer.ingress[0].ip}')" ]; do
           sleep 10
         done
         
         SERVICE_IP=$(kubectl get service azure-vote-front --namespace $(k8sNamespace) -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
         echo "Service available at http://$SERVICE_IP"
         
         # Verify application is responding
         echo "Verifying application response..."
         curl -s -o /dev/null -w "%{http_code}" http://$SERVICE_IP | grep 200
   ```

3. Update your pipeline and run it.

### Part 7: Set Up Application Monitoring

1. Create Application Insights:
   ```bash
   az monitor app-insights component create \
     --app container-app-insights \
     --location eastus \
     --resource-group container-devops-rg \
     --application-type web
   ```

2. Get the instrumentation key:
   ```bash
   INSTRUMENTATION_KEY=$(az monitor app-insights component show \
     --resource-group container-devops-rg \
     --app container-app-insights \
     --query instrumentationKey -o tsv)
   ```

3. Update the application to use Application Insights:
   Add the following lines to the `azure-vote/azure-vote/main.py` file, after the imports:
   ```python
   # Add Application Insights
   from opencensus.ext.azure.log_exporter import AzureLogHandler
   from opencensus.ext.azure.trace_exporter import AzureExporter
   from opencensus.trace.samplers import ProbabilitySampler
   from opencensus.trace.tracer import Tracer
   import logging
   
   # Set up Application Insights
   logger = logging.getLogger(__name__)
   handler = AzureLogHandler(connection_string=f'InstrumentationKey={os.environ.get("APPINSIGHTS_INSTRUMENTATIONKEY", "")}')
   logger.addHandler(handler)
   
   # Set up Azure Exporter
   tracer = Tracer(
       exporter=AzureExporter(
           connection_string=f'InstrumentationKey={os.environ.get("APPINSIGHTS_INSTRUMENTATIONKEY", "")}',
       ),
       sampler=ProbabilitySampler(1.0),
   )
   ```

4. Update the application's requirements:
   Add these lines to `azure-vote/requirements.txt`:
   ```
   opencensus-ext-azure
   opencensus-ext-flask
   ```

5. Add logging to the application:
   In the route handlers, add logging statements:
   ```python
   @app.route('/', methods=['GET', 'POST'])
   def index():
       logger.info("Request received for index page")
       with tracer.span(name="index_route") as span:
           # Existing code...
   ```

6. Update the Kubernetes manifest to include the instrumentation key:
   Add an environment variable to the frontend deployment in `kubernetes/azure-vote-all-in-one.yaml`:
   ```yaml
   env:
   - name: REDIS
     value: "azure-vote-back"
   - name: APPINSIGHTS_INSTRUMENTATIONKEY
     value: "#{AppInsightsKey}#"
   ```

7. Update your CD pipeline to set the Application Insights key:
   Add a variable to the `azure-pipelines-cd.yml` file:
   ```yaml
   variables:
     k8sNamespace: 'azure-vote'
     vmImageName: 'ubuntu-latest'
     k8sConnection: 'aks-service-connection'
     containerRegistry: 'containerdevopsacr.azurecr.io'
     imageRepository: 'azure-vote-front'
     AppInsightsKey: '12345678-1234-1234-1234-123456789012' # Replace with your actual key
   ```

8. Commit your changes:
   ```bash
   git add .
   git commit -m "Add Application Insights monitoring"
   git push
   ```

9. Run the CI/CD pipelines to deploy the updated application.

### Part 8: Create Dashboard for Application Monitoring

1. In the Azure Portal, navigate to Application Insights:
   - Find your Application Insights resource
   - Click on "Dashboards" in the left menu
   - Click "New dashboard"
   - Name it "Container App Monitoring"

2. Add monitoring tiles:
   - Add a "Metrics chart" tile for "Server response time"
   - Add a "Metrics chart" tile for "Server requests"
   - Add a "Metrics chart" tile for "Failed requests"
   - Add a "Log Query" tile with the following query:
     ```kusto
     traces
     | where timestamp > ago(1h)
     | project timestamp, message
     | order by timestamp desc
     ```
   - Add an "Application Map" tile to visualize dependencies

3. Customize your dashboard layout and save it.

## Clean Up Resources

When you're done experimenting, delete the resource group to avoid incurring charges:

```bash
az group delete --name container-devops-rg --yes
```

## Key Takeaways

- You've learned how to implement a complete CI/CD pipeline for containerized applications
- You've configured security scanning for container images
- You've set up quality gates for Kubernetes manifests
- You've implemented automated deployment validation
- You've added application monitoring with Application Insights
- You've created a dashboard for monitoring the application

## Next Steps

- Implement blue-green or canary deployments for zero-downtime updates
- Add automated load testing before production deployment
- Implement additional security scanning for vulnerabilities
- Set up policy compliance checks for your Kubernetes cluster
- Implement GitOps for continuous deployment with Flux or ArgoCD
- Add cost monitoring and optimization for your containerized application