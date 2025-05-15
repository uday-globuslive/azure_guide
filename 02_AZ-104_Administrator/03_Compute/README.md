# Azure Compute (AZ-104)

## Virtual Machines

### VM Fundamentals
- **Virtual Machine**: Emulation of a physical computer
- VM sizing and series
  - General purpose (B, D, A)
  - Compute optimized (F)
  - Memory optimized (E, M)
  - Storage optimized (L)
  - GPU (N)
  - High performance compute (H)
- Pricing models
  - Pay-as-you-go
  - Reserved Instances (1 or 3 years)
  - Spot VMs
- Azure Hybrid Benefit for Windows Server and SQL Server

### VM Storage
- OS disk options
  - Managed disks vs. unmanaged disks
  - Standard HDD, Standard SSD, Premium SSD, Ultra Disk
- Data disks
  - Adding, resizing, and managing data disks
  - Disk striping for performance
- Temporary storage
- Disk encryption options
  - Azure Disk Encryption (ADE)
  - Server-side encryption (SSE)

### VM Networking
- Virtual networks and subnets
- Network interfaces (NICs)
- Public IP addresses
- Network Security Groups (NSGs)
- Application Security Groups (ASGs)
- Load balancing options
- Bandwidth allocation

### VM Availability
- Availability Sets
  - Fault domains
  - Update domains
- Availability Zones
- VM scale sets for high availability
- Single VM SLA vs. multiple VM SLA
- Maintenance configurations and control

### VM Management
- Creating VMs
  - Azure Portal
  - Azure CLI and PowerShell
  - ARM templates
- VM extensions
  - Custom Script Extension
  - Desired State Configuration (DSC)
  - Diagnostics Extension
- Guest OS updates
- Backup and restore
- Monitoring and diagnostics
- Maintenance and updates

### VM Scale Sets (VMSS)
- Automatic scaling based on metrics
- Scaling policies and schedules
- Rolling upgrades
- Application health probes
- Instance protection
- Overprovisioning
- Flexible orchestration modes

### VM Images
- Azure Marketplace images
- Generalized vs. specialized images
- Creating custom images
- Shared Image Gallery
- Image versioning and replication
- Image templates with Azure Image Builder

## Azure App Service

### App Service Plans
- Tiers and pricing
  - Free and Shared
  - Basic
  - Standard
  - Premium
  - PremiumV2 and PremiumV3
  - Isolated and Isolated V2
- Scaling up (vertical) vs. scaling out (horizontal)
- Auto-scaling rules and best practices
- App Service Environments (ASE)

### Web Apps
- Deployment options
  - Continuous deployment (GitHub, Azure DevOps)
  - Container-based deployment
  - Manual deployment (FTP, ZIP)
- Application settings and configurations
- Custom domains and SSL certificates
- Networking configurations
- Deployment slots for staging and testing
- Monitoring and diagnostics

### Mobile Apps
- Offline data sync
- Authentication and authorization
- Push notifications
- Client SDKs

### API Apps
- API definition and documentation
- CORS settings
- API versioning strategies
- Security and authorization

### WebJobs
- Continuous vs. triggered WebJobs
- WebJob types (Script, Java, .NET, etc.)
- WebJob scaling and performance

## Azure Container Instances (ACI)

### ACI Basics
- Serverless container hosting
- Container groups
- Resource allocation
- Pricing and billing model

### Container Configuration
- Container images
- Registry authentication
- Environment variables
- Commands and entry points
- Restart policies

### Networking and Storage
- Virtual network integration
- Public IP assignment
- Container group private networking
- Volume mounting
  - Azure Files
  - Secret volumes
  - Empty directories
  - GitHub repositories

### Container Management
- Starting, stopping, and restarting containers
- Container logs and events
- Container metrics and monitoring
- Container health probes

## Azure Kubernetes Service (AKS)

### AKS Architecture
- Managed Kubernetes service
- Control plane vs. node pools
- Node types and VM sizes
- Cluster autoscaling
- Availability zones support

### Cluster Management
- Creating and scaling clusters
- Upgrading Kubernetes versions
- Node pool management
- Integration with Azure Monitor
- Integration with Azure Policy

### Application Deployment
- Kubernetes manifests
- Helm charts
- CI/CD integration
- Blue/green and canary deployments

### AKS Networking
- Kubenet vs. Azure CNI
- Network policies
- Ingress controllers
- Service mesh options

### AKS Storage
- Storage classes
- Persistent volumes
- Azure Disk and Azure Files integration
- Storage account considerations

### AKS Security
- RBAC for Kubernetes
- Azure AD integration
- Pod identity
- Network security
- Container security

## Azure Functions

### Function Fundamentals
- Serverless compute service
- Triggers and bindings
- Function app structure
- Function runtime versions
- Durable functions for stateful workflows

### Hosting Plans
- Consumption plan
- Premium plan
- App Service plan
- Scaling considerations for each plan

### Function Development
- Supported languages
  - C#, JavaScript, Python, Java, PowerShell
- Local development and testing
- Deployment methods
- Application settings and configuration

### Function Monitoring
- Application Insights integration
- Logging and diagnostics
- Performance monitoring
- Alerts and notifications

## Windows Virtual Desktop

### WVD Architecture
- Host pools
- App groups
- Workspaces
- Session hosts
- Gateways and connection brokers

### Deployment and Management
- Creating and configuring host pools
- Publishing applications and desktops
- FSLogix profile containers
- Monitoring and diagnostics
- Scaling automation

### User Experience
- Supported client applications
- Authentication methods
- Multi-session vs. single-session hosts
- GPU-accelerated rendering

## Azure Batch

### Batch Fundamentals
- Parallel and high-performance computing (HPC)
- Batch accounts
- Nodes and node pools
- Jobs, tasks, and job scheduling

### Batch Workloads
- Job preparation and release tasks
- Task dependencies
- Files and resources
- Application packages
- Task outputs

### Batch Management
- Monitoring and metrics
- Job and task priorities
- Automatic scaling
- Integration with other Azure services

## Compute Management and Automation

### Azure Automation
- Runbooks for automation
- DSC for configuration management
- Update management
- Change tracking
- Inventory management

### Azure Resource Manager (ARM) Templates
- Infrastructure as Code (IaC)
- Template structure and parameters
- Deployment modes
- Nested and linked templates
- Template testing and validation

### Azure Monitor for Compute
- VM insights
- Container insights
- Application insights
- Log Analytics
- Alerts and action groups

### Cost Management
- Compute cost optimization
- Right-sizing recommendations
- Reserved Instances
- Azure Hybrid Benefit
- Start/stop automation for dev/test

## Compute Security

### Security Center and Defender for Cloud
- Security posture management
- Vulnerability assessment
- Just-in-time VM access
- Adaptive application controls
- Security alerts and recommendations

### Network Security for Compute
- Network Security Groups (NSGs)
- Application Security Groups (ASGs)
- Azure Firewall
- Azure Bastion
- Private Link and Private Endpoints

### Identity and Access Management
- Managed identities for Azure resources
- Azure AD integration
- RBAC for compute resources
- Privileged Identity Management (PIM)

## Compute Migration

### Azure Migrate
- Assessment and discovery
- Server migration
- Database migration
- Web app migration
- Virtual desktop migration

### Migration Strategies
- Rehost (lift-and-shift)
- Refactor (modernize)
- Rearchitect (rebuild)
- Replace (SaaS alternatives)
- Retire (decommission)

## Best Practices

### Design Recommendations
- Service selection guidelines
- Architecture patterns
- Cost optimization strategies
- Security hardening

### Operational Excellence
- Automation with ARM templates and scripts
- Monitoring and alerting setup
- Disaster recovery planning
- Regular security reviews

## Hands-On Exercises

1. Deploy and configure virtual machines with availability sets
2. Implement VM Scale Sets with custom scaling rules
3. Deploy a web application with deployment slots on App Service
4. Set up containerized applications using ACI and AKS
5. Create and deploy serverless workloads with Azure Functions
6. Implement automation for compute resources with Azure Automation
7. Configure compute security with Just-in-Time access and NSGs