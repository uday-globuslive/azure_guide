# Dependencies and Package Management (AZ-400)

## Package Management Fundamentals

### Package Management Concepts
- **Definition**: System for distribution, sharing and consumption of reusable code
- **Core Components**:
  - Package: Deployable unit containing code and metadata
  - Feed: Repository for storing and distributing packages
  - Registry: Service hosting one or more feeds
  - Client: Tool for publishing and consuming packages
- **Benefits**:
  - Code reuse and sharing
  - Versioning and dependency management
  - Standardization and consistency
  - Security and governance

### Package Types and Formats
- **NuGet (.NET)**:
  - Package format: .nupkg
  - Manifest: .nuspec
  - Typical content: DLLs, dependencies, metadata
- **npm (JavaScript/Node.js)**:
  - Package format: tarball/directory
  - Manifest: package.json
  - Typical content: JS files, assets, configuration
- **Maven (Java)**:
  - Package format: JAR, WAR, EAR
  - Manifest: pom.xml
  - Typical content: compiled Java classes, resources
- **Python (PyPI)**:
  - Package format: wheel, egg, source distribution
  - Manifest: setup.py, pyproject.toml
  - Typical content: Python modules, native extensions
- **Docker (Containers)**:
  - Package format: image layers
  - Manifest: Dockerfile
  - Typical content: application files, runtime, dependencies
- **Helm (Kubernetes)**:
  - Package format: charts
  - Manifest: Chart.yaml, values.yaml
  - Typical content: Kubernetes manifests, templates

### Semantic Versioning
- **Version Structure**: MAJOR.MINOR.PATCH (e.g., 2.1.3)
  - MAJOR: Breaking changes
  - MINOR: New features, backward compatible
  - PATCH: Bug fixes, backward compatible
- **Pre-release Versions**: Alpha, Beta, RC (e.g., 2.0.0-alpha.1)
- **Version Ranges and Constraints**:
  - Exact (=2.1.3)
  - Greater than (>2.0.0)
  - Compatible (~2.1.0, ^2.1.0)
  - Range (>=2.0.0 <3.0.0)
- **Version Resolution**:
  - Dependency trees
  - Conflict resolution
  - Lock files

## Azure Artifacts

### Azure Artifacts Fundamentals
- **Overview**: Fully integrated package management solution in Azure DevOps
- **Supported Package Types**:
  - NuGet
  - npm
  - Maven
  - Python
  - Universal Packages
- **Key Features**:
  - Integration with Azure Pipelines
  - Package promotion across feeds
  - Upstream sources
  - Package retention policies
  - Feed permissions

### Feed Types and Visibility
- **Project-scoped Feeds**: Available to specific project
- **Organization-scoped Feeds**: Available to entire organization
- **Public Feeds**: Available to anyone (with limitations)
- **Feed Views**:
  - @local: Packages published directly to feed
  - @prerelease: Packages with prerelease versions
  - @release: Stable packages
  - Custom views for different stages

### Setting Up Azure Artifacts
- Creating and configuring feeds
- Feed permissions and access control
- Storage limits and quotas
- Retention policies
- License compliance settings
- Artifact upload and download tasks

### Package Promotion and Upstream Sources
- **Package Promotion**:
  - Moving packages between views
  - Promotion workflows
  - Quality gates
- **Upstream Sources**:
  - Connecting to public registries
  - Connecting to other internal feeds
  - Package caching
  - Proxy settings
  - Upstream priority order

### Using Universal Packages
- **Universal Package Format**: Versatile file storage format in Azure Artifacts
- Creating and uploading universal packages
- Versioning universal packages
- Downloading and consuming universal packages
- Use cases for universal packages
- Integration with CI/CD pipelines

## Package Management in CI/CD

### Package Restoration
- NuGet restore
- npm install
- Maven dependency resolution
- Optimizing restore performance
- Handling private packages
- Authentication in CI/CD

### Package Publishing
- Creating packages in the build
- Package versioning strategies
- Publishing to feeds
- Publishing during release
- Publishing approval workflows
- Artifact retention

### Package Governance in Pipelines
- Setting up policy validation
- Automated security scanning
- License compliance checking
- Quality gate enforcement
- Artifact signing
- Chain of custody

### Package Caching in Pipelines
- Pipeline caching mechanisms
- Package-specific caching strategies
- Cache invalidation rules
- Sharing cache across pipelines
- Optimizing cache hit rates
- Monitoring cache performance

## Container Registry Management

### Azure Container Registry (ACR)
- **Overview**: Private Docker registry service in Azure
- **Features**:
  - Geo-replication
  - Webhooks
  - Task automation
  - Content trust
  - Image scanning
- **SKUs**: Basic, Standard, Premium
- **Integration with Azure Services**:
  - AKS
  - App Service
  - Azure Container Instances
  - Azure Pipelines

### Managing Container Images
- Image tagging strategies
- Image promotion workflows
- Vulnerability scanning
- Registry integration with CI/CD
- Image cleanup and retention
- Storage optimization

### ACR Tasks
- Building container images
- Multi-architecture images
- Base image updates
- Scheduled tasks
- Task context
- Task triggering

### Container Security and Compliance
- Content trust and signing
- Azure Defender for container registries
- Image vulnerability scanning
- Access control
- Audit logging
- Network security

## Dependency Management

### Managing Direct Dependencies
- Explicitly declared dependencies
- Choosing dependency versions
- Updating dependencies
- Breaking change management
- Documentation and usage examples
- Testing with dependencies

### Managing Transitive Dependencies
- Dependency resolution mechanisms
- Handling conflicts
- Dependency locking
- Security vulnerabilities in transitive dependencies
- Dependency visualization
- Pruning unused dependencies

### Private vs. Public Dependencies
- Using private package sources
- Mirroring public packages
- Air-gapped development
- Package proxying
- Hybrid approaches
- Access tokens and authentication

### Dependency Security
- **Vulnerability Scanning**:
  - Static analysis
  - Known vulnerability databases
  - Automated alerts
- **Supply Chain Security**:
  - Vendor verification
  - Package integrity
  - Tamper protection
- **License Compliance**:
  - License detection
  - Approval workflows
  - Legal restrictions

## Dependency Analysis and Visualization

### Dependency Graphs
- Package relationships visualization
- Identifying problematic dependencies
- Detecting circular dependencies
- Measuring dependency depth
- Assessing upgrade impact
- Decision support for refactoring

### Vulnerability Reporting
- CVE identification
- Vulnerability severity assessment
- Remediation recommendations
- Update path planning
- Exemption workflows
- Compliance reporting

### Governance and Metrics
- Package usage statistics
- Dependency freshness metrics
- Version stability
- Support status tracking
- Cost attribution
- Risk assessment

### Dependency Monitoring
- Automated monitoring solutions
- Security alert systems
- Update notifications
- Breaking change alerts
- End-of-life warnings
- Integration with DevOps tools

## Advanced Package Management Scenarios

### Inner Source Packages
- Internal open source practices
- Documentation requirements
- Contribution workflows
- Version management
- Team ownership
- Adoption metrics

### Binary Artifact Storage
- Large file handling
- Version control integration
- Storage optimization
- Security and access control
- Retention and archiving
- Metadata management

### Package Management for Microservices
- Service versioning strategies
- Contract testing
- Dependency isolation
- Independent deployability
- Local development workflow
- Shared libraries management

### Multi-Environment Package Management
- Development feed strategy
- Testing feed strategy
- Production feed strategy
- Artifact promotion workflow
- Environment-specific configurations
- Compliance requirements

## Package Management Tools

### NuGet
- NuGet CLI
- dotnet CLI integration
- Visual Studio integration
- Package sources configuration
- Creating custom packages
- Symbol packages

### npm/Yarn
- npm CLI
- Yarn CLI
- package.json management
- npm scripts
- Workspaces
- Private registries

### Maven
- Maven CLI
- pom.xml configuration
- Repository configuration
- Build lifecycle
- Plugins and extensions
- Multi-module projects

### Python Package Management
- pip, pipenv, poetry
- requirements.txt
- Virtual environments
- Private PyPI servers
- Wheels vs. source distributions
- Binary dependencies

### Docker and Container Tools
- Docker CLI
- Docker Compose
- Multi-stage builds
- Image optimization
- Registry authentication
- Container scanning tools

## Best Practices

### Package Design Principles
- Single responsibility
- Minimal dependencies
- Stable interfaces
- Backward compatibility
- Forward compatibility planning
- Deprecation policies

### Versioning Strategies
- Synchronous version updates
- Independent versioning
- Monorepo versioning
- Breaking change management
- Release notes automation
- Major version planning

### Dependency Management Policies
- Defining approved sources
- Update frequency guidelines
- Security requirements
- License restrictions
- Deprecation handling
- Documentation standards

### Performance Optimization
- Minimizing dependency count
- Optimizing package size
- Caching strategies
- Parallel downloading
- Local mirroring
- Network optimization

## Hands-On Exercises

1. Set up Azure Artifacts feeds for different package types
2. Create and publish packages to Azure Artifacts
3. Configure upstream sources and package promotion
4. Implement dependency scanning in Azure Pipelines
5. Set up Azure Container Registry with vulnerability scanning
6. Create a container build and publish pipeline
7. Implement package retention and cleanup policies
8. Create dependency visualization for a complex application