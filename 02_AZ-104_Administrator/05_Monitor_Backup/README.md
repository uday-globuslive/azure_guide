# Monitor and Backup (AZ-104)

## Azure Monitor Fundamentals

### Monitor Data Platform
- **Metrics**: Numerical values collected at regular intervals
- **Logs**: Text-based data with detailed information
- **Traces**: Application-level diagnostic data
- **Changes**: Configuration change tracking data
- Data collection methods and agents
- Data retention periods and costs

### Data Sources
- **Azure resources**: Platform metrics and resource logs
- **Operating systems**: Windows and Linux performance data
- **Applications**: Code-level telemetry
- **Subscriptions**: Administrative activity logs
- **Tenants**: Azure AD logs and security data
- **Custom sources**: REST API data ingestion

### Metrics
- Standard and custom metrics
- Namespace organization
- Dimensions for filtering
- Units and aggregation types
- Real-time monitoring capabilities
- Metrics Explorer for visualization

### Log Analytics
- Log data schema and tables
- Kusto Query Language (KQL) basics
- Creating and saving queries
- Custom log collection
- Log Analytics workspaces
- Workspace design and consolidation

## Monitoring Azure Resources

### VM Monitoring
- Azure Monitor for VMs
- VM insights for performance analysis
- Guest OS metrics and diagnostics
- VM extensions for monitoring
- Dependency mapping
- Data collection rules

### Storage Monitoring
- Storage account metrics
- Transaction and capacity metrics
- Replication and availability metrics
- Storage Analytics logging
- Blob, file, queue, and table service monitoring

### Network Monitoring
- Network Watcher capabilities
- Connection monitoring
- NSG flow logs
- Traffic Analytics
- ExpressRoute monitoring
- DNS Analytics

### Database Monitoring
- SQL Database and SQL Managed Instance
- Azure Database for MySQL, PostgreSQL, MariaDB
- Cosmos DB metrics and logs
- Query performance insights
- Automatic tuning recommendations

### App Service Monitoring
- Web app metrics and quotas
- Application logging
- Web server logging
- Deployment logging
- Service health integration

### AKS Monitoring
- Container insights
- Node and pod metrics
- Container logs
- Kubernetes cluster monitoring
- Prometheus integration

## Advanced Monitoring

### Application Insights
- Web application instrumentation
- Server-side and client-side telemetry
- Dependency tracking
- Performance monitoring
- Availability tests
- User session analysis

### Service Health
- Azure service issues
- Planned maintenance events
- Health advisories
- Health history
- Resource health
- Customized dashboard views

### Network Insights
- ExpressRoute circuits
- Application gateways
- Load balancers
- Virtual networks
- Network connections
- Traffic analytics

### Alerts and Notifications
- Alert rule components
  - Target resource
  - Condition/signal
  - Actions
  - Alert details
- Alert severity levels
- Action groups configuration
- Notification methods
  - Email
  - SMS
  - Voice call
  - Azure mobile app
  - Webhook
  - ITSM tools
  - Logic Apps
  - Runbooks
- Smart groups for alert correlation
- Alert processing rules

### Workbooks
- Interactive report templates
- Metric and log visualizations
- Parameter-based filtering
- Sharing and collaboration
- Templates and gallery

### Dashboards
- Customized monitoring views
- Resource-centric dashboards
- Shared dashboards
- Dashboard creation methods
- Dashboard organization

## Azure Backup

### Backup Fundamentals
- **Recovery Services vault**: Central management point for backups
- **Backup policy**: Schedule, retention, and rule set for backups
- **Backup instance**: Individual protected resource
- **Recovery point**: Specific point-in-time backup
- Backup types (full, incremental, differential)
- Storage redundancy options
- Backup Reports

### VM Backup
- Azure VM backup
- VM agent requirements
- File-level recovery
- Disk-level recovery
- Full VM recovery
- Cross-region restore
- Instant restore capabilities

### Storage Backup
- Azure Files backup
- Storage account backup
- Backup Center integration
- Snapshot management
- Operational tier vs. archive tier

### Database Backup
- SQL Server in Azure VMs
- Azure SQL Database
- SQL Managed Instance
- Long-term retention policies
- Point-in-time recovery

### App Backup
- SAP HANA in Azure VMs
- Workload-aware backup
- Application-consistent snapshots
- Pre/post scripts for applications
- VSS integration for Windows

### Backup Monitoring
- Backup jobs monitoring
- Backup alerts configuration
- Backup Reports
- Power BI integration
- Success/failure monitoring

## Azure Site Recovery (ASR)

### ASR Fundamentals
- **Disaster recovery as a service (DRaaS)**
- **Recovery Services vault**: Central management for ASR
- **Replication policy**: Rules governing replication
- Recovery plans for orchestrated failover
- Site Recovery components
  - Configuration server
  - Process server
  - Master target server

### Replication Scenarios
- Azure VM to secondary region
- VMware VMs to Azure
- Hyper-V VMs to Azure
- Physical servers to Azure
- Migration scenarios with ASR

### Recovery Plans
- Ordered failover groups
- Scripts and manual actions
- Azure Automation integration
- Multi-tier application recovery
- Network configuration during failover

### Network Recovery
- Network mapping
- Public IP reservation
- Load balancer configuration
- Network security group settings
- Traffic Manager integration

### ASR Testing
- Test failover
- Cleanup after test failover
- Failover/failback validation
- Recovery time objective (RTO) measurement
- Recovery point objective (RPO) validation

## Azure Archive and Long-term Retention

### Archive Storage
- Archive access tier for blobs
- Rehydration options and priorities
- Lifecycle management
- Archival policies and automation
- Cold storage cost optimization

### Long-term Backup Retention
- Compliance and regulatory requirements
- Backup retention policy configuration
- GFS (Grandfather-Father-Son) scheme
- Tape replacement strategies
- Azure Backup long-term retention

### Immutable Storage
- Time-based retention policy
- Legal hold policy
- Write-once-read-many (WORM) storage
- Policy configuration and management
- Compliance certification

## Monitoring and Backup Automation

### Azure Automation
- Runbooks for monitoring tasks
- Alert remediation with automation
- Scheduled maintenance scripts
- Backup verification with Automation
- State configuration for consistency

### Logic Apps
- Integrating with monitoring alerts
- Custom notification workflows
- Cross-platform automation
- Remediation action orchestration
- Advanced notification scenarios

### Azure Policy
- Enforcing monitoring requirements
- Backup compliance policies
- Automatic remediation
- Audit vs. enforce modes
- Initiative definitions for monitoring

### Resource Graph
- Querying resource state at scale
- Monitoring coverage analysis
- Backup compliance reporting
- Custom dashboard integration
- Resource tracking and visualization

## Cost Management for Monitoring and Backup

### Monitor Cost Optimization
- Log Analytics data volume management
- Data collection optimization
- Monitoring coverage planning
- Retention period adjustment
- Workspace consolidation

### Backup Cost Management
- Storage tier optimization
- Retention policy adjustment
- Backup frequency optimization
- Incremental backup strategies
- Reserved capacity options

### Pricing Models
- Pay-as-you-go vs. capacity reservations
- Commitment tiers
- Azure Hybrid Benefit application
- Direct vs. partner procurement options
- Enterprise Agreement considerations

## Governance and Compliance

### Monitoring Governance
- Centralized vs. decentralized models
- Workspace access control
- Query permissions
- Dashboard sharing
- Cross-tenant monitoring

### Backup Governance
- Backup policy standards
- Recovery Services vault design
- Cross-region backup strategies
- Access control for backup operations
- Role-based access control (RBAC)

### Regulatory Compliance
- Audit logging requirements
- Evidence collection and reporting
- Data residency considerations
- Retention requirements
- Encryption and security controls

## Best Practices

### Design Recommendations
- Monitoring architecture planning
- Backup strategy development
- Recovery planning
- Alert and notification strategy

### Operational Excellence
- Monitoring automation
- Backup testing and validation
- Documentation and runbooks
- Incident response integration

### Performance Optimization
- Monitoring agent performance
- Backup window planning
- Network throughput considerations
- Recovery time optimization

## Hands-On Exercises

1. Configure Azure Monitor for infrastructure and applications
2. Set up alert rules and notification channels
3. Create and configure a Recovery Services vault for VMs
4. Implement Azure Site Recovery between regions
5. Develop monitoring dashboards and workbooks
6. Configure long-term retention backup policies
7. Test recovery procedures for critical workloads