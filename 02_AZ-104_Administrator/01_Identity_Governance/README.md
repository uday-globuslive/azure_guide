# Identity and Governance (AZ-104)

## Azure Active Directory (Azure AD)

### Azure AD Concepts
- **Azure Active Directory**: Cloud-based identity and access management service
- **Tenant**: Dedicated instance of Azure AD representing an organization
- **User**: Individual identity with attributes and credentials
- **Group**: Collection of users for simplified access management
- **Device**: Physical or virtual machine registered with Azure AD
- **Application**: Software program registered with Azure AD

### User Management
- Creating and managing user accounts
  - Add individual users
  - Bulk user operations
  - Guest user access (B2B collaboration)
- User properties and attributes
- License assignment
- Password policies and self-service password reset
- User access reviews
- Privileged Identity Management (PIM)

### Group Management
- Types of groups
  - Security groups
  - Microsoft 365 groups
- Group creation methods
  - Assigned (static)
  - Dynamic (rule-based membership)
- Group-based licensing
- Administrative units for delegation
- Managing group lifecycle

### Hybrid Identity
- **Azure AD Connect**: Tool for synchronizing on-premises AD with Azure AD
- Authentication methods
  - Password Hash Synchronization (PHS)
  - Pass-through Authentication (PTA)
  - Federation with AD FS
- Directory synchronization configurations
- Azure AD Connect Health monitoring
- Seamless Single Sign-On (SSO)

### Conditional Access
- Policy components
  - Assignments (users, groups, apps)
  - Conditions (risk, device, location)
  - Access controls (grant, block, session controls)
- Common policy scenarios
  - Require MFA for specific applications
  - Block access from untrusted locations
  - Require compliant devices
- Monitoring and troubleshooting policies
- Integration with Microsoft Intune

## Azure Subscription Management

### Subscription Fundamentals
- **Subscription**: Logical container for resources and billing boundary
- Types of subscriptions
  - Free
  - Pay-As-You-Go
  - Enterprise Agreement (EA)
  - Cloud Solution Provider (CSP)
- Subscription lifecycle management
- Subscription limits and quotas

### Management Groups
- Organizing subscriptions
- Creating management group hierarchies
- Applying policies at management group level
- Delegating administrative access
- Best practices for enterprise-scale deployments

### Cost Management
- Azure Cost Management + Billing
- Budgets and alerts
- Cost allocation and showback
- Utilizing Azure Advisor recommendations
- Resource optimization strategies
- Reserved Instances and Savings Plans

## Resource Governance

### Resource Groups
- **Resource Group**: Container that holds related resources
- Logical grouping strategies
  - By lifecycle
  - By resource type
  - By department/project
- Resource group properties and metadata
- Moving resources between groups
- Resource group locks to prevent changes
- Resource group deployments

### Azure Policy
- Creating and assigning policies
- Policy definitions and initiatives
- Built-in vs. custom policies
- Policy effects (Audit, Deny, Deploy, Modify)
- Compliance monitoring and remediation
- Policy exemptions

### Role-Based Access Control (RBAC)
- Azure roles vs. Azure AD roles
- Built-in roles
  - Owner, Contributor, Reader
  - Service-specific roles
- Custom role definitions
- Scope of role assignments
  - Management group
  - Subscription
  - Resource group
  - Resource
- Just-In-Time (JIT) access with PIM
- Access reviews and monitoring

### Azure Resource Tags
- Tag name and value pairs
- Tagging strategies
  - Cost center
  - Environment
  - Department
  - Application owner
- Tag inheritance
- Enforcing tagging with policies
- Resource tags for cost allocation

### Resource Locks
- Types of locks
  - Read-only (CanNotDelete)
  - Delete (ReadOnly)
- Applying locks at different scopes
- Lock inheritance
- Managing locks with Azure CLI, PowerShell, and ARM templates

## Azure Resource Manager (ARM)

### ARM Concepts
- **ARM**: Deployment and management service for Azure
- Resource providers and resource types
- ARM API versions
- Resource consistency and dependencies
- ARM vs. classic deployment model

### ARM Templates
- Template schema structure
- Parameters, variables, and functions
- Resource definitions
- Output values
- Template deployment modes
  - Incremental vs. Complete
- Nested and linked templates
- Template validation and what-if operations
- Deployment history and debugging

### Bicep
- Modern language for ARM templates
- Bicep vs. ARM JSON templates
- Converting between Bicep and ARM JSON
- Modules and code reuse
- Developing and deploying Bicep files

## Best Practices

### Governance Framework
- Azure Enterprise Scaffold
- Cloud Adoption Framework (CAF)
- Well-Architected Framework
- Landing Zone implementation

### Security and Compliance
- Security Center and Defender for Cloud
- Regulatory compliance management
- Azure Blueprint for compliance templates
- Security baselines

### Automation
- Azure Automation
- PowerShell and Azure CLI for governance
- Infrastructure as Code (IaC) approach
- DevOps integration for governance

## Hands-On Exercises

1. Set up Azure AD Connect to synchronize on-premises AD with Azure AD
2. Configure Conditional Access policies for multi-factor authentication
3. Create a management group hierarchy and implement RBAC
4. Develop and deploy ARM templates for standardized resource deployment
5. Implement Azure Policy for governance and compliance
6. Set up cost management and budgets for a subscription