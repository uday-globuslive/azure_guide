# Implementing Monitoring for Azure DevOps Pipelines

This hands-on lab guides you through setting up comprehensive monitoring for your Azure DevOps pipelines and deployed applications.

## Lab Overview

Duration: 120 minutes

In this lab, you will:
- Configure pipeline analytics and monitoring
- Set up application monitoring in Azure
- Implement logging and telemetry
- Create custom dashboards for DevOps metrics
- Configure alerts and notifications
- Implement automated remediation

## Prerequisites

- An Azure subscription
- Azure DevOps organization and project
- Sample application deployed to Azure (provided in this lab)
- Basic understanding of Azure Monitor and Application Insights

## Lab Environment Setup

1. Clone the sample repository:

```bash
git clone https://github.com/your-org/monitoring-devops-sample
cd monitoring-devops-sample
```

2. Sign in to Azure:

```bash
az login
```

3. Create resource group for this lab:

```bash
az group create --name monitor-devops-rg --location eastus
```

## Exercise 1: Setting Up Pipeline Analytics

### Task 1: Enable Pipeline Analytics

1. Navigate to Azure DevOps project settings

2. Under "Analytics", enable analytics for your project

3. Verify analytics with:

```bash
az devops configure --defaults organization=https://dev.azure.com/your-org/ project=your-project
az pipelines analytics show --pipeline-id <pipeline-id>
```

### Task 2: Create a Pipeline Dashboard

1. Navigate to Dashboards in Azure DevOps

2. Create a new dashboard named "Pipeline Performance"

3. Add the following widgets:
   - Build History
   - Release Pipeline Overview
   - Test Results Trend
   - Code Coverage
   - Deployment Status

4. Configure each widget to show relevant metrics

## Exercise 2: Application Monitoring Setup

### Task 1: Create Application Insights Resource

1. Create an Application Insights resource:

```bash
az monitor app-insights component create --app monitoring-app-insights \
                                       --location eastus \
                                       --resource-group monitor-devops-rg \
                                       --application-type web
```

2. Get the instrumentation key:

```bash
az monitor app-insights component show --app monitoring-app-insights \
                                     --resource-group monitor-devops-rg \
                                     --query instrumentationKey -o tsv
```

### Task 2: Instrument Application with App Insights

1. For a .NET application, add the following to your project file:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.20.0" />
</ItemGroup>
```

2. Configure Application Insights in your code:

```csharp
// Program.cs
builder.Services.AddApplicationInsightsTelemetry(builder.Configuration["APPLICATIONINSIGHTS_CONNECTION_STRING"]);
```

3. For a Node.js application:

```javascript
// app.js
const appInsights = require('applicationinsights');
appInsights.setup('your-instrumentation-key')
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .start();
```

4. Add the instrumentation key to your pipeline variables:

```yaml
variables:
  - name: ApplicationInsights__ConnectionString
    value: 'InstrumentationKey=your-instrumentation-key'
```

## Exercise 3: Setting Up Custom Logging

### Task 1: Configure Log Analytics Workspace

1. Create a Log Analytics workspace:

```bash
az monitor log-analytics workspace create --resource-group monitor-devops-rg \
                                        --workspace-name monitor-devops-logs \
                                        --location eastus
```

2. Link Application Insights to Log Analytics:

```bash
az monitor app-insights component update --app monitoring-app-insights \
                                       --resource-group monitor-devops-rg \
                                       --workspace-resource-id "/subscriptions/{subscription-id}/resourceGroups/monitor-devops-rg/providers/Microsoft.OperationalInsights/workspaces/monitor-devops-logs"
```

### Task 2: Implement Custom Logging

1. Create custom logs for your application:

```csharp
// C# example
private readonly ILogger<YourClass> _logger;

public YourClass(ILogger<YourClass> logger)
{
    _logger = logger;
}

public void YourMethod()
{
    _logger.LogInformation("Operation started at {time}", DateTime.UtcNow);
    // your code here
    _logger.LogInformation("Operation completed successfully");
}
```

2. Configure logging in pipeline YAML:

```yaml
- task: AzureCLI@2
  inputs:
    azureSubscription: 'Your-Azure-Subscription'
    scriptType: 'bash'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az monitor diagnostic-settings create \
        --resource $(webAppResourceId) \
        --name "pipeline-diagnostics" \
        --workspace $(logAnalyticsWorkspaceId) \
        --logs '[{"category":"AppServiceHTTPLogs","enabled":true},{"category":"AppServiceConsoleLogs","enabled":true},{"category":"AppServiceAppLogs","enabled":true}]' \
        --metrics '[{"category":"AllMetrics","enabled":true}]'
```

## Exercise 4: Creating Monitoring Dashboards

### Task 1: Build Azure Monitor Dashboard

1. Create a dashboard template:

```json
{
  "properties": {
    "lenses": {
      "0": {
        "order": 0,
        "parts": {
          "0": {
            "position": {
              "x": 0,
              "y": 0,
              "colSpan": 6,
              "rowSpan": 4
            },
            "metadata": {
              "inputs": [
                {
                  "name": "resourceTypeMode",
                  "value": "workspace"
                },
                {
                  "name": "ComponentId",
                  "value": {
                    "SubscriptionId": "${subscription-id}",
                    "ResourceGroup": "monitor-devops-rg",
                    "Name": "monitor-devops-logs",
                    "ResourceId": "/subscriptions/${subscription-id}/resourceGroups/monitor-devops-rg/providers/Microsoft.OperationalInsights/workspaces/monitor-devops-logs"
                  }
                }
              ],
              "type": "Extension/Microsoft_OperationsManagementSuite_Workspace/PartType/LogsDashboardPart",
              "settings": {
                "content": {
                  "Query": "AppServiceHTTPLogs\n| summarize RequestCount=count() by Status=tostring(ScStatus), bin(TimeGenerated, 1h)\n| render columnchart",
                  "chartSettings": {
                    "yAxis": {
                      "title": "HTTP Requests"
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
  }
}
```

2. Create the dashboard:

```bash
az portal dashboard create --resource-group monitor-devops-rg --name "AppMonitoring" --location eastus --input-path ./dashboard-template.json
```

### Task 2: Configure Workbooks for Advanced Visualization

1. Navigate to Azure Monitor Workbooks

2. Create a new workbook with the following queries:

```kql
// Performance overview
AppRequests
| where TimeGenerated > ago(24h)
| summarize RequestCount=count(), AvgDuration=avg(DurationMs), FailedCount=countif(Success==false) by bin(TimeGenerated, 1h)
| render columnchart

// Dependency calls
AppDependencies
| where TimeGenerated > ago(24h)
| summarize AvgDuration=avg(DurationMs), Count=count() by Type, Target
| order by Count desc
```

3. Save and pin the workbook to your dashboard

## Exercise 5: Setting Up Alerts and Notifications

### Task 1: Configure Azure Monitor Alerts

1. Create alert for pipeline failures:

```bash
az monitor metrics alert create --name "Pipeline-Failure-Alert" \
                              --resource-group monitor-devops-rg \
                              --scopes "/subscriptions/{subscription-id}/resourceGroups/monitor-devops-rg/providers/Microsoft.Insights/components/monitoring-app-insights" \
                              --condition "count requests/failed > 5 where request.name contains 'pipeline'" \
                              --window-size 5m \
                              --evaluation-frequency 1m
```

2. Create performance alert:

```bash
az monitor metrics alert create --name "High-Response-Time-Alert" \
                              --resource-group monitor-devops-rg \
                              --scopes "/subscriptions/{subscription-id}/resourceGroups/monitor-devops-rg/providers/Microsoft.Insights/components/monitoring-app-insights" \
                              --condition "avg requests/duration > 5000" \
                              --window-size 5m \
                              --evaluation-frequency 1m
```

### Task 2: Configure Action Groups

1. Create an action group for your team:

```bash
az monitor action-group create --name devops-monitoring-team \
                              --resource-group monitor-devops-rg \
                              --action email devops-team-lead your-email@example.com \
                              --action email devops-on-call on-call@example.com \
                              --action webhook devops-webhook-action https://your-webhook-url \
                              --action sms devops-oncall-sms 1234567890
```

2. Connect the action group to your alerts:

```bash
az monitor metrics alert update --name "Pipeline-Failure-Alert" \
                              --resource-group monitor-devops-rg \
                              --add-action /subscriptions/{subscription-id}/resourceGroups/monitor-devops-rg/providers/Microsoft.Insights/actionGroups/devops-monitoring-team
```

## Exercise 6: Automated Remediation

### Task 1: Create Logic App for Auto-Remediation

1. Create a Logic App for automatic scaling:

```bash
az group deployment create \
  --name AutoRemediation \
  --resource-group monitor-devops-rg \
  --template-file auto-remediation-template.json \
  --parameters logicAppName=devops-auto-remediation
```

2. Configure the Logic App with the following steps:
   - Trigger: When an Azure Monitor alert is fired
   - Condition: Check if alert is for high CPU usage or response time
   - Action: Scale up Azure App Service plan or restart web app

### Task 2: Create Runbook for Complex Remediation

1. Create an Azure Automation account:

```bash
az automation account create --name devops-automation \
                           --resource-group monitor-devops-rg \
                           --location eastus
```

2. Create a runbook:

```bash
az automation runbook create --resource-group monitor-devops-rg \
                           --automation-account-name devops-automation \
                           --name AutoRemediate \
                           --type PowerShell \
                           --content-link auto-remediate.ps1 \
                           --description "Auto remediation for common issues"
```

3. Link the runbook to your action group:

```bash
az monitor action-group create --name automation-actions \
                              --resource-group monitor-devops-rg \
                              --action webhook webhook-automation \
                              "https://s2events.azure-automation.net/webhooks?token=<token>&Id=<runbook-id>"
```

## Exercise 7: Continuous Improvement with Monitoring Data

1. Create an Azure Function to process monitoring data:

```bash
az functionapp create --resource-group monitor-devops-rg \
                     --consumption-plan-location eastus \
                     --runtime dotnet \
                     --name monitor-analysis-func \
                     --storage-account monitoranalysissa
```

2. Implement a function that analyzes performance trends:

```csharp
[FunctionName("AnalyzePerformanceTrends")]
public static async Task Run(
    [TimerTrigger("0 0 0 * * 1")] TimerInfo myTimer,
    [CosmosDB("monitoring", "trends", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<PerformanceReport> reportCollector,
    ILogger log)
{
    // Query Application Insights for performance data
    var client = new ApplicationInsightsClient(Environment.GetEnvironmentVariable("AppInsightsApiKey"));
    var data = await client.GetPerformanceDataAsync(DateTime.UtcNow.AddDays(-7), DateTime.UtcNow);
    
    // Analyze trends
    var report = new PerformanceReport
    {
        Id = Guid.NewGuid().ToString(),
        Generated = DateTime.UtcNow,
        AverageResponseTime = data.Average(d => d.ResponseTime),
        P95ResponseTime = CalculatePercentile(data.Select(d => d.ResponseTime).ToList(), 95),
        RequestVolume = data.Sum(d => d.RequestCount),
        FailureRate = data.Sum(d => d.FailedCount) / (double)data.Sum(d => d.RequestCount),
        Recommendations = GenerateRecommendations(data)
    };
    
    // Save report
    await reportCollector.AddAsync(report);
}
```

## Review and Cleanup

1. Review the monitoring dashboards

2. Check the alert configurations

3. Clean up resources:

```bash
az group delete --name monitor-devops-rg --yes --no-wait
```

## Key Learnings

- End-to-end monitoring is crucial for DevOps success
- Application Insights provides deep visibility into application performance
- Custom dashboards help visualize key metrics
- Alerts provide timely notifications of issues
- Automated remediation reduces mean time to recover (MTTR)
- Analyzing monitoring data helps drive continuous improvement

## Next Steps

- Implement more advanced KQL queries
- Set up cost monitoring for your Azure resources
- Implement business metrics monitoring
- Integrate monitoring with incident management systems
- Implement predictive analytics to detect issues before they occur