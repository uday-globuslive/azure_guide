# Landing Zone Implementation Project

## Architecture Overview

This project implements a comprehensive Azure Landing Zone following Microsoft's Cloud Adoption Framework (CAF) and Well-Architected Framework best practices. The architecture includes:

1. **Management Group Hierarchy**: Structured management groups for governance
2. **Hub-Spoke Network Topology**: Centralized connectivity and security controls
3. **Identity and Access Management**: Azure AD and RBAC implementation
4. **Security Controls**: Defense-in-depth security architecture
5. **Governance Framework**: Policy implementation and compliance monitoring
6. **Operations Management**: Monitoring, logging, and alerting
7. **Automation Framework**: Infrastructure as Code (IaC) and CI/CD pipelines

![Architecture Diagram](architecture.png)

## Business Requirements

A fictional large enterprise, Contoso Corporation, needs a secure, compliant, and scalable Azure environment with the following requirements:

1. Support for multiple business units with delegated administration
2. Centralized security and governance controls
3. Standardized environment configurations
4. Hybrid connectivity to on-premises datacenters
5. Compliance with industry regulations (ISO 27001, GDPR, HIPAA)
6. Cost management and optimization
7. Automated deployment and operational processes
8. Comprehensive monitoring and security posture management

## Implementation Guide

### Part 1: Management Group Hierarchy

1. Create Management Group Structure:
   ```bash
   # Create top-level management group
   az account management-group create --name "Contoso" --display-name "Contoso Corporation"
   
   # Create platform management group
   az account management-group create --name "platform" --display-name "Platform" --parent "Contoso"
   
   # Create platform sub-management groups
   az account management-group create --name "platform-connectivity" --display-name "Connectivity" --parent "platform"
   az account management-group create --name "platform-identity" --display-name "Identity" --parent "platform"
   az account management-group create --name "platform-management" --display-name "Management" --parent "platform"
   
   # Create landing zones management group
   az account management-group create --name "landingzones" --display-name "Landing Zones" --parent "Contoso"
   
   # Create landing zones sub-management groups
   az account management-group create --name "lz-corp" --display-name "Corporate" --parent "landingzones"
   az account management-group create --name "lz-online" --display-name "Online" --parent "landingzones"
   az account management-group create --name "lz-confidential" --display-name "Confidential" --parent "landingzones"
   
   # Create sandbox and decommissioned management groups
   az account management-group create --name "sandbox" --display-name "Sandbox" --parent "Contoso"
   az account management-group create --name "decommissioned" --display-name "Decommissioned" --parent "Contoso"
   ```

2. Create Subscriptions and assign to Management Groups:
   ```bash
   # Note: Subscription creation requires EA or MCA account
   # Example of moving existing subscriptions
   
   # Move connectivity subscription
   az account management-group subscription add --name "platform-connectivity" --subscription "00000000-0000-0000-0000-000000000001"
   
   # Move identity subscription
   az account management-group subscription add --name "platform-identity" --subscription "00000000-0000-0000-0000-000000000002"
   
   # Move management subscription
   az account management-group subscription add --name "platform-management" --subscription "00000000-0000-0000-0000-000000000003"
   
   # Move corporate landing zone subscriptions
   az account management-group subscription add --name "lz-corp" --subscription "00000000-0000-0000-0000-000000000004"
   az account management-group subscription add --name "lz-corp" --subscription "00000000-0000-0000-0000-000000000005"
   
   # Move online landing zone subscription
   az account management-group subscription add --name "lz-online" --subscription "00000000-0000-0000-0000-000000000006"
   
   # Move confidential landing zone subscription
   az account management-group subscription add --name "lz-confidential" --subscription "00000000-0000-0000-0000-000000000007"
   ```

### Part 2: Hub-Spoke Network Implementation

1. Create Hub Virtual Network in the connectivity subscription:
   ```bash
   # Set connectivity subscription
   az account set --subscription "00000000-0000-0000-0000-000000000001"
   
   # Create resource group for hub network
   az group create --name rg-connectivity-hub --location eastus
   
   # Create hub virtual network
   az network vnet create \
     --name vnet-hub-eastus \
     --resource-group rg-connectivity-hub \
     --location eastus \
     --address-prefixes 10.0.0.0/16
   
   # Create subnets in hub network
   az network vnet subnet create \
     --name GatewaySubnet \
     --resource-group rg-connectivity-hub \
     --vnet-name vnet-hub-eastus \
     --address-prefixes 10.0.0.0/24
   
   az network vnet subnet create \
     --name AzureFirewallSubnet \
     --resource-group rg-connectivity-hub \
     --vnet-name vnet-hub-eastus \
     --address-prefixes 10.0.1.0/24
   
   az network vnet subnet create \
     --name BastionSubnet \
     --resource-group rg-connectivity-hub \
     --vnet-name vnet-hub-eastus \
     --address-prefixes 10.0.2.0/24
   
   az network vnet subnet create \
     --name JumpboxSubnet \
     --resource-group rg-connectivity-hub \
     --vnet-name vnet-hub-eastus \
     --address-prefixes 10.0.3.0/24
   ```

2. Create Azure Firewall and Bastion in the hub:
   ```bash
   # Create public IP for Firewall
   az network public-ip create \
     --name pip-fw-eastus \
     --resource-group rg-connectivity-hub \
     --location eastus \
     --allocation-method Static \
     --sku Standard
   
   # Create Firewall
   az network firewall create \
     --name fw-hub-eastus \
     --resource-group rg-connectivity-hub \
     --location eastus
   
   # Configure Firewall IP Config
   az network firewall ip-config create \
     --firewall-name fw-hub-eastus \
     --name fw-config \
     --public-ip-address pip-fw-eastus \
     --resource-group rg-connectivity-hub \
     --vnet-name vnet-hub-eastus
   
   # Create Firewall Policy
   az network firewall policy create \
     --name fw-policy-eastus \
     --resource-group rg-connectivity-hub \
     --location eastus
   
   # Associate policy with firewall
   az network firewall update \
     --name fw-hub-eastus \
     --resource-group rg-connectivity-hub \
     --firewall-policy fw-policy-eastus
   
   # Create Bastion public IP
   az network public-ip create \
     --name pip-bastion-eastus \
     --resource-group rg-connectivity-hub \
     --location eastus \
     --allocation-method Static \
     --sku Standard
   
   # Create Bastion Host
   az network bastion create \
     --name bastion-hub-eastus \
     --public-ip-address pip-bastion-eastus \
     --resource-group rg-connectivity-hub \
     --vnet-name vnet-hub-eastus \
     --location eastus
   ```

3. Create VPN Gateway for hybrid connectivity:
   ```bash
   # Create public IP for VPN Gateway
   az network public-ip create \
     --name pip-vpn-eastus \
     --resource-group rg-connectivity-hub \
     --location eastus \
     --allocation-method Dynamic
   
   # Create VPN Gateway
   az network vnet-gateway create \
     --name vpn-hub-eastus \
     --resource-group rg-connectivity-hub \
     --location eastus \
     --vnet vnet-hub-eastus \
     --gateway-type Vpn \
     --vpn-type RouteBased \
     --sku VpnGw1 \
     --public-ip-address pip-vpn-eastus \
     --no-wait
   ```

4. Create Corporate Spoke Network in a landing zone subscription:
   ```bash
   # Set corporate landing zone subscription
   az account set --subscription "00000000-0000-0000-0000-000000000004"
   
   # Create resource group for corporate spoke
   az group create --name rg-corp-prod-network --location eastus
   
   # Create corporate spoke virtual network
   az network vnet create \
     --name vnet-corp-prod-eastus \
     --resource-group rg-corp-prod-network \
     --location eastus \
     --address-prefixes 10.1.0.0/16
   
   # Create subnets in corporate spoke
   az network vnet subnet create \
     --name snet-app \
     --resource-group rg-corp-prod-network \
     --vnet-name vnet-corp-prod-eastus \
     --address-prefixes 10.1.0.0/24
   
   az network vnet subnet create \
     --name snet-db \
     --resource-group rg-corp-prod-network \
     --vnet-name vnet-corp-prod-eastus \
     --address-prefixes 10.1.1.0/24
   
   az network vnet subnet create \
     --name snet-shared \
     --resource-group rg-corp-prod-network \
     --vnet-name vnet-corp-prod-eastus \
     --address-prefixes 10.1.2.0/24
   ```

5. Set up Virtual Network Peering:
   ```bash
   # Get the resource IDs of both VNets
   HUB_VNET_ID=$(az network vnet show --resource-group rg-connectivity-hub --name vnet-hub-eastus --query id -o tsv)
   
   SPOKE_VNET_ID=$(az network vnet show --resource-group rg-corp-prod-network --name vnet-corp-prod-eastus --query id -o tsv)
   
   # Create peering from hub to spoke
   az account set --subscription "00000000-0000-0000-0000-000000000001"
   
   az network vnet peering create \
     --name hub-to-corp-prod \
     --resource-group rg-connectivity-hub \
     --vnet-name vnet-hub-eastus \
     --remote-vnet $SPOKE_VNET_ID \
     --allow-vnet-access \
     --allow-forwarded-traffic \
     --allow-gateway-transit
   
   # Create peering from spoke to hub
   az account set --subscription "00000000-0000-0000-0000-000000000004"
   
   az network vnet peering create \
     --name corp-prod-to-hub \
     --resource-group rg-corp-prod-network \
     --vnet-name vnet-corp-prod-eastus \
     --remote-vnet $HUB_VNET_ID \
     --allow-vnet-access \
     --allow-forwarded-traffic \
     --use-remote-gateways
   ```

6. Configure Route Tables for centralized egress through Azure Firewall:
   ```bash
   # Get Firewall private IP
   az account set --subscription "00000000-0000-0000-0000-000000000001"
   FW_PRIVATE_IP=$(az network firewall show --name fw-hub-eastus --resource-group rg-connectivity-hub --query "ipConfigurations[0].privateIpAddress" -o tsv)
   
   # Create route table in corporate landing zone
   az account set --subscription "00000000-0000-0000-0000-000000000004"
   
   az network route-table create \
     --name rt-corp-prod-to-hub \
     --resource-group rg-corp-prod-network \
     --location eastus
   
   # Add route for internet traffic via Firewall
   az network route-table route create \
     --route-table-name rt-corp-prod-to-hub \
     --resource-group rg-corp-prod-network \
     --name to-internet \
     --address-prefix 0.0.0.0/0 \
     --next-hop-type VirtualAppliance \
     --next-hop-ip-address $FW_PRIVATE_IP
   
   # Associate route table with subnets
   az network vnet subnet update \
     --resource-group rg-corp-prod-network \
     --vnet-name vnet-corp-prod-eastus \
     --name snet-app \
     --route-table rt-corp-prod-to-hub
   
   az network vnet subnet update \
     --resource-group rg-corp-prod-network \
     --vnet-name vnet-corp-prod-eastus \
     --name snet-db \
     --route-table rt-corp-prod-to-hub
   
   az network vnet subnet update \
     --resource-group rg-corp-prod-network \
     --vnet-name vnet-corp-prod-eastus \
     --name snet-shared \
     --route-table rt-corp-prod-to-hub
   ```

### Part 3: Identity and Access Management

1. Set up Custom RBAC roles:
   ```bash
   # Create custom Network Operations role definition
   cat > network-operator-role.json << EOF
   {
     "Name": "Network Operations",
     "Description": "Can monitor and operate network resources",
     "Actions": [
       "Microsoft.Network/virtualNetworks/read",
       "Microsoft.Network/virtualNetworks/subnets/read",
       "Microsoft.Network/routeTables/read",
       "Microsoft.Network/routeTables/routes/read",
       "Microsoft.Network/networkSecurityGroups/read",
       "Microsoft.Network/networkWatchers/connectionMonitors/*",
       "Microsoft.Network/networkWatchers/flowLogs/*",
       "Microsoft.Network/networkInterfaces/read",
       "Microsoft.Resources/subscriptions/resourceGroups/read"
     ],
     "NotActions": [],
     "AssignableScopes": [
       "/providers/Microsoft.Management/managementGroups/platform-connectivity"
     ]
   }
   EOF
   
   az role definition create --role-definition network-operator-role.json
   ```

2. Create Azure AD Groups for RBAC:
   ```bash
   # Note: Azure AD operations require appropriate permissions
   # Example using Microsoft Graph API
   
   # Create Azure AD Security Groups (requires Microsoft Graph PowerShell)
   # Here is PowerShell example:
   # New-MgGroup -DisplayName "Connectivity Owners" -SecurityEnabled -MailEnabled:$false -MailNickname "ConnectivityOwners"
   # New-MgGroup -DisplayName "Network Operations" -SecurityEnabled -MailEnabled:$false -MailNickname "NetworkOperations"
   ```

3. Assign roles to groups:
   ```bash
   # Assign Owner role to Connectivity Owners group at connectivity management group
   CONNECTIVITY_OWNERS_ID="00000000-0000-0000-0000-000000000010" # Replace with actual group ID
   
   az role assignment create \
     --role Owner \
     --assignee-object-id $CONNECTIVITY_OWNERS_ID \
     --assignee-principal-type Group \
     --scope /providers/Microsoft.Management/managementGroups/platform-connectivity
   
   # Assign custom Network Operations role to Network Operations group
   NETWORK_OPS_ID="00000000-0000-0000-0000-000000000011" # Replace with actual group ID
   
   az role assignment create \
     --role "Network Operations" \
     --assignee-object-id $NETWORK_OPS_ID \
     --assignee-principal-type Group \
     --scope /providers/Microsoft.Management/managementGroups/platform-connectivity
   ```

4. Configure Privileged Identity Management (PIM):
   ```
   # Note: PIM configuration is typically done through Azure Portal or Microsoft Graph API
   # This requires Azure AD Premium P2 licenses
   ```

### Part 4: Security Controls Implementation

1. Create Azure Security Center workspace:
   ```bash
   # Set management subscription
   az account set --subscription "00000000-0000-0000-0000-000000000003"
   
   # Create resource group for management services
   az group create --name rg-security-eastus --location eastus
   
   # Create Log Analytics workspace
   az monitor log-analytics workspace create \
     --name law-security-eastus \
     --resource-group rg-security-eastus \
     --location eastus \
     --sku PerGB2018
   ```

2. Enable Microsoft Defender for Cloud:
   ```bash
   # Get workspace resource ID
   WORKSPACE_ID=$(az monitor log-analytics workspace show --resource-group rg-security-eastus --workspace-name law-security-eastus --query id -o tsv)
   
   # Enable Security Center at management group level
   az security auto-provisioning-setting update --name default --auto-provision On
   
   # Set workspace for Defender for Cloud
   az security workspace-setting create \
     --name default \
     --target-workspace $WORKSPACE_ID
   
   # Enable Defender for Cloud plans at management group level
   # Note: This requires Azure PowerShell
   # Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'
   # Set-AzSecurityPricing -Name 'VirtualMachines' -PricingTier 'Standard'
   # Set-AzSecurityPricing -Name 'SqlServers' -PricingTier 'Standard'
   # Set-AzSecurityPricing -Name 'AppServices' -PricingTier 'Standard'
   # Set-AzSecurityPricing -Name 'StorageAccounts' -PricingTier 'Standard'
   # Set-AzSecurityPricing -Name 'KeyVaults' -PricingTier 'Standard'
   # Set-AzSecurityPricing -Name 'KubernetesService' -PricingTier 'Standard'
   # Set-AzSecurityPricing -Name 'ContainerRegistry' -PricingTier 'Standard'
   ```

3. Create Azure Key Vault in the management subscription:
   ```bash
   az keyvault create \
     --name kv-contoso-eastus \
     --resource-group rg-security-eastus \
     --location eastus \
     --sku Premium \
     --enabled-for-disk-encryption true \
     --enabled-for-deployment true \
     --enabled-for-template-deployment true
   ```

4. Configure network and monitoring settings:
   ```bash
   # Configure diagnostic settings for Key Vault
   az monitor diagnostic-settings create \
     --name kv-diagnostics \
     --resource $(az keyvault show --name kv-contoso-eastus --resource-group rg-security-eastus --query id -o tsv) \
     --workspace $(az monitor log-analytics workspace show --resource-group rg-security-eastus --workspace-name law-security-eastus --query id -o tsv) \
     --logs '[{"category": "AuditEvent","enabled": true}]' \
     --metrics '[{"category": "AllMetrics","enabled": true}]'
   ```

### Part 5: Governance Framework

1. Create Azure Policy definitions and initiatives:
   ```bash
   # Create a custom policy definition for requiring tags
   cat > require-department-tag.json << EOF
   {
     "properties": {
       "displayName": "Require department tag on resources",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "Requires a 'department' tag on resources",
       "parameters": {
         "effect": {
           "type": "String",
           "defaultValue": "Deny",
           "allowedValues": ["Audit", "Deny", "Disabled"]
         }
       },
       "policyRule": {
         "if": {
           "allOf": [
             {
               "field": "type",
               "notEquals": "Microsoft.Resources/resourceGroups"
             },
             {
               "field": "tags['department']",
               "exists": "false"
             }
           ]
         },
         "then": {
           "effect": "[parameters('effect')]"
         }
       }
     }
   }
   EOF
   
   az policy definition create \
     --name 'require-department-tag' \
     --display-name 'Require department tag on resources' \
     --description 'Requires a department tag on all resources' \
     --rules require-department-tag.json \
     --mode Indexed \
     --management-group Contoso
   
   # Create a policy initiative (set definition)
   cat > governance-initiative.json << EOF
   {
     "properties": {
       "displayName": "Contoso Governance Initiative",
       "description": "Initiative enforcing Contoso governance requirements",
       "metadata": {
         "category": "Governance"
       },
       "parameters": {
         "tagEffect": {
           "type": "String",
           "defaultValue": "Deny",
           "allowedValues": ["Audit", "Deny", "Disabled"]
         }
       },
       "policyDefinitions": [
         {
           "policyDefinitionId": "/providers/Microsoft.Management/managementGroups/Contoso/providers/Microsoft.Authorization/policyDefinitions/require-department-tag",
           "parameters": {
             "effect": {
               "value": "[parameters('tagEffect')]"
             }
           }
         },
         {
           "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/8effc8c7-44dd-450b-a250-7fe71fe1aad2",
           "parameters": {}
         }
       ]
     }
   }
   EOF
   
   az policy set-definition create \
     --name 'contoso-governance-initiative' \
     --display-name 'Contoso Governance Initiative' \
     --description 'Initiative enforcing Contoso governance requirements' \
     --definitions governance-initiative.json \
     --management-group Contoso
   
   # Assign initiative to landing zones management group
   az policy assignment create \
     --name 'governance-initiative-assignment' \
     --display-name 'Governance Initiative Assignment' \
     --policy-set-definition 'contoso-governance-initiative' \
     --params '{"tagEffect": {"value": "Deny"}}' \
     --scope /providers/Microsoft.Management/managementGroups/landingzones
   ```

2. Create a blueprint (using Azure Portal or API):
   ```
   # Note: Azure Blueprints is being replaced by Bicep templates
   # Create a Bicep template for standardized landing zone deployment
   ```

3. Configure Azure Cost Management:
   ```bash
   # Set management subscription
   az account set --subscription "00000000-0000-0000-0000-000000000003"
   
   # Create budget for corporate landing zone subscriptions
   # Replace with actual subscription IDs
   CORP_SUB_IDS=$(echo '["00000000-0000-0000-0000-000000000004", "00000000-0000-0000-0000-000000000005"]')
   
   az consumption budget create \
     --budget-name "Corporate-Departments-Budget" \
     --category cost \
     --amount 10000 \
     --time-grain monthly \
     --start-date $(date -u +"%Y-%m-01T00:00:00Z") \
     --subscription-filter $CORP_SUB_IDS \
     --notification "Actual_GreaterThan_80_Percent" \
     --notification-enabled true \
     --notification-operator "GreaterThan" \
     --notification-threshold 80 \
     --contact-emails "finance@contoso.com" \
     --contact-roles "Owner"
   ```

### Part 6: Operations Management

1. Create centralized monitoring in the management subscription:
   ```bash
   # Set management subscription
   az account set --subscription "00000000-0000-0000-0000-000000000003"
   
   # Create resource group
   az group create --name rg-monitoring-eastus --location eastus
   
   # Create Log Analytics workspace
   az monitor log-analytics workspace create \
     --name law-platform-eastus \
     --resource-group rg-monitoring-eastus \
     --location eastus \
     --sku PerGB2018
   
   # Get workspace resource ID
   WORKSPACE_ID=$(az monitor log-analytics workspace show --resource-group rg-monitoring-eastus --workspace-name law-platform-eastus --query id -o tsv)
   ```

2. Configure diagnostic settings for hub network resources:
   ```bash
   # Set connectivity subscription
   az account set --subscription "00000000-0000-0000-0000-000000000001"
   
   # Configure diagnostic settings for hub VNet
   az monitor diagnostic-settings create \
     --name hub-vnet-diagnostics \
     --resource $(az network vnet show --resource-group rg-connectivity-hub --name vnet-hub-eastus --query id -o tsv) \
     --workspace $WORKSPACE_ID \
     --logs '[{"category": "VMProtectionAlerts","enabled": true}]' \
     --metrics '[{"category": "AllMetrics","enabled": true}]'
   
   # Configure diagnostic settings for Azure Firewall
   az monitor diagnostic-settings create \
     --name hub-fw-diagnostics \
     --resource $(az network firewall show --name fw-hub-eastus --resource-group rg-connectivity-hub --query id -o tsv) \
     --workspace $WORKSPACE_ID \
     --logs '[{"category": "AzureFirewallApplicationRule","enabled": true}, {"category": "AzureFirewallNetworkRule","enabled": true}, {"category": "AzureFirewallDnsProxy","enabled": true}]' \
     --metrics '[{"category": "AllMetrics","enabled": true}]'
   ```

3. Create monitoring dashboards and alerts:
   ```bash
   # Create action group for platform team
   az monitor action-group create \
     --name ag-platform-critical \
     --resource-group rg-monitoring-eastus \
     --action email-receiver \
     --short-name platform \
     --email-receiver-name PlatformTeam \
     --email-receiver-email-address "platform-team@contoso.com"
   
   # Create alert for Azure Firewall health
   az monitor metrics alert create \
     --name "Azure Firewall Health Alert" \
     --resource-group rg-monitoring-eastus \
     --scopes $(az network firewall show --name fw-hub-eastus --resource-group rg-connectivity-hub --query id -o tsv) \
     --condition "avg FirewallHealth < 100" \
     --window-size 5m \
     --evaluation-frequency 1m \
     --severity 1 \
     --description "Alert when Azure Firewall health drops below 100%" \
     --action "/subscriptions/00000000-0000-0000-0000-000000000003/resourceGroups/rg-monitoring-eastus/providers/microsoft.insights/actionGroups/ag-platform-critical"
   ```

4. Set up Azure Automation for operational tasks:
   ```bash
   # Create Automation Account
   az automation account create \
     --name aa-platform-eastus \
     --resource-group rg-monitoring-eastus \
     --location eastus \
     --sku Free
   
   # Create runbook for VM startup/shutdown
   # Would upload runbook .ps1 file or create via API
   ```

### Part 7: Automation Framework

1. Create Azure DevOps project for IaC:
   ```
   # Note: This is typically done via Azure DevOps portal or API
   ```

2. Set up CI/CD pipeline:
   ```yaml
   # Example pipeline YAML for deploying landing zone resources
   
   trigger:
     branches:
       include:
         - main
     paths:
       include:
         - 'infra/landingzones/**'
   
   variables:
     - group: 'LandingZone-Variables'
   
   stages:
     - stage: Validate
       jobs:
         - job: ValidateTemplates
           pool:
             vmImage: 'ubuntu-latest'
           steps:
             - task: AzureCLI@2
               inputs:
                 azureSubscription: 'Management-ServiceConnection'
                 scriptType: 'bash'
                 scriptLocation: 'inlineScript'
                 inlineScript: |
                   az deployment sub validate \
                     --location eastus \
                     --template-file infra/landingzones/main.bicep \
                     --parameters @infra/landingzones/parameters/dev.parameters.json
   
     - stage: DeployDev
       dependsOn: Validate
       jobs:
         - deployment: DeployLandingZone
           environment: 'dev'
           pool:
             vmImage: 'ubuntu-latest'
           strategy:
             runOnce:
               deploy:
                 steps:
                   - task: AzureCLI@2
                     inputs:
                       azureSubscription: 'Management-ServiceConnection'
                       scriptType: 'bash'
                       scriptLocation: 'inlineScript'
                       inlineScript: |
                         az deployment sub create \
                           --location eastus \
                           --template-file infra/landingzones/main.bicep \
                           --parameters @infra/landingzones/parameters/dev.parameters.json
   ```

## Infrastructure as Code

The entire landing zone is described using Bicep templates. The key template files include:

1. [managementGroups.bicep](deploy/managementGroups.bicep) - Management group hierarchy
2. [hubNetwork.bicep](deploy/hubNetwork.bicep) - Hub networking components
3. [spokeNetwork.bicep](deploy/spokeNetwork.bicep) - Spoke networking template
4. [policy.bicep](deploy/policy.bicep) - Policy definitions and assignments
5. [monitoring.bicep](deploy/monitoring.bicep) - Monitoring resources
6. [security.bicep](deploy/security.bicep) - Security components
7. [landingZone.bicep](deploy/landingZone.bicep) - Landing zone deployment

Deploy the landing zone using these templates:
```bash
az deployment sub create --name contoso-landing-zone --location eastus --template-file deploy/main.bicep --parameters param-main.json
```

## Landing Zone Reference Implementation

For real-world deployments, consider using Microsoft's Enterprise-Scale Landing Zone reference implementation:

```bash
# Clone the Enterprise-Scale repository
git clone https://github.com/Azure/Enterprise-Scale.git

# Deploy using the reference implementation
cd Enterprise-Scale
./.github/workflows/es-foundation.yml
```

## Governance Documentation

### RBAC Model

The landing zone implements a comprehensive RBAC model:

1. **Subscription Owner Role**: Limited to select administrators
2. **Subscription Contributor Role**: For deployment pipelines
3. **Custom Roles**:
   - Network Operator
   - Security Operator
   - Cost Management
   - Application Owner

Access to production environments requires:
- Just-in-time access
- MFA enforcement
- Role elevation audit logging

### Policy Framework

Policies are implemented at different management group levels:

1. **Contoso (Root) Level**:
   - Resource location restriction
   - Allowed resource types

2. **Platform Level**:
   - Security baseline
   - Network security enforcement

3. **Landing Zone Level**:
   - Tagging requirements
   - Cost optimization
   - Workload-specific compliance

### Resource Naming Standards

All resources follow the naming convention:
`<resource-type>-<workload>-<environment>-<region>-<instance>`

Examples:
- `vnet-hub-prod-eastus`
- `vm-sql-dev-eastus-001`
- `kv-shared-prod-eastus`

### Resource Tagging Standards

Required tags:
- `Environment`: (Prod, Dev, Test, QA)
- `Department`: (Finance, HR, Marketing, IT)
- `CostCenter`: Financial cost center code
- `Application`: Application name
- `Owner`: Team or individual responsible

## Security Controls

### Network Security

1. **Perimeter Security**:
   - Azure Firewall in hub network
   - DDoS Protection
   - Web Application Firewall for web workloads

2. **Network Segmentation**:
   - Hub-spoke topology
   - NSGs on all subnets
   - Service endpoints and Private Link where applicable
   - Micro-segmentation within landing zones

3. **Traffic Inspection**:
   - TLS inspection for outbound traffic
   - Centralized egress through Azure Firewall
   - Traffic Analytics for network monitoring

### Identity Security

1. **Authentication**:
   - Azure AD for identity management
   - Multi-factor authentication enforcement
   - Conditional Access policies
   - Password policies and monitoring

2. **Authorization**:
   - RBAC with least privilege principle
   - Privileged Identity Management
   - Just-in-time access
   - Custom roles for specific functions

### Data Protection

1. **Encryption**:
   - Encryption at rest for all storage
   - Encryption in transit enforced
   - Customer-managed keys for sensitive data
   - Key rotation policies

2. **Data Classification**:
   - Automated data classification
   - Information protection labels
   - Data Loss Prevention policies
   - Activity monitoring

## Operations Management

### Monitoring Strategy

1. **Log Collection**:
   - Centralized Log Analytics workspace
   - Platform logs from all resources
   - Application logs from workloads
   - Security logs from Azure AD and resources

2. **Alert Framework**:
   - Severity-based alerting
   - Tiered response procedures
   - Automated remediation where possible
   - Integration with ITSM systems

3. **Dashboards and Visualization**:
   - Executive dashboards for overall health
   - Operational dashboards for day-to-day management
   - Security dashboards for compliance and posture
   - Cost dashboards for financial management

### Automation Framework

1. **Infrastructure Deployment**:
   - CI/CD pipelines for infrastructure
   - Git-based deployment workflows
   - Approval gates for production changes
   - Automated testing of infrastructure

2. **Operational Tasks**:
   - Automated backup verification
   - Scheduled scaling operations
   - Compliance scanning and reporting
   - Cost optimization recommendations

## Cost Management

1. **Budgeting and Allocation**:
   - Subscription-level budgets
   - Department-level cost allocation
   - Consumption monitoring
   - Anomaly detection

2. **Optimization Strategies**:
   - Right-sizing recommendations
   - Reserved Instances for predictable workloads
   - Auto-shutdown for dev/test environments
   - Storage lifecycle management

## Next Steps

1. Implement multi-region deployment for disaster recovery
2. Add Azure Sentinel for advanced security monitoring
3. Set up hybrid operations with Azure Arc
4. Implement regulatory compliance controls for specific industries
5. Develop custom landing zone templates for different workload archetypes