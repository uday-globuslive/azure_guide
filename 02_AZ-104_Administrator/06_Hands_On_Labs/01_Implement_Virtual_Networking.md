# Hands-On Lab: Implementing Virtual Networks in Azure

## Objective
Design and implement a hub and spoke network topology with security controls, peering connections, and proper routing.

## Prerequisites
- An Azure account with an active subscription
- Azure PowerShell or Azure CLI installed
- Understanding of basic networking concepts
- Completion of AZ-900 labs

## Estimated Time
1.5-2 hours

## Architecture Overview

In this lab, you will create the following:
- A hub virtual network with gateway subnet, firewall subnet, and management subnet
- Two spoke virtual networks for workloads
- Virtual network peering between hub and spokes
- Network security groups to control traffic
- A basic Azure Firewall deployment
- User-defined routes for controlling traffic flow
- Test virtual machines to validate connectivity

## Steps

### Part 1: Create the Hub Virtual Network

1. Sign in to the Azure Portal:
   ```
   https://portal.azure.com
   ```

2. Create a resource group for networking resources:
   - Name: `NetworkingRG`
   - Region: Select a region suitable for your location

3. Create the hub virtual network:
   - Name: `Hub-VNet`
   - Address space: `10.0.0.0/16`
   - Resource group: `NetworkingRG`
   - Location: Same as resource group

4. Create the following subnets in the hub virtual network:
   - GatewaySubnet: `10.0.0.0/24`
   - AzureFirewallSubnet: `10.0.1.0/24`
   - ManagementSubnet: `10.0.2.0/24`
   - JumpboxSubnet: `10.0.3.0/24`

### Part 2: Create Spoke Virtual Networks

1. Create the first spoke virtual network:
   - Name: `Spoke1-VNet`
   - Address space: `10.1.0.0/16`
   - Resource group: `NetworkingRG`
   - Location: Same as resource group
   - Subnets:
     - WorkloadSubnet1: `10.1.0.0/24`
     - WorkloadSubnet2: `10.1.1.0/24`

2. Create the second spoke virtual network:
   - Name: `Spoke2-VNet`
   - Address space: `10.2.0.0/16`
   - Resource group: `NetworkingRG`
   - Location: Same as resource group
   - Subnets:
     - WorkloadSubnet1: `10.2.0.0/24`
     - WorkloadSubnet2: `10.2.1.0/24`

### Part 3: Configure Virtual Network Peering

1. Set up peering from Hub to Spoke1:
   - Navigate to the Hub-VNet
   - Select "Peerings" from the left menu
   - Click "+ Add"
   - Name: `Hub-to-Spoke1`
   - Virtual network: `Spoke1-VNet`
   - Configure the following settings:
     - Allow virtual network access: Enabled
     - Allow forwarded traffic: Enabled
     - Allow gateway transit: Enabled

2. Set up peering from Spoke1 to Hub:
   - Navigate to the Spoke1-VNet
   - Select "Peerings" from the left menu
   - Click "+ Add"
   - Name: `Spoke1-to-Hub`
   - Virtual network: `Hub-VNet`
   - Configure the following settings:
     - Allow virtual network access: Enabled
     - Allow forwarded traffic: Enabled
     - Use remote gateways: Enabled

3. Repeat the process to create peering connections between Hub and Spoke2.

### Part 4: Create Network Security Groups

1. Create a Network Security Group for the management subnet:
   - Name: `Management-NSG`
   - Resource group: `NetworkingRG`
   - Add inbound security rules:
     - Rule 1:
       - Name: `Allow-RDP-In`
       - Priority: 100
       - Source: Your IP address or trusted range
       - Destination: ManagementSubnet address prefix
       - Service: RDP
       - Action: Allow

2. Create a Network Security Group for the workload subnets:
   - Name: `Workload-NSG`
   - Resource group: `NetworkingRG`
   - Add inbound security rules:
     - Rule 1:
       - Name: `Allow-HTTP-In`
       - Priority: 100
       - Source: VirtualNetwork
       - Destination: VirtualNetwork
       - Service: HTTP
       - Action: Allow
     - Rule 2:
       - Name: `Deny-Internet-In`
       - Priority: 4000
       - Source: Internet
       - Destination: VirtualNetwork
       - Service: Any
       - Action: Deny

3. Associate the NSGs with the appropriate subnets:
   - Associate Management-NSG with ManagementSubnet in Hub-VNet
   - Associate Workload-NSG with WorkloadSubnet1 and WorkloadSubnet2 in both spoke VNets

### Part 5: Deploy Azure Firewall

1. Create a public IP address for the firewall:
   - Name: `Firewall-PIP`
   - SKU: Standard
   - Assignment: Static

2. Deploy Azure Firewall:
   - Name: `Hub-Firewall`
   - Resource group: `NetworkingRG`
   - Firewall policy: Create new
     - Name: `Hub-Firewall-Policy`
   - Virtual network: `Hub-VNet`
   - Public IP address: `Firewall-PIP`

3. Configure firewall network rules:
   - Navigate to the `Hub-Firewall-Policy`
   - Create a rule collection group:
     - Name: `NetworkRuleCollectionGroup`
     - Priority: 200
   - Add a rule collection:
     - Name: `AllowedNetworkRules`
     - Rule collection type: Network
     - Priority: 100
     - Action: Allow
   - Add rules:
     - Rule 1:
       - Name: `AllowAnyHTTP`
       - Source type: IP Address
       - Source: `10.0.0.0/8`
       - Protocol: TCP
       - Destination Ports: 80
       - Destination Type: IP Address
       - Destination: `*`
     - Rule 2:
       - Name: `AllowAnyHTTPS`
       - Source type: IP Address
       - Source: `10.0.0.0/8`
       - Protocol: TCP
       - Destination Ports: 443
       - Destination Type: IP Address
       - Destination: `*`

### Part 6: Configure User-Defined Routes

1. Create a route table for the spoke networks:
   - Name: `Spoke-Route-Table`
   - Resource group: `NetworkingRG`
   - Propagate gateway routes: No

2. Add a route to the table:
   - Route name: `ToInternet`
   - Address prefix: `0.0.0.0/0`
   - Next hop type: Virtual appliance
   - Next hop address: IP address of the Azure Firewall private IP

3. Associate the route table with workload subnets:
   - Navigate to each WorkloadSubnet in Spoke1-VNet and Spoke2-VNet
   - Go to the "Route table" section
   - Select `Spoke-Route-Table`
   - Save the changes

### Part 7: Deploy Test Virtual Machines

1. Create a jumpbox VM in the hub network:
   - Name: `Hub-Jumpbox`
   - Resource group: `NetworkingRG`
   - Region: Same as virtual networks
   - Image: Windows Server 2019 Datacenter
   - Size: Standard_D2s_v3
   - Username and password: Create secure credentials
   - Virtual network: `Hub-VNet`
   - Subnet: `JumpboxSubnet`
   - Public IP: Create new
   - NSG: Create new with RDP allowed

2. Create a test VM in Spoke1:
   - Name: `Spoke1-VM`
   - Resource group: `NetworkingRG`
   - Region: Same as virtual networks
   - Image: Windows Server 2019 Datacenter
   - Size: Standard_D2s_v3
   - Username and password: Same as jumpbox
   - Virtual network: `Spoke1-VNet`
   - Subnet: `WorkloadSubnet1`
   - Public IP: None
   - NSG: Create new with no inbound internet access

3. Create a test VM in Spoke2:
   - Name: `Spoke2-VM`
   - Resource group: `NetworkingRG`
   - Region: Same as virtual networks
   - Image: Windows Server 2019 Datacenter
   - Size: Standard_D2s_v3
   - Username and password: Same as jumpbox
   - Virtual network: `Spoke2-VNet`
   - Subnet: `WorkloadSubnet1`
   - Public IP: None
   - NSG: Create new with no inbound internet access

### Part 8: Test Connectivity

1. Connect to the Hub-Jumpbox VM using RDP and the public IP address.

2. From the jumpbox, test connectivity to the spoke VMs:
   - Use Remote Desktop Connection to connect to the Spoke1-VM using its private IP address
   - Use Remote Desktop Connection to connect to the Spoke2-VM using its private IP address

3. From each spoke VM, test internet connectivity:
   - Open a web browser and navigate to a public website
   - Verify that the connection is successful and traffic is routed through the firewall

4. Verify the network flow:
   - In the Azure portal, navigate to the Hub-Firewall
   - Go to "Metrics" and check the "Data processed" metric
   - Observe the traffic flowing through the firewall

5. Test direct spoke-to-spoke communication (should fail due to current configuration):
   - From Spoke1-VM, try to ping Spoke2-VM
   - The ping should fail because traffic is being routed via the hub

### Part 9: Enable Spoke-to-Spoke Communication (Optional)

1. Create a new rule in the firewall policy:
   - Rule collection: `VNetToVNet`
   - Priority: 200
   - Action: Allow
   - Rule:
     - Name: `AllowSpokeToSpoke`
     - Source type: IP Address
     - Source: `10.1.0.0/16,10.2.0.0/16`
     - Protocol: Any
     - Destination Ports: *
     - Destination Type: IP Address
     - Destination: `10.1.0.0/16,10.2.0.0/16`

2. Test connectivity again:
   - From Spoke1-VM, try to ping or RDP to Spoke2-VM
   - The connection should now succeed

## Clean Up Resources

When you're done experimenting, delete the resource group to avoid incurring charges:

1. Navigate to Resource Groups in the Azure portal
2. Select the `NetworkingRG` resource group
3. Click "Delete resource group"
4. Enter the resource group name to confirm deletion
5. Click "Delete"

## Key Takeaways

- You've learned how to design and implement a hub and spoke network topology
- You've configured network security using NSGs and Azure Firewall
- You've implemented custom routing with UDRs
- You've set up virtual network peering for inter-network connectivity
- You've validated the design by testing connectivity between different network segments

## Next Steps

- Implement a VPN Gateway in the hub network for on-premises connectivity
- Set up Azure Bastion for secure VM access without public IPs
- Implement Private Link for secure access to PaaS services
- Configure Azure Monitor Network Insights for comprehensive monitoring
- Implement more complex traffic routing scenarios with multiple firewalls