# Hands-On Lab: Creating Your First Azure Virtual Machine

## Objective
Create a Windows virtual machine, connect to it using Remote Desktop Protocol (RDP), and configure basic settings.

## Prerequisites
- An Azure account with an active subscription
- Basic familiarity with the Azure portal

## Estimated Time
30-45 minutes

## Steps

### 1. Sign in to the Azure Portal

1. Open your web browser and navigate to [https://portal.azure.com](https://portal.azure.com)
2. Sign in with your Azure account credentials

### 2. Create a Resource Group

1. In the Azure portal, click on "Resource groups" in the left navigation or search for it in the top search bar
2. Click "+ Create" to create a new resource group
3. Enter the following information:
   - Subscription: Select your subscription
   - Resource group: Enter "MyFirstResourceGroup"
   - Region: Select a region close to your location
4. Click "Review + create" and then "Create" after validation passes

### 3. Create a Virtual Machine

1. In the Azure portal, search for "Virtual machines" in the top search bar
2. Click "+ Create" and select "Azure virtual machine"
3. In the "Basics" tab, enter the following information:
   - Subscription: Select your subscription
   - Resource group: Select "MyFirstResourceGroup"
   - Virtual machine name: Enter "MyFirstVM"
   - Region: Use the same region as your resource group
   - Availability options: No infrastructure redundancy required
   - Image: Windows Server 2019 Datacenter
   - Size: Standard_D2s_v3 (2 vcpus, 8 GiB memory) or select "See all sizes" to choose another
4. Under "Administrator account", enter a username and password you'll remember
5. Under "Inbound port rules", select "Allow selected ports" and check "RDP (3389)"
6. Click "Next: Disks >"
7. Accept the default disk settings and click "Next: Networking >"
8. Ensure that "Create new" is selected for the virtual network and accept the default settings
9. Click "Review + create" and then "Create" after validation passes

### 4. Connect to the Virtual Machine

1. Once deployment is complete, click "Go to resource"
2. In the VM overview page, click "Connect" at the top of the page
3. Select "RDP" in the connect page
4. Click "Download RDP File" and open the file when it downloads
5. When prompted, enter the administrator credentials you specified during VM creation
6. When asked if you want to connect despite certificate errors, click "Yes" or "Continue"

### 5. Explore and Configure the Virtual Machine

1. Once connected, Server Manager should open automatically
2. Explore the Windows Server environment
3. Open PowerShell by right-clicking the Start button and selecting "Windows PowerShell"
4. Run the following commands to view system information:
   ```powershell
   systeminfo
   Get-ComputerInfo
   ```
5. Install a web server role by running the following command in PowerShell:
   ```powershell
   Install-WindowsFeature -Name Web-Server -IncludeManagementTools
   ```
6. Open a web browser in the VM and navigate to `http://localhost` to see the default IIS web page

### 6. Access the Web Server from the Internet

1. Return to the Azure portal in your local browser
2. Navigate to your VM and click on "Networking" in the left navigation
3. Click on "Add inbound port rule"
4. Add the following settings:
   - Destination port ranges: 80
   - Protocol: TCP
   - Name: HTTP
5. Click "Add"
6. Find your VM's public IP address on the overview page
7. Open a new browser tab on your local machine and navigate to the public IP address
8. You should see the default IIS web page

### 7. Stop the Virtual Machine

1. Return to the VM overview page in the Azure portal
2. Click "Stop" at the top of the page
3. When prompted, click "OK" to stop the VM

## Clean Up Resources

When you're done experimenting, you can delete the resource group to clean up all resources:

1. Navigate to "Resource groups" in the Azure portal
2. Select "MyFirstResourceGroup"
3. Click "Delete resource group"
4. Enter the resource group name to confirm deletion
5. Click "Delete"

## Key Takeaways

- You've learned how to create a basic Windows virtual machine in Azure
- You've connected to the VM using RDP and explored the system
- You've configured a web server role and opened the required port
- You've accessed the web server from the internet
- You've learned how to manage the VM's power state and clean up resources

## Next Steps

- Try creating a Linux VM and connecting via SSH
- Explore different VM sizes and their price implications
- Learn about VM availability options for high availability
- Explore Azure Backup options for VM protection