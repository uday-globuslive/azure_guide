# Developer Concepts

This guide explains the essential developer concepts that form the foundation for understanding Azure development services and DevOps practices.

## Scripting Fundamentals

### What is Scripting?
Scripting involves writing code that automates tasks without requiring compilation. Scripts are interpreted at runtime rather than compiled into machine code beforehand.

### PowerShell Basics
PowerShell is Microsoft's task automation and configuration management framework.

#### Key PowerShell Concepts
- **Cmdlets**: Command-let functions (Verb-Noun format)
  - Examples: `Get-Process`, `New-Item`, `Set-Content`
- **Aliases**: Shortcuts for cmdlets
  - Examples: `dir` (for `Get-ChildItem`), `cd` (for `Set-Location`)
- **Pipeline**: Passing output from one command as input to another
  - Example: `Get-Process | Where-Object { $_.CPU -gt 10 }`
- **Variables**: Store and retrieve data
  - Example: `$serverName = "prod-sql-01"`
- **Scripts**: Collections of commands saved as `.ps1` files

#### PowerShell for Azure
- **Azure PowerShell Module**: Collection of cmdlets for Azure management
- **Authentication Methods**:
  - Connect-AzAccount (interactive login)
  - Service principals
  - Managed identities
- **Common Tasks**:
  - Resource creation and management
  - Configuration and monitoring
  - Automation of repetitive tasks
  - Pipeline integration

#### PowerShell Example
```powershell
# Connect to Azure
Connect-AzAccount

# List all resource groups
Get-AzResourceGroup

# Create a new resource group
New-AzResourceGroup -Name "MyResourceGroup" -Location "East US"

# Create a storage account
New-AzStorageAccount -ResourceGroupName "MyResourceGroup" `
  -Name "mystorageaccount" -Location "East US" `
  -SkuName "Standard_LRS" -Kind "StorageV2"
```

### Bash Scripting Basics
Bash (Bourne Again SHell) is the default shell for most Linux distributions and macOS.

#### Key Bash Concepts
- **Commands**: Programs that can be executed
- **Variables**: Store data
  - Example: `servername="prod-sql-01"`
- **Control Structures**: if statements, loops, case statements
- **Functions**: Reusable code blocks
- **Pipes and Redirection**: Connect commands and redirect input/output
  - Example: `cat file.txt | grep "error" > errors.txt`

#### Bash for Azure
- **Azure CLI**: Command-line interface for Azure management
- **Authentication Methods**:
  - az login (interactive login)
  - Service principals
  - Managed identities
- **Common Tasks**:
  - Resource creation and management
  - Configuration and monitoring
  - Scripting automation processes
  - CI/CD pipeline integration

#### Bash Example
```bash
#!/bin/bash

# Connect to Azure
az login

# List all resource groups
az group list --output table

# Create a new resource group
az group create --name MyResourceGroup --location eastus

# Create a storage account
az storage account create \
  --name mystorageaccount \
  --resource-group MyResourceGroup \
  --location eastus \
  --sku Standard_LRS \
  --kind StorageV2
```

### Common Scripting Tasks in Cloud Environments
- **Resource Provisioning**: Creating and configuring resources
- **Configuration Management**: Setting configuration parameters
- **Monitoring and Reporting**: Gathering and analyzing metrics
- **User Management**: Creating and managing users
- **Backup and Recovery**: Automating backup processes
- **Security Tasks**: Implementing security controls
- **CI/CD Automation**: Integrating with deployment pipelines

## Data Serialization Formats

### JSON (JavaScript Object Notation)
JSON is a lightweight data interchange format that is easy for humans to read and write, and easy for machines to parse and generate.

#### JSON Structure
- **Objects**: Collections of name/value pairs enclosed in curly braces `{}`
- **Arrays**: Ordered lists of values enclosed in square brackets `[]`
- **Values**: Strings, numbers, booleans, objects, arrays, or null
- **Name/Value Pairs**: Format is `"name": value`

#### JSON Example
```json
{
  "resourceGroup": "MyResourceGroup",
  "location": "eastus",
  "resources": [
    {
      "name": "myVM",
      "type": "virtualMachine",
      "size": "Standard_D2s_v3"
    },
    {
      "name": "myStorageAccount",
      "type": "storageAccount",
      "sku": "Standard_LRS"
    }
  ],
  "tags": {
    "environment": "production",
    "department": "IT"
  }
}
```

#### JSON in Azure
- **ARM Templates**: JSON format for Azure infrastructure as code
- **Configuration Files**: Application settings in JSON format
- **API Responses**: Most Azure APIs return JSON
- **Azure Functions Settings**: Function configurations
- **Package Management**: package.json for Node.js applications

### YAML (YAML Ain't Markup Language)
YAML is a human-readable data serialization standard often used for configuration files.

#### YAML Structure
- **Maps**: Collections of name/value pairs (similar to JSON objects)
- **Sequences**: Ordered lists of values (similar to arrays)
- **Scalars**: Strings, numbers, booleans, etc.
- **Indentation**: Used for structure instead of braces or brackets
- **Anchors and Aliases**: Allow for reusing content

#### YAML Example
```yaml
resourceGroup: MyResourceGroup
location: eastus
resources:
  - name: myVM
    type: virtualMachine
    size: Standard_D2s_v3
  - name: myStorageAccount
    type: storageAccount
    sku: Standard_LRS
tags:
  environment: production
  department: IT
```

#### YAML in Azure
- **Azure Pipelines**: Defining CI/CD pipelines
- **Kubernetes Configurations**: AKS deployment files
- **Azure Policy Definitions**: Custom policy definitions
- **Azure DevOps Templates**: Reusable workflow definitions
- **Bicep Parameter Files**: When in YAML format

### XML (eXtensible Markup Language)
XML is a markup language that defines rules for encoding documents in a format that is both human-readable and machine-readable.

#### XML Structure
- **Elements**: Main components marked by tags
- **Attributes**: Name-value pairs within start tags
- **Namespaces**: Prevent naming conflicts
- **Schema**: Define structure and data types

#### XML Example
```xml
<Resources>
  <ResourceGroup name="MyResourceGroup" location="eastus">
    <Resource name="myVM" type="virtualMachine" size="Standard_D2s_v3" />
    <Resource name="myStorageAccount" type="storageAccount" sku="Standard_LRS" />
    <Tags>
      <Tag key="environment" value="production" />
      <Tag key="department" value="IT" />
    </Tags>
  </ResourceGroup>
</Resources>
```

#### XML in Azure
- **Service Configurations**: Older Azure service definitions
- **SOAP APIs**: Some Azure services have SOAP interfaces
- **Application Configurations**: Web.config files
- **Azure Service Fabric**: Application manifests

## API Concepts

### What is an API?
An Application Programming Interface (API) is a set of rules that allow programs to talk to each other, defining the kinds of calls or requests that can be made, how to make them, the data formats to use, and the conventions to follow.

### API Types

#### REST (Representational State Transfer)
- **Architecture Style**: Uses HTTP methods on resources
- **Characteristics**: 
  - Stateless communication
  - Client-server architecture
  - Cacheable responses
  - Uniform interface
- **HTTP Methods**:
  - GET: Retrieve resources
  - POST: Create new resources
  - PUT: Update resources (replace)
  - PATCH: Update resources (partial)
  - DELETE: Remove resources
- **Status Codes**:
  - 2xx: Success (200 OK, 201 Created, 204 No Content)
  - 4xx: Client errors (400 Bad Request, 401 Unauthorized, 404 Not Found)
  - 5xx: Server errors (500 Internal Server Error)

#### SOAP (Simple Object Access Protocol)
- **Protocol**: XML-based messaging protocol
- **Characteristics**: 
  - More formal/structured than REST
  - Protocol-independent
  - Built-in error handling
- **Components**:
  - Envelope: Root of the XML document
  - Header: Contains metadata
  - Body: Contains actual message
  - Fault: Error information

#### GraphQL
- **Query Language**: For APIs, with runtime for executing queries
- **Characteristics**:
  - Client specifies exactly what data it needs
  - Single endpoint for all requests
  - Strong typing system
  - Real-time updates with subscriptions

#### gRPC
- **Remote Procedure Call Framework**: Using HTTP/2
- **Characteristics**:
  - Uses Protocol Buffers (binary serialization)
  - High performance
  - Strongly typed
  - Code generation for multiple languages

### API Management Concepts

#### API Documentation
- **OpenAPI/Swagger**: Standard for describing REST APIs
- **API Reference**: Method descriptions, parameters, responses
- **Tutorials and Examples**: How to use the API

#### Authentication and Authorization
- **API Keys**: Simple tokens for identification
- **OAuth 2.0**: Framework for access delegation
- **JWT (JSON Web Tokens)**: Compact, self-contained tokens
- **HMAC (Hash-based Message Authentication Code)**: Signature-based verification

#### API Design Principles
- **Consistency**: Uniform naming and response patterns
- **Versioning**: Managing API changes
- **Error Handling**: Clear error messages and codes
- **Pagination**: Handling large result sets
- **Filtering and Sorting**: Query parameter conventions

#### API Testing
- **Unit Testing**: Testing individual endpoints
- **Integration Testing**: Testing API interactions
- **Performance Testing**: Load testing API endpoints
- **Security Testing**: Vulnerability assessment

### Azure API Services
- **Azure API Management**: Comprehensive API gateway solution
- **Azure Functions**: Serverless API implementation
- **Azure App Service**: Hosting for API applications
- **Azure Logic Apps**: Workflow-based APIs
- **Azure API Apps**: Part of App Service for API hosting

## Source Control

### What is Source Control?
Source control (or version control) is a system that records changes to files over time so that you can recall specific versions later. It allows multiple people to collaborate on projects efficiently.

### Source Control Concepts

#### Repository
- **Definition**: Container for a project's files and revision history
- **Types**:
  - Centralized: Single server-based repository (SVN)
  - Distributed: Each user has a complete copy (Git)

#### Commit
- **Definition**: Snapshot of changes at a point in time
- **Components**:
  - Commit message: Description of changes
  - Author information
  - Timestamp
  - Changes to files

#### Branch
- **Definition**: Parallel version of the repository
- **Use Cases**:
  - Feature development
  - Bug fixes
  - Release management
  - Experimentation

#### Merge
- **Definition**: Combining changes from different branches
- **Merge Conflicts**: When changes overlap
- **Resolution**: Manual intervention to resolve conflicts

#### Clone
- **Definition**: Copy of a repository on a local machine
- **Remote**: Link to the original repository

### Git Basics
Git is a distributed version control system that tracks changes in source code during software development.

#### Core Git Commands
- **git init**: Initialize a new repository
- **git clone**: Copy a repository
- **git add**: Stage changes for commit
- **git commit**: Record changes to the repository
- **git push**: Upload local changes to a remote
- **git pull**: Download changes from a remote
- **git branch**: List, create, or delete branches
- **git checkout**: Switch branches or restore files
- **git merge**: Join two or more development histories
- **git status**: Show working tree status
- **git log**: Show commit logs

#### Git Workflow
- **Working Directory**: Files on your computer
- **Staging Area**: Prepared changes ready to be committed
- **Local Repository**: Commits stored on your machine
- **Remote Repository**: Repository stored on a server

#### Common Git Workflows
- **Feature Branch Workflow**: Create branch for each feature
- **Gitflow**: Strict branching model with develop and master branches
- **Forking Workflow**: Fork repositories, make changes, submit pull requests
- **Trunk-Based Development**: Frequent merges to main branch

### Pull Requests and Code Reviews

#### Pull Request Process
- **Creation**: Developer submits changes for review
- **Review**: Team examines code changes
- **Feedback**: Reviewers provide comments
- **Revision**: Developer addresses feedback
- **Approval**: Reviewers signify acceptance
- **Merge**: Changes incorporated into target branch

#### Code Review Benefits
- **Quality Improvement**: Catch bugs early
- **Knowledge Sharing**: Team learns from each other
- **Consistency**: Adherence to standards
- **Mentorship**: Senior developers guide juniors
- **Collective Ownership**: Team responsibility for code

#### Code Review Best Practices
- **Small Changes**: Keep pull requests focused
- **Clear Descriptions**: Explain what, why, and how
- **Automated Checks**: Use CI/CD to verify changes
- **Constructive Feedback**: Be specific and helpful
- **Timely Reviews**: Don't delay the process

### Source Control in Azure DevOps

#### Azure Repos
- **Git Repositories**: Complete Git functionality
- **Team Foundation Version Control (TFVC)**: Centralized alternative
- **Features**:
  - Branch policies
  - Pull request workflows
  - Code search
  - Integration with Azure Boards

#### Branch Policies
- **Required Reviewers**: Specific people must approve
- **Minimum Number of Reviewers**: Set threshold
- **Linked Work Items**: Require association with work items
- **Build Validation**: Require successful builds
- **Status Checks**: Require external validations

#### Repository Security
- **Permission Levels**: Contributor, Reader, Administrator
- **Branch Lock**: Prevent unauthorized changes
- **Branch Protection**: Prevent force pushes
- **Path-Based Authorization**: Control access to specific areas

## Application Architecture

### Client-Server Model
The client-server model divides computing tasks between service providers (servers) and service requesters (clients).

#### Components
- **Client**: Requests services or resources
  - Examples: Web browsers, mobile apps, desktop applications
- **Server**: Provides services or resources
  - Examples: Web servers, database servers, file servers
- **Communication**: Usually over a network using standardized protocols

#### Characteristics
- **Roles Separation**: Clear division of responsibilities
- **Centralized Resources**: Managed in one place
- **Scalability**: Can scale client and server components independently
- **Maintenance**: Server updates without client changes

### Web Applications

#### Web Application Components
- **Frontend (Client-side)**:
  - HTML: Structure
  - CSS: Presentation
  - JavaScript: Behavior
  - Frameworks: React, Angular, Vue.js
- **Backend (Server-side)**:
  - Application logic
  - Data processing
  - Authentication/authorization
  - API endpoints
  - Frameworks: ASP.NET, Django, Express.js
- **Database**:
  - Data storage and retrieval
  - Relational or NoSQL databases

#### Web Application Patterns
- **Model-View-Controller (MVC)**:
  - Model: Data and business logic
  - View: User interface
  - Controller: Handles input and updates model/view
- **Single Page Application (SPA)**:
  - One HTML page dynamically updated
  - Uses AJAX for background communication
  - Minimizes page reloads
- **Progressive Web Application (PWA)**:
  - Web app with native-like capabilities
  - Offline functionality
  - Push notifications
  - Installable

#### Web Application Hosting in Azure
- **Azure App Service**: PaaS offering for web applications
- **Azure Static Web Apps**: Hosting for static content and serverless APIs
- **Azure Functions**: Serverless compute for web backends
- **Azure Container Apps**: Containerized web applications
- **Azure Kubernetes Service**: Container orchestration for complex applications

### Microservices

#### Microservices Architecture
A microservices architecture structures an application as a collection of loosely coupled services.

#### Characteristics
- **Service Independence**: Services can be developed, deployed, and scaled independently
- **Single Responsibility**: Each service focuses on a specific business capability
- **Decentralized Data Management**: Each service manages its own data
- **Resilience**: Failure in one service doesn't crash the entire application
- **Technology Diversity**: Different services can use different technologies
- **Containerization**: Often implemented using container technologies

#### Microservices Components
- **API Gateway**: Entry point for clients, handles routing
- **Service Registry**: Keeps track of available service instances
- **Service Discovery**: Finds available service instances
- **Load Balancer**: Distributes traffic among service instances
- **Circuit Breaker**: Prevents cascading failures
- **Configuration Server**: Centralized configuration management
- **Distributed Tracing**: Tracks requests across services

#### Microservices in Azure
- **Azure Kubernetes Service (AKS)**: Container orchestration
- **Azure Container Apps**: Simplified container hosting
- **Azure Service Fabric**: Platform for microservices
- **Azure API Management**: API gateway functionality
- **Azure Functions**: Serverless microservices

### Containers

#### Container Concepts
- **Definition**: Standardized, executable package of software that includes everything needed to run an application
- **Components**:
  - Application code
  - Runtime
  - System tools
  - Libraries
  - Settings
- **Compared to VMs**:
  - Containers share the host OS kernel
  - Lighter weight and faster startup
  - Less isolated but more efficient

#### Docker Basics
- **Dockerfile**: Instructions to build a container image
- **Image**: Template with application and dependencies
- **Container**: Running instance of an image
- **Registry**: Repository for storing and distributing images
- **Docker Compose**: Define multi-container applications

#### Container Orchestration
- **Definition**: Managing the deployment and lifecycle of containers
- **Features**:
  - Scheduling: Placing containers on nodes
  - Scaling: Adjusting number of container instances
  - Load Balancing: Distributing traffic
  - Self-healing: Replacing failed containers
  - Rolling Updates: Updating without downtime

#### Container Services in Azure
- **Azure Container Registry (ACR)**: Private container registry
- **Azure Container Instances (ACI)**: Simple container hosting
- **Azure Kubernetes Service (AKS)**: Managed Kubernetes
- **Azure Container Apps**: Serverless container platform
- **Web App for Containers**: App Service for containers

This overview provides the essential developer concepts that will help you build your knowledge of Azure development services and DevOps practices. The subsequent sections will build upon these fundamentals to explain cloud-specific development concepts and Azure implementations.