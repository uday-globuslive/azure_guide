# Azure Core Services (AZ-900)

## Compute Services

### Azure Virtual Machines (VMs)
- IaaS offering for full control over the operating system
- Ideal for lift-and-shift migration
- Support Windows and Linux
- Pay-as-you-go pricing options

### Azure App Service
- PaaS for building, deploying, and scaling web apps
- Supports multiple languages and frameworks (.NET, Java, Node.js, Python, PHP)
- Built-in CI/CD integration and zero-downtime deployments

### Azure Container Instances (ACI)
- Run containers without managing servers
- Perfect for simple applications, task automation, and build jobs

### Azure Kubernetes Service (AKS)
- Managed Kubernetes service for container orchestration
- Simplified deployment, management, and scaling of containerized applications

### Azure Functions
- Serverless compute service
- Run small pieces of code (functions) without managing infrastructure
- Pay only for the compute time you consume

## Networking Services

### Azure Virtual Network
- Enables Azure resources to communicate with each other, the internet, and on-premises networks
- Provides isolation, segmentation, and filtering capabilities

### Azure Load Balancer
- Distributes incoming traffic among healthy VMs
- High availability and network performance

### Azure Application Gateway
- Web traffic load balancer that manages traffic to web applications
- Web application firewall capabilities

### Azure DNS
- Hosting service for DNS domains
- Uses Microsoft's global network of name servers

### Azure Content Delivery Network (CDN)
- Delivers high-bandwidth content to users globally
- Minimizes latency by caching content at strategically placed points of presence

## Storage Services

### Azure Blob Storage
- Unstructured data storage solution
- Ideal for serving images/documents directly to a browser, streaming video/audio, backup/restore, and data analysis

### Azure File Storage
- Fully managed file shares in the cloud
- Accessible via SMB protocol
- Mount file shares concurrently by cloud or on-premises deployments

### Azure Queue Storage
- Service for storing large numbers of messages
- Messages accessible from anywhere via authenticated HTTP or HTTPS calls

### Azure Table Storage
- NoSQL key-attribute data store
- Ideal for storing structured, non-relational data

### Azure Disk Storage
- Block-level storage volumes for Azure VMs
- Available in SSD and HDD options with various performance tiers

## Database Services

### Azure Cosmos DB
- Globally distributed, multi-model database service
- Supports document, key-value, graph, and column-family data models
- Guaranteed low latency and high availability

### Azure SQL Database
- Fully managed relational database with auto-scaling, security, and intelligence
- Based on the latest stable version of Microsoft SQL Server

### Azure Database for MySQL
- Fully managed MySQL database service
- High availability and security

### Azure Database for PostgreSQL
- Fully managed PostgreSQL database service
- Built-in high availability and security

### Azure SQL Managed Instance
- Fully managed SQL Server instance with near-100% compatibility with on-premises SQL Server

## Identity Services

### Azure Active Directory (Azure AD)
- Cloud-based identity and access management service
- Enables users to sign in and access resources
- Single sign-on (SSO) to thousands of cloud SaaS applications

### Azure Multi-Factor Authentication (MFA)
- Additional security by requiring two or more verification methods
- Part of Azure AD

## Management Tools

### Azure Portal
- Web-based, unified console for managing Azure resources
- Customizable dashboard and accessibility features

### Azure PowerShell
- PowerShell module for managing Azure resources
- Available on Windows, macOS, and Linux

### Azure CLI
- Command-line interface for managing Azure resources
- Available on Windows, macOS, and Linux

### Azure Cloud Shell
- Browser-based shell experience to manage Azure resources
- No local installation required

### Azure Advisor
- Personalized cloud consultant that helps follow best practices
- Provides recommendations for high availability, security, performance, and cost

## Hands-On Exercises

1. Create and configure a virtual machine
2. Set up an Azure virtual network
3. Create a storage account and upload files
4. Deploy a simple web application using Azure App Service
5. Create an Azure SQL Database instance