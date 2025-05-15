# Hands-On Lab: Configuring Azure Monitor

## Objective
Set up comprehensive monitoring for Azure resources using Azure Monitor, Log Analytics, and Application Insights. Create custom dashboards, alerts, and automated responses for operational insights.

## Prerequisites
- An Azure account with an active subscription
- Contributor or Owner access to an Azure subscription
- Basic familiarity with Azure resources
- Completion of earlier AZ-104 labs

## Estimated Time
1.5-2 hours

## Architecture Overview

In this lab, you will:
- Set up a Log Analytics workspace
- Configure data collection from multiple sources
- Deploy test resources to monitor
- Create custom queries and alerts
- Configure automated responses
- Create operational dashboards

## Steps

### Part 1: Create Log Analytics Workspace and Configure Data Sources

1. Create a Resource Group:
   ```bash
   az group create --name monitoring-lab-rg --location eastus
   ```

2. Create a Log Analytics Workspace:
   ```bash
   az monitor log-analytics workspace create \
     --resource-group monitoring-lab-rg \
     --workspace-name monitoring-workspace \
     --location eastus \
     --sku PerGB2018
   ```

3. Deploy a test virtual machine:
   ```bash
   # Create a Windows VM for monitoring
   az vm create \
     --resource-group monitoring-lab-rg \
     --name monitoring-vm \
     --image Win2019Datacenter \
     --admin-username azureadmin \
     --admin-password "YourComplexPassword123!" \
     --size Standard_DS2_v2 \
     --public-ip-sku Standard
   ```

4. Install the Log Analytics agent on the VM:
   ```bash
   # Get workspace ID and keys
   WORKSPACE_ID=$(az monitor log-analytics workspace show --resource-group monitoring-lab-rg --workspace-name monitoring-workspace --query customerId -o tsv)
   
   WORKSPACE_KEY=$(az monitor log-analytics workspace get-shared-keys --resource-group monitoring-lab-rg --workspace-name monitoring-workspace --query primarySharedKey -o tsv)
   
   # Install Log Analytics agent
   az vm extension set \
     --resource-group monitoring-lab-rg \
     --vm-name monitoring-vm \
     --name MicrosoftMonitoringAgent \
     --publisher Microsoft.EnterpriseCloud.Monitoring \
     --protected-settings "{'workspaceKey': '$WORKSPACE_KEY'}" \
     --settings "{'workspaceId': '$WORKSPACE_ID'}" \
     --version 1.0
   ```

5. Create a web app to monitor with Application Insights:
   ```bash
   # Create App Service Plan
   az appservice plan create \
     --name monitoring-asp \
     --resource-group monitoring-lab-rg \
     --sku S1 \
     --location eastus
   
   # Create Web App
   az webapp create \
     --name monitoring-webapp-$RANDOM \
     --resource-group monitoring-lab-rg \
     --plan monitoring-asp
   ```

6. Create Application Insights resource:
   ```bash
   # Save the web app name to a variable
   WEBAPP_NAME=$(az webapp list --resource-group monitoring-lab-rg --query [0].name -o tsv)
   
   # Create Application Insights
   az monitor app-insights component create \
     --app monitoring-appinsights \
     --location eastus \
     --resource-group monitoring-lab-rg \
     --application-type web
   
   # Get the instrumentation key
   INSTRUMENTATION_KEY=$(az monitor app-insights component show \
     --app monitoring-appinsights \
     --resource-group monitoring-lab-rg \
     --query instrumentationKey -o tsv)
   
   # Configure the web app with Application Insights
   az webapp config appsettings set \
     --resource-group monitoring-lab-rg \
     --name $WEBAPP_NAME \
     --settings APPINSIGHTS_INSTRUMENTATIONKEY=$INSTRUMENTATION_KEY ApplicationInsightsAgent_EXTENSION_VERSION=~2
   ```

7. Configure VM Insights:
   ```bash
   # Enable VM insights solution
   az vm insights enable --resource-group monitoring-lab-rg --vm monitoring-vm
   ```

### Part 2: Configure Data Collection Rules

1. Navigate to the Azure Portal to configure data collection:
   - Go to your Log Analytics workspace
   - Click on "Agents configuration" in the left menu
   - Click on "Windows Performance Counters"
   - Add the following performance counters:
     - Processor(*)\% Processor Time
     - Memory(*)\Available MBytes
     - LogicalDisk(*)\Disk Reads/sec
     - LogicalDisk(*)\Disk Writes/sec
     - Network Adapter(*)\Bytes Received/sec
     - Network Adapter(*)\Bytes Sent/sec
   - Set the sample rate to 60 seconds
   - Click "Apply"

2. Configure Windows Event Logs collection:
   - In the same Agents configuration section, click on "Windows Event Logs"
   - Add the following event logs:
     - Application (Error, Warning)
     - System (Error, Warning, Critical)
     - Security (Audit Failure)
   - Click "Apply"

3. Enable Activity Log collection:
   - In the Azure Portal, go to "Monitor"
   - Click on "Activity log" in the left menu
   - Click "Diagnostic settings" at the top
   - Click "+ Add diagnostic setting"
   - Enter a name: "ActivityLogToWorkspace"
   - Check "Send to Log Analytics workspace"
   - Select your Log Analytics workspace
   - Check all log categories
   - Click "Save"

### Part 3: Create Custom Queries and Alerts

1. Create a custom log query to monitor VM CPU performance:
   - Go to your Log Analytics workspace
   - Click on "Logs" in the left menu
   - Run the following query:
   ```kusto
   Perf
   | where ObjectName == "Processor" and CounterName == "% Processor Time" and InstanceName == "_Total"
   | summarize avg(CounterValue) by bin(TimeGenerated, 15m), Computer
   | render timechart
   ```
   - Click "Save" and save as "VM CPU Performance"

2. Create a custom alert rule:
   - In the query window, click "New alert rule"
   - Configure the alert condition:
     - Threshold: Static
     - Operator: Greater than
     - Aggregation granularity (Period): 15 minutes
     - Aggregation type: Average
     - Threshold value: 80
   - Configure an action group:
     - Click "Create new" under Action groups
     - Name: "MonitoringAdmins"
     - Short name: "MonAdmin"
     - Select the subscription
     - Resource group: monitoring-lab-rg
     - Action group name: "Email Notification"
     - Action name: "Send email"
     - Action type: Email/SMS/Push/Voice
     - Configure email details (your email address)
     - Click "OK"
   - Set alert rule details:
     - Alert rule name: "High CPU Alert"
     - Description: "Alert when CPU exceeds 80% for 15 minutes"
     - Severity: 2 - Warning
   - Click "Create alert rule"

3. Create a heartbeat alert to detect offline VMs:
   - In the Log Analytics workspace, go to "Logs"
   - Run the following query:
   ```kusto
   Heartbeat
   | summarize TimeGenerated=max(TimeGenerated) by Computer
   | extend TimeFromNow = now() - TimeGenerated
   | where TimeFromNow > timespan(15m)
   ```
   - Click "New alert rule"
   - Set condition: Number of results greater than 0
   - Use the same action group as before
   - Alert rule name: "VM Heartbeat Alert"
   - Description: "Alert when a VM has not sent a heartbeat in 15 minutes"
   - Severity: 1 - Critical
   - Click "Create alert rule"

### Part 4: Create Workbooks and Dashboards

1. Create a workbook for VM performance:
   - Go to "Monitor" in the Azure Portal
   - Click on "Workbooks" in the left menu
   - Click "+ New"
   - Click "Edit" and remove the default content
   - Add a new query:
     - Click "+ Add query"
     - Use the following query:
     ```kusto
     Perf
     | where TimeGenerated > ago(1h)
     | where ObjectName == "Processor" or ObjectName == "Memory" or ObjectName like "LogicalDisk%"
     | where CounterName == "% Processor Time" or CounterName == "% Used Memory" or CounterName == "% Free Space"
     | summarize avg(CounterValue) by bin(TimeGenerated, 5m), Computer, ObjectName, CounterName
     | render timechart
     ```
     - Run the query and select the appropriate visualization (Line chart)
     - Click "Done Editing"
   - Add another query for network performance:
     - Click "+ Add query"
     - Use the following query:
     ```kusto
     Perf
     | where TimeGenerated > ago(1h)
     | where ObjectName == "Network Adapter"
     | where CounterName == "Bytes Received/sec" or CounterName == "Bytes Sent/sec"
     | summarize avg(CounterValue) by bin(TimeGenerated, 5m), Computer, CounterName
     | render timechart
     ```
     - Run the query and select Line chart visualization
     - Click "Done Editing"
   - Click "Done Editing" for the workbook
   - Click "Save"
   - Name: "VM Performance Workbook"
   - Save to: Shared Reports (subscription level)
   - Subscription: Select your subscription
   - Resource group: monitoring-lab-rg
   - Click "Apply"

2. Create a custom dashboard:
   - Go to the Azure Portal home
   - Click on "Dashboard" in the left menu
   - Click "+ New dashboard"
   - Select "Blank dashboard"
   - Add a title: "Azure Monitoring Dashboard"
   - Add tiles from the gallery:
     - Drag "Metrics chart" to the dashboard
     - Configure it to show VM CPU usage
     - Click "Done"
   - Add a Log Analytics query tile:
     - Drag "Log Analytics" to the dashboard
     - Select your workspace
     - Use the following query:
     ```kusto
     Perf
     | where ObjectName == "Memory" and CounterName == "Available MBytes"
     | summarize avg(CounterValue) by bin(TimeGenerated, 15m), Computer
     | render timechart
     ```
     - Set the title: "Memory Availability"
     - Click "Done"
   - Add your workbook:
     - Drag "Workbooks" to the dashboard
     - Select the workbook you created
     - Click "Done"
   - Add a Metrics Explorer tile:
     - Drag "Metrics chart" to the dashboard
     - Select your web app
     - Metric namespace: Web app metrics
     - Metric: Http 5xx
     - Aggregation: Sum
     - Click "Done"
   - Resize and arrange tiles as needed
   - Click "Save" to save your dashboard

### Part 5: Implement Automated Responses with Logic Apps

1. Create a Logic App for automated response:
   ```bash
   az logic-app create \
     --resource-group monitoring-lab-rg \
     --name monitoring-response \
     --location eastus
   ```

2. Configure the Logic App in the Azure Portal:
   - Go to the Logic App you created
   - Click "Logic app designer"
   - Select the template "When a HTTP request is received"
   - Use the following JSON schema for the request:
   ```json
   {
       "properties": {
           "data": {
               "properties": {
                   "essentials": {
                       "properties": {
                           "alertRule": {
                               "type": "string"
                           },
                           "alertTargetIDs": {
                               "items": {
                                   "type": "string"
                               },
                               "type": "array"
                           },
                           "severity": {
                               "type": "string"
                           }
                       },
                       "type": "object"
                   }
               },
               "type": "object"
           }
       },
       "type": "object"
   }
   ```

3. Add a condition step:
   - Click "+ New step"
   - Search for and select "Condition"
   - Set up the condition:
     - Choose "Expression" and enter: `contains(body('HTTP_Request')?['data']?['essentials']?['alertRule'], 'Heartbeat')`
     - This checks if the alert is related to VM heartbeat issues

4. For "If true" branch:
   - Click "Add an action"
   - Search for and select "Azure VM"
   - Select "Start VM (V2)"
   - Authenticate if prompted
   - Configure the action:
     - Subscription: Select your subscription
     - Resource Group: monitoring-lab-rg
     - Virtual Machine: monitoring-vm

5. For "If false" branch:
   - Click "Add an action"
   - Search for and select "Office 365 Outlook" or "Outlook.com"
   - Select "Send an email (V2)"
   - Authenticate with your email account
   - Configure the email:
     - To: Your email address
     - Subject: `Alert: @{body('HTTP_Request')?['data']?['essentials']?['alertRule']}`
     - Body: `There was an alert triggered for @{body('HTTP_Request')?['data']?['essentials']?['alertTargetIDs'][0]} with severity @{body('HTTP_Request')?['data']?['essentials']?['severity']}. Please investigate.`

6. Save the Logic App workflow:
   - Click "Save" at the top of the designer

7. Copy the HTTP Post URL:
   - After saving, the HTTP trigger will show a URL
   - Copy this URL to use in the alert action group

8. Update your action group to use the Logic App:
   - Go to "Monitor" in the Azure Portal
   - Click "Alerts" and then "Action groups"
   - Select the "MonitoringAdmins" action group
   - Click "+ Add action"
   - Action type: Logic App
   - Name: "Automated Response"
   - Select the Logic App you created
   - Click "OK"
   - Click "Save"

### Part 6: Test your Monitoring Solution

1. Generate test load on the VM:
   - Connect to the VM via RDP or Serial Console
   - Open PowerShell as Administrator
   - Run the following command to generate CPU load:
   ```powershell
   for ($i=0; $i -lt 10; $i++) { Start-Process -WindowStyle Hidden powershell -ArgumentList "while($true) {}" }
   ```
   - Let it run for a few minutes, then stop the processes:
   ```powershell
   Get-Process powershell | Where-Object { $_.CommandLine -eq "powershell -Command while($true) {}" } | Stop-Process
   ```

2. Generate test traffic to your web app:
   - Open PowerShell or Bash locally
   - Run the following command, replacing WEBAPP_NAME with your actual web app name:
   ```bash
   for i in {1..100}; do curl -s https://WEBAPP_NAME.azurewebsites.net/ > /dev/null; sleep 1; done
   ```

3. Simulate a VM heartbeat issue:
   - In the Azure Portal, stop the VM:
   ```bash
   az vm stop --resource-group monitoring-lab-rg --name monitoring-vm
   ```
   - Wait for the heartbeat alert to trigger
   - Verify if your Logic App executed and tried to restart the VM

4. Review monitoring data:
   - Check your custom queries in Log Analytics
   - View the Application Insights metrics for your web app
   - Open your custom dashboard to view all monitoring data
   - Check alert history to see which alerts were triggered

## Clean Up Resources

When you're done experimenting, delete the resource group to avoid incurring charges:

```bash
az group delete --name monitoring-lab-rg --yes
```

## Key Takeaways

- You've learned how to set up a comprehensive monitoring solution in Azure
- You've configured data collection from multiple sources (VMs, web apps, Azure platform)
- You've created custom queries and alerts for proactive monitoring
- You've implemented automated responses to monitoring events
- You've built visualization tools with workbooks and dashboards

## Next Steps

- Implement more advanced monitoring scenarios with Azure Monitor for specific services
- Set up monitoring for hybrid environments with Azure Arc
- Create custom metrics for your applications
- Implement more complex alert processing with Logic Apps
- Set up budget alerts for cost monitoring
- Implement availability testing for web applications