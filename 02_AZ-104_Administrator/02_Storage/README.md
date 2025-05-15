# Azure Storage (AZ-104)

## Storage Account Fundamentals

### Storage Account Types
- **General-purpose v2 (GPv2)**: Standard storage account for blobs, files, queues, and tables
- **Premium block blobs**: High-performance storage for block blobs and append blobs
- **Premium file shares**: High-performance storage for file shares
- **Premium page blobs**: High-performance storage for page blobs (VHD/disk images)

### Performance Tiers
- **Standard**: Using HDD, lower cost, higher latency
- **Premium**: Using SSD, higher cost, lower latency
- Performance targets and scalability
- Throughput and IOPS limits

### Redundancy Options
- **Locally Redundant Storage (LRS)**: Three copies in a single data center
- **Zone-Redundant Storage (ZRS)**: Three copies across availability zones
- **Geo-Redundant Storage (GRS)**: Six copies across two regions (LRS in primary + LRS in secondary)
- **Geo-Zone-Redundant Storage (GZRS)**: Six copies across two regions (ZRS in primary + LRS in secondary)
- **Read-Access Geo-Redundant Storage (RA-GRS)**: GRS with read access to secondary region
- **Read-Access Geo-Zone-Redundant Storage (RA-GZRS)**: GZRS with read access to secondary region
- Failover process and considerations

### Access Tiers
- **Hot**: Frequently accessed data, higher storage cost, lower access cost
- **Cool**: Infrequently accessed data, lower storage cost, higher access cost
- **Archive**: Rarely accessed data, lowest storage cost, highest access/rehydration cost
- Tier lifecycle management and automation
- Rehydration priorities for archived data

## Blob Storage

### Blob Types
- **Block blobs**: Text and binary data, up to 4.75 TB
- **Append blobs**: Optimized for append operations, like logging
- **Page blobs**: Random access files, used for VHDs and disks

### Container Organization
- Container creation and management
- Public access levels
  - Private
  - Blob (anonymous read access for blobs only)
  - Container (anonymous read access for entire container)
- Container properties and metadata

### Blob Operations
- Uploading and downloading blobs
- Copying blobs between containers/accounts
- Leasing blobs to prevent modifications
- Snapshots and versioning
- Soft delete and protection policies
- Immutable storage for WORM (Write Once, Read Many)

### Blob Storage Security
- Shared Access Signatures (SAS)
  - Service SAS (account level)
  - Stored Access Policy (container level)
  - User delegation SAS (Azure AD integration)
- Encryption options
- Private endpoints and network controls

### Special Blob Services
- **Static website hosting**: Serving static content directly from blob storage
- **CDN integration**: Caching blobs at edge locations
- **Object replication**: Asynchronously copying blobs between accounts
- **Data Lake Storage Gen2**: Hierarchical namespace for big data analytics

## Azure Files

### File Share Types and Performance
- Standard file shares (on HDD)
- Premium file shares (on SSD)
- Performance scaling and targets
- Large file share support (up to 100 TiB)

### File Share Protocols
- Server Message Block (SMB) 2.1, 3.0, and 3.1.1
- Network File System (NFS) 4.1
- REST API access
- Protocol compatibility considerations

### File Share Access
- Mounting on Windows, macOS, and Linux
- Connecting from on-premises networks
- Access control with Active Directory integration
- AD-based authentication
- Share-level permissions

### File Sync
- Azure File Sync components
  - Storage Sync Service
  - Sync Groups
  - Cloud Endpoints
  - Server Endpoints
- Deploying sync agents
- Cloud tiering for capacity optimization
- Sync topology patterns

## Shared Access Signatures (SAS)

### SAS Types
- **Account SAS**: Access to multiple services in a storage account
- **Service SAS**: Access to a specific service (blob, file, queue, table)
- **User delegation SAS**: Secured with Azure AD credentials

### SAS Parameters
- Time-based restrictions (start time, expiry time)
- Permissions (read, write, delete, list, etc.)
- IP address restrictions
- Protocol restrictions (HTTPS only)
- Using stored access policies

### SAS Security Practices
- Generating and distributing SAS securely
- Monitoring SAS usage
- Revoking access with stored access policies
- Using short-lived tokens
- Alternatives to SAS

## Storage Security

### Encryption
- Encryption at rest (Azure Storage Service Encryption)
- Encryption in transit (HTTPS/TLS)
- Client-side encryption
- Customer-managed keys with Azure Key Vault
- Double encryption

### Network Security
- Service endpoints for virtual networks
- Private endpoints and Private Link
- Network security groups and service tags
- Firewall rules (IP and VNET-based)
- Cross-origin resource sharing (CORS)

### Authentication and Authorization
- Azure AD integration for blob and queue storage
- Role-based access control (RBAC)
- Azure Storage authorization hierarchy
- Shared Key authentication
- Anonymous access control

## Data Movement and Migration

### Azure Storage Explorer
- Managing storage resources graphically
- Uploading and downloading content
- Managing SAS tokens and permissions
- Cross-account operations

### AzCopy
- Installing and configuring AzCopy
- Basic copy operations
- Sync operations
- Resume functionality
- Using AzCopy with SAS tokens or Azure AD

### Azure Import/Export
- Preparing drives using WAImportExport tool
- Creating import and export jobs
- Shipping logistics and considerations
- Tracking job status and completion

### Azure Data Box
- Data Box family of products
  - Data Box Disk (up to 40 TB)
  - Data Box (up to 100 TB)
  - Data Box Heavy (up to 1 PB)
- Ordering and logistics
- Data transfer workflow
- Security and encryption

## Storage Monitoring and Troubleshooting

### Monitoring Tools
- Azure Monitor metrics
- Storage Analytics metrics and logging
- Activity logs
- Azure Storage Explorer diagnostics
- Alerting and notifications

### Common Issues
- Connectivity problems
- Performance bottlenecks
- Throttling and capacity limits
- Authentication and authorization failures
- Diagnostic logging and analysis

### Cost Optimization
- Right-sizing storage accounts
- Appropriate redundancy selection
- Lifecycle management policies
- Capacity planning and forecasting
- Reserved capacity options

## Azure NetApp Files

### ANF Overview
- Enterprise-grade file service built on NetApp technology
- Ultra-high performance
- Multiple protocol support (NFS, SMB)
- Integration with on-premises NetApp deployments

### Service Levels
- Standard
- Premium
- Ultra
- Capacity management and service level changes

### Data Protection
- Snapshots and cloning
- Backup integration
- Cross-region replication

## Best Practices

### Design Recommendations
- Storage type selection guidelines
- Performance optimization strategies
- Cost optimization strategies
- Security hardening

### Operational Excellence
- Automation with ARM templates and scripts
- Monitoring and alerting setup
- Disaster recovery planning
- Regular security reviews

## Hands-On Exercises

1. Create and configure storage accounts with different redundancy options
2. Implement lifecycle management for blob storage
3. Set up Azure File Sync between on-premises and Azure
4. Configure and use Shared Access Signatures securely
5. Implement network security for storage accounts
6. Perform data migration using AzCopy and Azure Data Box
7. Set up monitoring and alerting for storage resources