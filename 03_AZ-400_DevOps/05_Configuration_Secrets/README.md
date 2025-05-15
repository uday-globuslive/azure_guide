# Configuration and Secrets Management (AZ-400)

## Configuration Management Fundamentals

### Configuration Management Concepts
- **Definition**: Practice of systematically handling changes to a system's configuration
- **Core Components**:
  - Configuration items: Individual settings or parameters
  - Configuration store: Repository for storing configuration data
  - Configuration schema: Structure and validation rules
  - Configuration versioning: Tracking changes over time
- **Configuration vs. Code**: Separating what changes frequently from what doesn't
- **Core Principles**:
  - Separation of configuration from code
  - Configuration as versioned artifacts
  - Environment-specific configuration
  - Single source of truth
  - Configuration validation

### Configuration Management Benefits
- Consistency across environments
- Reduced configuration drift
- Simplified troubleshooting
- Faster deployments
- Improved security
- Better compliance and auditability

### Configuration Management Scope
- Application settings
- Infrastructure configuration
- Environment variables
- Feature flags
- Connection strings
- API endpoints
- Runtime settings
- System behavior controls

## Azure App Configuration

### Azure App Configuration Overview
- **Definition**: Managed service for storing application settings
- **Key Features**:
  - Centralized management
  - Point-in-time snapshots
  - Schema validation
  - Encryption for sensitive values
  - RBAC access control
  - Change history and comparison
- **Integration Points**:
  - Azure DevOps
  - Azure Pipelines
  - GitHub Actions
  - Various application frameworks

### Key-Value Organization
- Hierarchical key naming
- Labeling for environment separation
- Key namespaces
- Grouping configurations
- Naming conventions
- Tags and metadata

### Configuration Versioning
- Version tracking
- Point-in-time snapshots
- Rollback capabilities
- Comparing configurations
- Audit trail for changes
- Change notifications

### App Configuration SDK Integration
- .NET Core integration
- Java Spring integration
- JavaScript/Node.js integration
- Python integration
- Refresh strategies
- Caching mechanisms

### Secure Access to App Configuration
- Azure AD integration
- Key Vault references
- Managed identities
- RBAC configuration
- Private endpoints
- Network isolation

## Feature Management

### Feature Flags Concepts
- **Definition**: Toggles that control application behavior
- **Feature Flag Types**:
  - Release toggles
  - Experiment toggles
  - Ops toggles
  - Permission toggles
  - Kill switches
- **Benefits**:
  - Risk reduction
  - Gradual rollout
  - A/B testing
  - Ring deployment
  - Emergency off switches

### Azure App Configuration Feature Manager
- Feature flag structure
- Conditions and rules
- Targeting specific users
- Percentage rollouts
- Time-based activation
- Integration with App Configuration

### Feature Flags in Code
- .NET Feature Management
- JavaScript libraries
- Java libraries
- Python libraries
- Dependency injection
- Middleware integration

### Feature Flag Best Practices
- Naming conventions
- Short-lived vs. long-lived flags
- Documentation
- Feature flag cleanup
- Testing with feature flags
- Monitoring flag usage

### Feature Experimentation
- A/B testing implementation
- Target audience segmentation
- Metrics collection
- Experimentation analysis
- Data-driven decision making
- Gradual feature rollout

## Environment Configuration

### Environment Types
- Development
- Integration
- Testing/QA
- Staging/Pre-production
- Production
- Disaster recovery
- Special purpose environments
  - Security testing
  - Performance testing
  - Demo environments

### Environment Configuration Strategies
- **Tokenization**:
  - Using placeholders in configuration
  - Variable substitution during deployment
  - Token format standardization
- **Environment-specific files**:
  - Different files per environment
  - File naming conventions
  - Source control management
- **Runtime configuration injection**:
  - Environment variables
  - Command-line arguments
  - Configuration providers

### Configuration Transformation
- XML transformations
- JSON transformations
- YAML transformations
- Parameterized ARM templates
- Environment variable substitution
- Parameter files for deployment

### Environment Promotion
- Progressive configuration updates
- Configuration validation
- Environmental drift detection
- Configuration synchronization
- Approval workflows
- Rollback strategies

## Secrets Management

### Secrets Management Fundamentals
- **Definition**: Secure handling of sensitive information like credentials, keys, and certificates
- **Types of Secrets**:
  - API keys
  - Connection strings
  - Authentication credentials
  - Encryption keys
  - Certificates
  - Tokens and passwords
- **Secret Management Principles**:
  - Never store secrets in code
  - Encrypt secrets at rest
  - Limit access to secrets
  - Audit secret access
  - Rotate secrets regularly
  - Centralize secret management

### Azure Key Vault

#### Key Vault Fundamentals
- **Definition**: Managed service for securing keys and secrets
- **Key Vault Types**:
  - Standard tier
  - Premium tier (HSM-backed)
- **Key Components**:
  - Keys: Cryptographic keys for encryption
  - Secrets: Sensitive text values
  - Certificates: X.509 certificates
- **Key Features**:
  - RBAC access control
  - Access policies
  - Soft-delete and purge protection
  - Backup and restore
  - Automatic renewal
  - Monitoring and logging

#### Key Vault Access Control
- Azure AD integration
- Managed identities
- Access policies
- RBAC implementation
- Privileged access management
- Network access control
- Private endpoints

#### Key Vault Monitoring
- Logging and auditing
- Azure Monitor integration
- Alert configuration
- Operational health monitoring
- Compliance monitoring
- Security monitoring

### Secrets in DevOps Pipelines

#### Azure DevOps Secret Variables
- Pipeline secret variables
- Variable groups
- Environment variables
- Secret variable scoping
- Variable templates
- Secret variable security

#### Key Vault Integration
- Azure DevOps Key Vault task
- GitHub Actions Key Vault integration
- Pipeline identity configuration
- Runtime secret access
- Just-in-time secret access
- Secret filtering in logs

#### Certificate Management
- Certificate storage in Key Vault
- Certificate retrieval in pipelines
- Certificate installation
- Using certificates for authentication
- Certificate renewal automation
- Certificate lifecycle management

## Configuration in CI/CD Pipelines

### Configuration in Build Pipelines
- Build-time configuration
- Conditional compilation
- Environment-specific artifacts
- Configuration bundling
- Configuration validation
- Pre-processing configuration

### Configuration in Release Pipelines
- Environment-specific variable groups
- Configuration replacement
- Configuration validation gates
- Configuration testing
- Approval workflows
- Environment configuration reports

### Infrastructure as Code (IaC) Configuration
- ARM template parameters
- Terraform variables
- Bicep parameters
- Pulumi configuration
- CloudFormation parameters
- Environment-specific parameters

### Database Configuration
- Database schema changes
- Database configuration scripts
- Data seeding
- Environment-specific data
- Migration scripts
- Configuration for database connections

## Advanced Configuration Patterns

### Service Discovery
- Service registration
- Service discovery mechanisms
- Dynamic endpoint configuration
- Health checking
- Load balancing configuration
- Circuit breaker configuration

### Externalized Configuration
- Configuration servers
- Distributed configuration
- Configuration replication
- Client-side caching
- Configuration prioritization
- Fallback mechanisms

### Configuration Change Management
- Change approval workflows
- Configuration impact analysis
- Automated testing of changes
- Gradual configuration rollout
- Configuration change notifications
- Emergency change procedures

### Zero-Downtime Configuration Updates
- Runtime configuration reloading
- Blue-green configuration switching
- Canary configuration testing
- Shadow configuration testing
- Configuration versioning
- Backward compatibility

## Configuration Security

### Configuration Security Best Practices
- Secure storage
- Encrypted transmission
- Principle of least privilege
- Configuration validation
- Audit logging
- Secure deployment practices

### Sensitive Data Handling
- Data classification
- Encryption strategies
- Key management
- Access controls
- Compliance requirements
- Data residency considerations

### Configuration Compliance
- Regulatory requirements
- Compliance validation
- Configuration baselines
- Compliance reporting
- Configuration drift detection
- Remediation automation

### Security Testing for Configuration
- Misconfiguration scanning
- Security validation
- Vulnerability assessment
- Penetration testing
- Configuration review
- Security benchmarks

## Configuration as Code

### Configuration Version Control
- Repository organization
- Branching strategies
- Pull request validation
- Merge checks
- History and blame
- Documentation as code

### Configuration Testing
- Validation testing
- Unit testing
- Integration testing
- Schema validation
- Linting and formatting
- Compliance testing

### Configuration Deployment Automation
- Continuous deployment
- Pipeline triggers
- Automated validation
- Rollback mechanisms
- Deployment monitoring
- Post-deployment verification

### Configuration Governance
- Standards and guidelines
- Configuration reviews
- Automated enforcement
- Exception management
- Training and awareness
- Continuous improvement

## Best Practices

### Configuration Design Patterns
- Hierarchical configuration
- Environment-based configuration
- Feature-based configuration
- Tenant-based configuration
- Role-based configuration
- Progressive configuration

### Secrets Management Best Practices
- Centralized secrets store
- Regular rotation
- Access auditing
- Just-in-time access
- Principle of least privilege
- Automated rotation

### Configuration Management Strategy
- Technology selection
- Implementation planning
- Team responsibilities
- Integration with DevOps
- Tool standardization
- Training and documentation

### Troubleshooting and Debugging
- Configuration logging
- Environment comparisons
- Configuration dumps
- History tracking
- Root cause analysis
- Remediation planning

## Hands-On Exercises

1. Set up Azure App Configuration for a multi-environment application
2. Implement feature flags for a web application using App Configuration
3. Configure Azure Key Vault and integrate with an application
4. Set up pipeline secrets using Azure DevOps variable groups
5. Implement configuration transformation in a CI/CD pipeline
6. Create a configuration validation gate in a release pipeline
7. Implement automatic secret rotation for a database connection
8. Set up environment-specific configuration with approval workflows