# Azure Services Reference Guide

This comprehensive reference provides details on core Azure services across different categories. Use this guide as a quick reference while studying for the AZ-900 exam or implementing Azure solutions.

## Compute Services

### Azure Virtual Machines
- **Description**: IaaS offering that provides on-demand, scalable computing resources
- **Use Cases**: Web servers, application servers, domain controllers, development/test
- **Key Features**:
  - Multiple VM sizes and types (general purpose, compute-optimized, memory-optimized, etc.)
  - Support for Windows and Linux operating systems
  - Scaling options (scale sets, autoscaling)
  - Managed disks with different performance tiers
- **Pricing Model**: Pay-per-second for compute usage, plus storage costs

### Azure App Service
- **Description**: PaaS offering for building, deploying, and scaling web apps
- **Use Cases**: Web applications, REST APIs, mobile backends
- **Key Features**:
  - Support for multiple languages (.NET, Java, Node.js, Python, PHP)
  - Built-in autoscaling and load balancing
  - Integration with CI/CD workflows
  - Deployment slots for zero-downtime updates
- **Pricing Model**: Based on App Service Plan tier and instance count

### Azure Container Instances
- **Description**: Serverless container hosting without orchestration
- **Use Cases**: Simple applications, task automation, batch jobs
- **Key Features**:
  - Fast startup times
  - Per-second billing
  - Custom sizes (CPU cores and memory)
  - Persistent storage options
- **Pricing Model**: Pay for container group CPU and memory usage per second

### Azure Kubernetes Service (AKS)
- **Description**: Managed Kubernetes container orchestration service
- **Use Cases**: Microservices, complex containerized applications
- **Key Features**:
  - Simplified cluster management
  - Integrated CI/CD
  - Virtual network integration
  - Horizontal pod and cluster autoscaling
- **Pricing Model**: Pay only for VM instances, storage, and networking resources

### Azure Functions
- **Description**: Serverless compute service that runs code in response to events
- **Use Cases**: Data processing, IoT, small web APIs, scheduled tasks
- **Key Features**:
  - Triggers and bindings to connect to services
  - Stateless or stateful (Durable Functions)
  - Multiple language support
  - Pay-per-execution model
- **Pricing Model**: Consumption plan (pay-per-execution) or Premium plan (dedicated resources)

### Azure Batch
- **Description**: Cloud-scale job scheduling and compute management
- **Use Cases**: High-performance computing, parallel processing, rendering
- **Key Features**:
  - Schedule compute-intensive work
  - Auto-scale VM resources
  - Job and task monitoring
  - Application package management
- **Pricing Model**: Pay for underlying compute resources used

## Storage Services

### Azure Blob Storage
- **Description**: Object storage for unstructured data
- **Use Cases**: Document storage, video/audio streaming, backup, data lakes
- **Key Features**:
  - Three storage tiers (Hot, Cool, Archive)
  - Lifecycle management
  - Data redundancy options
  - Blob versioning and soft delete
- **Pricing Model**: Based on data volume, storage tier, and operations

### Azure Files
- **Description**: Fully managed file shares in the cloud
- **Use Cases**: File sharing, legacy application migration, hybrid scenarios
- **Key Features**:
  - SMB and NFS protocol support
  - Fully managed with no maintenance
  - Accessible from anywhere
  - Azure File Sync for caching
- **Pricing Model**: Based on capacity used, transactions, and bandwidth

### Azure Queue Storage
- **Description**: Service for storing large numbers of messages for asynchronous processing
- **Use Cases**: Decoupling application components, background task processing
- **Key Features**:
  - At-least-once delivery guarantee
  - Timestamps for message expiration
  - Message TTL (time-to-live)
  - Poisoned message handling
- **Pricing Model**: Based on storage used and operations performed

### Azure Table Storage
- **Description**: NoSQL key-attribute data store
- **Use Cases**: Semi-structured data storage, logging, user data
- **Key Features**:
  - Schema-less design
  - Automatic partitioning
  - Secondary indexes (with Cosmos DB API)
  - Low-cost operations
- **Pricing Model**: Based on capacity and operations

### Azure Disk Storage
- **Description**: Block-level storage volumes for Azure VMs
- **Use Cases**: VM operating systems, databases, stateful applications
- **Key Features**:
  - Ultra, Premium, Standard SSD, and Standard HDD options
  - Encryption at rest
  - Disk snapshots
  - Disk bursting
- **Pricing Model**: Based on disk type, size, and performance tier

## Networking Services

### Azure Virtual Network
- **Description**: Private network in Azure for resources to communicate
- **Use Cases**: Secure communication between Azure resources, extending on-premises network
- **Key Features**:
  - IP address space management
  - Subnet organization
  - Network security groups
  - Virtual network peering
- **Pricing Model**: Free except for certain resources like load balancers, gateways

### Azure Load Balancer
- **Description**: Distributes inbound traffic to backend resources
- **Use Cases**: High-availability applications, horizontal scaling
- **Key Features**:
  - Layer 4 (TCP/UDP) load balancing
  - Public and internal modes
  - Health probes
  - Port forwarding
- **Pricing Model**: Based on load balancer type and rules

### Azure Application Gateway
- **Description**: Web traffic load balancer with HTTP layer features
- **Use Cases**: Layer 7 load balancing, web application firewall
- **Key Features**:
  - SSL/TLS termination
  - Cookie-based session affinity
  - URL-based routing
  - Web Application Firewall (WAF)
- **Pricing Model**: Based on gateway tier, capacity, and data processed

### Azure VPN Gateway
- **Description**: Service for sending encrypted traffic between Azure VNet and on-premises
- **Use Cases**: Site-to-site VPN, point-to-site VPN
- **Key Features**:
  - Multiple gateway SKUs for different throughput needs
  - Active-active configuration
  - BGP routing support
  - IPsec/IKE standard-based VPN
- **Pricing Model**: Based on gateway type and connection uptime

### Azure Express Route
- **Description**: Private connection between Azure and on-premises infrastructure
- **Use Cases**: High-bandwidth, reliable connection to Azure
- **Key Features**:
  - Layer 3 connectivity
  - Redundant connections
  - Lower, more predictable latency
  - QoS support for services like Teams/Skype
- **Pricing Model**: Based on circuit bandwidth, unlimited data, and premium add-on

### Azure DNS
- **Description**: Hosting service for DNS domains
- **Use Cases**: Domain name resolution, DNS management
- **Key Features**:
  - High availability and performance
  - Public and private zones
  - DNSSEC support
  - Integration with other Azure services
- **Pricing Model**: Based on hosted DNS zones and query volume

### Azure Front Door
- **Description**: Scalable, secure entry point for web applications
- **Use Cases**: Global load balancing, SSL offloading, WAF
- **Key Features**:
  - Global HTTP/HTTPS load balancing
  - URL-based routing
  - SSL offloading
  - Session affinity
- **Pricing Model**: Based on data transfer and routing rules

## Database Services

### Azure SQL Database
- **Description**: Fully managed relational database service
- **Use Cases**: Web applications, business applications, content management systems
- **Key Features**:
  - Built-in high availability
  - Automated backups and point-in-time restore
  - Intelligent performance optimization
  - Advanced security features
- **Pricing Model**: DTU-based or vCore-based pricing options

### Azure Cosmos DB
- **Description**: Globally distributed, multi-model database service
- **Use Cases**: Web and mobile applications, IoT, gaming
- **Key Features**:
  - Multiple APIs (SQL, MongoDB, Cassandra, Gremlin, Table)
  - Global distribution
  - Multi-master replication
  - Sub-10 ms latency
- **Pricing Model**: Based on provisioned throughput and storage

### Azure Database for MySQL
- **Description**: Fully managed MySQL database service
- **Use Cases**: Web applications, mobile apps, e-commerce
- **Key Features**:
  - High availability (up to 99.99% SLA)
  - Automatic backups
  - Point-in-time restore
  - Intelligent performance recommendations
- **Pricing Model**: Based on compute, storage, and backup needs

### Azure Database for PostgreSQL
- **Description**: Fully managed PostgreSQL database service
- **Use Cases**: Complex queries, geospatial applications, OLTP
- **Key Features**:
  - Single server and Hyperscale (Citus) options
  - Automatic patching
  - Built-in security
  - Scaling capabilities
- **Pricing Model**: Compute-based with separate storage charges

### Azure Synapse Analytics
- **Description**: Analytics service that combines enterprise data warehousing and big data
- **Use Cases**: Enterprise data warehousing, big data analytics
- **Key Features**:
  - SQL and Spark pools
  - Built-in data integration
  - Code-free data orchestration
  - Unified experience for analytics
- **Pricing Model**: Based on compute, storage, and activities

### Azure Database Migration Service
- **Description**: Service to migrate databases to Azure with minimal downtime
- **Use Cases**: Migrating on-premises databases to Azure
- **Key Features**:
  - Database assessment
  - Schema conversion
  - Data migration
  - Minimal downtime options
- **Pricing Model**: Based on Standard or Premium tiers

## Identity and Access Services

### Azure Active Directory (Azure AD)
- **Description**: Cloud-based identity and access management service
- **Use Cases**: Identity management, single sign-on, application access management
- **Key Features**:
  - Single sign-on (SSO)
  - Multi-factor authentication
  - Conditional Access
  - Privileged Identity Management
- **Pricing Model**: Free, Premium P1, and Premium P2 tiers

### Azure Active Directory Domain Services
- **Description**: Managed domain services compatible with Windows Server Active Directory
- **Use Cases**: Lift-and-shift of legacy directory-aware apps to Azure
- **Key Features**:
  - Domain join
  - Group Policy
  - LDAP
  - Kerberos/NTLM authentication
- **Pricing Model**: Based on directory objects

### Azure Role-Based Access Control (RBAC)
- **Description**: Authorization system to manage access to Azure resources
- **Use Cases**: Implementing least privilege access
- **Key Features**:
  - Fine-grained access management
  - Built-in roles and custom roles
  - Multiple scope levels (management group, subscription, resource group, resource)
  - Access reviews
- **Pricing Model**: Included with Azure subscription

### Azure AD B2C
- **Description**: Customer identity and access management solution
- **Use Cases**: Customer-facing applications, consumer identity management
- **Key Features**:
  - Social identity providers
  - Customizable user experiences
  - Scalable for millions of users
  - Standards-based authentication
- **Pricing Model**: Free tier plus per-authentication actions

## Management and Governance Services

### Azure Portal
- **Description**: Web-based, unified console for managing Azure resources
- **Use Cases**: Resource management, monitoring, configuration
- **Key Features**:
  - Visual resource management
  - Customizable dashboards
  - Cloud Shell integration
  - Role-based access
- **Pricing Model**: Free

### Azure Resource Manager
- **Description**: Deployment and management service for Azure
- **Use Cases**: Resource provisioning, organizing, controlling access
- **Key Features**:
  - Declarative templates
  - Resource grouping
  - Access control
  - Tagging
- **Pricing Model**: Free

### Azure Policy
- **Description**: Service to create, assign, and manage policies for resources
- **Use Cases**: Enforcing standards, compliance assessment, resource protection
- **Key Features**:
  - Policy definitions
  - Initiative definitions
  - Assignments
  - Compliance reporting
- **Pricing Model**: Free for basic features, charges for certain add-ons

### Azure Blueprints
- **Description**: Enables creating standardized resource configurations
- **Use Cases**: Governance for new environments, standards enforcement
- **Key Features**:
  - Artifacts packaging (RBACs, policies, ARM templates)
  - Blueprint versioning
  - Locking controls
  - Auditing and tracking
- **Pricing Model**: Free

### Azure Monitor
- **Description**: Comprehensive monitoring solution for Azure resources
- **Use Cases**: Performance monitoring, alert management, autoscaling
- **Key Features**:
  - Metrics and logs collection
  - Custom dashboards
  - Alert rules
  - Autoscale rules
- **Pricing Model**: Based on data ingestion and retention

### Azure Security Center
- **Description**: Unified security management and threat protection
- **Use Cases**: Security posture management, threat protection
- **Key Features**:
  - Security assessments
  - Threat protection
  - Just-in-time VM access
  - Adaptive application controls
- **Pricing Model**: Free tier and Standard tier (per node)

### Azure Advisor
- **Description**: Personalized consultant service for best practices
- **Use Cases**: Optimization of Azure deployments
- **Key Features**:
  - Cost recommendations
  - Security recommendations
  - Performance recommendations
  - Reliability recommendations
- **Pricing Model**: Free

## Security Services

### Azure DDoS Protection
- **Description**: Protection against Distributed Denial of Service attacks
- **Use Cases**: Web applications, public-facing services
- **Key Features**:
  - Always-on monitoring
  - Automatic attack mitigation
  - Attack analytics
  - Cost protection
- **Pricing Model**: Basic (free) and Standard tiers

### Azure Key Vault
- **Description**: Cloud service for securely storing and accessing secrets
- **Use Cases**: Secret management, key management, certificate management
- **Key Features**:
  - Centralized secret management
  - Enhanced security for keys
  - Certificate management
  - Integration with Azure services
- **Pricing Model**: Based on operations and certificates

### Azure Information Protection
- **Description**: Cloud-based solution for classifying and protecting documents and emails
- **Use Cases**: Data protection, compliance, information rights management
- **Key Features**:
  - Classification labels
  - Data protection
  - Policy enforcement
  - Integration with Office 365
- **Pricing Model**: Based on subscription level

### Azure Sentinel
- **Description**: Cloud-native security information and event management (SIEM) solution
- **Use Cases**: Security monitoring, threat detection, incident response
- **Key Features**:
  - Data collection at cloud scale
  - Threat detection
  - Incident investigation
  - Automated response
- **Pricing Model**: Based on data ingestion

## Application Integration Services

### Azure Logic Apps
- **Description**: Cloud service for automating workflows and integrating applications
- **Use Cases**: System orchestration, business process automation, integration
- **Key Features**:
  - Visual designer
  - Prebuilt connectors
  - Enterprise integration
  - Serverless execution
- **Pricing Model**: Pay-per-execution model

### Azure Service Bus
- **Description**: Reliable cloud messaging as a service
- **Use Cases**: Application decoupling, asynchronous messaging, pub/sub scenarios
- **Key Features**:
  - Queues, topics, and subscriptions
  - Message sessions
  - Duplicate detection
  - Scheduled delivery
- **Pricing Model**: Based on operations and messaging units

### Azure Event Grid
- **Description**: Fully managed event routing service
- **Use Cases**: Event-based architectures, reactive programming
- **Key Features**:
  - Pub/sub model
  - Built-in events from Azure services
  - Custom topics
  - Event filtering
- **Pricing Model**: Pay per operation

### Azure API Management
- **Description**: Full-featured API management platform
- **Use Cases**: Publishing APIs, centralizing API program
- **Key Features**:
  - API gateway
  - Developer portal
  - Policy enforcement
  - Analytics
- **Pricing Model**: Tiered pricing based on features and scale

## IoT Services

### Azure IoT Hub
- **Description**: Managed service to enable bi-directional communication between IoT devices and application backend
- **Use Cases**: IoT device management, telemetry collection
- **Key Features**:
  - Device-to-cloud and cloud-to-device messaging
  - Device provisioning
  - Device management
  - Security and authentication
- **Pricing Model**: Based on edition and number of messages

### Azure IoT Central
- **Description**: Fully managed IoT SaaS solution
- **Use Cases**: IoT application implementation without cloud expertise
- **Key Features**:
  - Device templates
  - Rules and actions
  - Analytics and dashboards
  - Industry-specific templates
- **Pricing Model**: Based on number of devices

### Azure Sphere
- **Description**: Secured, high-level application platform with built-in communication and security for internet-connected devices
- **Use Cases**: Secure IoT device development
- **Key Features**:
  - Secured MCU
  - Operating system
  - Cloud security service
  - Update management
- **Pricing Model**: Based on device activation

## DevOps and Developer Tools

### Azure DevOps
- **Description**: Services for teams to share code, track work, and ship software
- **Use Cases**: CI/CD, project management, code repositories
- **Key Features**:
  - Boards (agile planning)
  - Repos (Git repositories)
  - Pipelines (CI/CD)
  - Test Plans
  - Artifacts (package management)
- **Pricing Model**: Free tier with paid user licenses for additional features

### GitHub and GitHub Actions
- **Description**: Code hosting platform with built-in CI/CD
- **Use Cases**: Open-source collaboration, software development lifecycle
- **Key Features**:
  - Code repositories
  - Pull requests
  - Issue tracking
  - CI/CD automation
- **Pricing Model**: Free tier with paid plans for teams and organizations

### Azure DevTest Labs
- **Description**: Service for quickly creating environments for dev/test
- **Use Cases**: Development and testing environments, training, hackathons
- **Key Features**:
  - VM templates
  - Cost control
  - Policies
  - Auto-shutdown
- **Pricing Model**: Pay only for lab resources used

## AI and Machine Learning Services

### Azure Machine Learning
- **Description**: Cloud-based service for developing, training, testing, deploying, and managing ML models
- **Use Cases**: Predictive analytics, image classification, data science
- **Key Features**:
  - Automated ML
  - Designer (drag-and-drop interface)
  - MLOps capabilities
  - Integration with open-source frameworks
- **Pricing Model**: Based on compute, storage, and other resources used

### Azure Cognitive Services
- **Description**: Suite of APIs for adding AI capabilities to applications
- **Use Cases**: Image recognition, speech recognition, language understanding
- **Key Features**:
  - Vision services
  - Speech services
  - Language services
  - Decision services
- **Pricing Model**: Tiered pricing based on service and number of transactions

### Azure Bot Service
- **Description**: Platform for developing, publishing, and managing bots
- **Use Cases**: Customer service, information retrieval, productivity
- **Key Features**:
  - Multiple channel integration
  - Bot Framework SDK
  - Templates for common scenarios
  - Bot analytics
- **Pricing Model**: Free tier with premium features

## Analytics and Big Data Services

### Azure HDInsight
- **Description**: Fully managed, open-source analytics service
- **Use Cases**: Data extraction, transformation, and loading (ETL), data warehousing, machine learning
- **Key Features**:
  - Hadoop, Spark, Hive, LLAP, Kafka, Storm, R
  - Enterprise Security Package
  - Integration with other Azure services
  - Automation and monitoring
- **Pricing Model**: Based on cluster type, size, and duration

### Azure Databricks
- **Description**: Apache Spark-based analytics platform
- **Use Cases**: Big data processing, machine learning, real-time analytics
- **Key Features**:
  - Fully managed Spark clusters
  - Interactive workspace
  - Enterprise security
  - Integration with Azure services
- **Pricing Model**: Based on Databricks Units (DBUs)

### Azure Data Factory
- **Description**: Data integration service for ETL at scale
- **Use Cases**: Data migration, data warehouse population, log processing
- **Key Features**:
  - Visual ETL/ELT design
  - Code-free data transformation
  - Global data movement
  - Control flow activities
- **Pricing Model**: Based on activities, pipeline runs, and data movement

### Azure Stream Analytics
- **Description**: Real-time analytics service for streaming data
- **Use Cases**: IoT analytics, real-time dashboards, anomaly detection
- **Key Features**:
  - Real-time processing
  - SQL-like query language
  - Integration with Azure services
  - Machine learning integration
- **Pricing Model**: Based on streaming units

## Additional Services

### Azure Content Delivery Network (CDN)
- **Description**: Global content delivery network for static content
- **Use Cases**: Web content delivery, media streaming, gaming assets
- **Key Features**:
  - Global point-of-presence
  - Dynamic site acceleration
  - Rules engine
  - Analytics
- **Pricing Model**: Based on outbound data transfers and requests

### Azure Media Services
- **Description**: Cloud-based media workflow platform
- **Use Cases**: Live streaming, video on demand, content protection
- **Key Features**:
  - Live and on-demand streaming
  - Content protection
  - Encoding
  - Media analytics
- **Pricing Model**: Based on resources used (encoding, streaming, etc.)

### Azure Search
- **Description**: AI-powered cloud search service
- **Use Cases**: Web/mobile apps, enterprise search, app search
- **Key Features**:
  - Full-text search
  - AI-powered relevance
  - Multi-language support
  - Geo-spatial search
- **Pricing Model**: Based on tiers with different features and scale

### Azure Maps
- **Description**: Geospatial services for adding maps, spatial analytics, and mobility to apps
- **Use Cases**: Location-based apps, IoT spatial analytics, logistics
- **Key Features**:
  - Interactive maps
  - Route finding
  - Traffic data
  - Geolocation services
- **Pricing Model**: Based on number of transactions

## Azure Pricing and Support

### Azure Pricing Calculator
- **Description**: Tool for estimating Azure costs
- **Use Cases**: Budget planning, solution cost estimation
- **Key Features**:
  - Service selection
  - Configuration options
  - Total cost calculation
  - Export and sharing
- **Pricing Model**: Free

### Azure Total Cost of Ownership (TCO) Calculator
- **Description**: Tool to estimate cost savings of migrating to Azure
- **Use Cases**: Migration planning, financial justification
- **Key Features**:
  - On-premises infrastructure analysis
  - Azure equivalent calculation
  - Detailed reports
  - Customizable assumptions
- **Pricing Model**: Free

### Azure Support Plans
- **Description**: Technical support options for Azure
- **Use Cases**: Issue resolution, technical guidance
- **Key Features**:
  - Basic: Self-help resources
  - Developer: Trial/non-production guidance
  - Standard: Production workload support
  - Professional Direct: Business-critical support
  - Premier: Comprehensive support with designated managers
- **Pricing Model**: Monthly fee based on plan level

### Azure Service Level Agreements (SLAs)
- **Description**: Commitments for uptime and connectivity
- **Use Cases**: Understanding service reliability, planning redundancy
- **Key Features**:
  - Uptime percentages
  - Service credits for downtime
  - Composite SLAs for solutions
  - Exclusions and limitations
- **Pricing Model**: Included with services

This reference guide covers the core Azure services you'll need to understand for the AZ-900 exam. Each service includes a brief description, common use cases, key features, and pricing model to help you grasp the fundamentals. As Azure constantly evolves, always refer to the official Microsoft documentation for the most current information.