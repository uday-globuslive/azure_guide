# Continuous Integration and Continuous Delivery (CI/CD) (AZ-400)

## Continuous Integration Fundamentals

### CI Principles
- **Definition**: The practice of automating the integration of code changes from multiple contributors into a single software project
- **Core Practices**:
  - Frequent code commits
  - Automated building and testing
  - Fast feedback loops
  - Maintaining a single source of truth
  - Every commit is potentially releasable

### Benefits of CI
- Early detection of integration issues
- Reduced integration complexity
- Improved code quality
- Faster development cycles
- Increased developer productivity
- Consistent build process

### CI Workflow
- Code commit triggers
- Source code checkout
- Environment preparation
- Build code
- Run tests (unit, integration)
- Code quality analysis
- Artifact creation
- Reporting and notifications

## Implementing Build Pipelines

### Build Pipeline Components
- **Build Agent**: Physical or virtual machine that runs build tasks
- **Build Triggers**: Events that initiate the build (code push, PR, schedule)
- **Build Tasks**: Individual steps in the build process
- **Build Variables**: Dynamic values used during the build
- **Build Artifacts**: Outputs of the build process
- **Build Policy**: Rules governing the build process

### Azure Pipelines Overview
- Architecture and components
- Hosted vs. self-hosted agents
- Agent pools and capabilities
- Service connections and security
- Parallelism and job distribution
- Retention policies

### YAML Pipelines
- Pipeline as code benefits
- YAML schema and structure
- Multi-stage YAML pipelines
- Template reuse
- Conditional execution
- Environment-specific settings
- Migrating from classic to YAML

### Classic UI Pipelines
- Task-based visual editor
- Task groups for reusability
- Build variables and parameters
- Repository integration
- Build options and triggers
- Artifact publishing

### Build Strategies for Different Languages
- **.NET**:
  - MSBuild, dotnet CLI
  - NuGet package restoration
  - Unit testing with MSTest/NUnit/xUnit
  - Code coverage with Coverlet
- **Java**:
  - Maven, Gradle
  - Unit testing with JUnit
  - Code coverage with JaCoCo
- **Node.js**:
  - npm, Yarn
  - Testing with Jest/Mocha
  - Linting with ESLint
- **Python**:
  - pip, virtualenv
  - Testing with pytest
  - Linting with pylint/flake8

### Container Builds
- Docker image builds
- Multi-stage Dockerfiles
- Container registries
- Kubernetes manifests
- Helm charts
- Container scanning

## Continuous Delivery and Deployment

### CD Principles
- **Continuous Delivery**: Practice of ensuring code can be rapidly and safely deployed to production
- **Continuous Deployment**: Extension where every change that passes tests is automatically deployed to production
- **Core Practices**:
  - Deployment automation
  - Environment consistency
  - Configuration management
  - Deployment rollback capability
  - Staged deployments
  - Feature toggles

### Benefits of CD
- Reliable releases
- Faster time to market
- Lower deployment risk
- Quicker feedback from users
- Improved team collaboration
- Reduced manual overhead

### CD Workflow
- Build artifact promotion
- Environment provisioning
- Configuration application
- Deployment execution
- Smoke/Integration testing
- Performance testing
- Security validation
- Monitoring integration

## Implementing Release Pipelines

### Release Pipeline Components
- **Environments**: Deployment targets (Dev, Test, Staging, Production)
- **Stages**: Logical grouping of tasks for a specific environment
- **Approvals and Gates**: Control mechanisms for deployments
- **Deployment Groups**: Sets of target machines
- **Release Variables**: Dynamic values used during release
- **Deployment Strategies**: Methods for updating running applications

### Azure Release Pipelines
- Classic release pipelines
- Multi-stage YAML pipelines
- Environments and resources
- Service connections
- Artifact sources
- Release triggers
- Deployment jobs

### Approval and Gating
- Pre-deployment approvals
- Post-deployment approvals
- Gates (Azure Monitor, REST API, etc.)
- Scheduled deployments
- Manual interventions
- Restricting environment access

### Deployment Strategies
- **Blue-Green Deployment**:
  - Separate production and staging environments
  - Traffic switching
  - Quick rollback capability
- **Canary Deployment**:
  - Gradual traffic shifting
  - Limited user exposure
  - Progressive confidence building
- **Progressive Exposure (Ring Deployment)**:
  - Deploying to rings of users
  - Inner and outer rings
  - Controlled expansion
- **Feature Flags/Toggles**:
  - Runtime configuration for features
  - A/B testing support
  - Progressive feature roll-out
  - Integration with Azure App Configuration

### Infrastructure as Code in CD
- Azure Resource Manager (ARM) templates
- Bicep templates
- Terraform
- Azure CLI scripts
- PowerShell DSC
- Environment consistency
- Idempotent deployments

## Deployment Targets

### Azure App Service
- Deployment slots
- App settings and connection strings
- Deployment Center
- WebJobs and Functions
- Container deployments
- Traffic routing and testing in production

### Virtual Machines
- Deployment groups
- Remote PowerShell
- SSH-based deployments
- VM extensions
- Custom scripts
- Image-based deployment

### Kubernetes
- Azure Kubernetes Service (AKS)
- Kubernetes manifests
- Helm charts
- Kustomize
- Canary and blue-green strategies
- GitOps with Flux/ArgoCD

### Serverless
- Azure Functions deployment
- Consumption vs. premium plans
- Deployment slots
- CORS configuration
- Proxies and routes
- Durable functions

### Databases
- SQL Database schema updates
- DACPAC/BACPAC deployments
- Database migration
- Entity Framework migrations
- NoSQL database updates
- Data seeding

## Pipeline Security and Compliance

### Secure Pipelines
- Pipeline permissions and security
- Service connection security
- Agent pool security
- Variable groups and libraries
- Secure files
- Protected resources

### Secret Management
- Azure Key Vault integration
- Variable groups
- Secret variables
- Service connection credentials
- JIT access to secrets
- Auditing and rotation

### Compliance in Pipelines
- Security scanning
- Compliance checks
- Audit trails
- Approval workflows
- Environment segmentation
- Policy enforcement

### Pipeline Scanning and Validation
- Code scanning (SAST)
- Dependency scanning
- Container scanning
- License compliance
- Open source vulnerability scanning
- Compliance validation

## Testing in CI/CD

### Test Types in the Pipeline
- Unit testing
- Integration testing
- Functional testing
- UI testing
- Load and performance testing
- Security testing
- Acceptance testing

### Azure Test Plans Integration
- Manual test plans
- Test cases and test suites
- Test runs and results
- Bug filing
- Exploratory testing
- Test analytics

### Test Automation Frameworks
- MSTest, NUnit, xUnit
- JUnit, TestNG
- Jest, Mocha
- pytest
- Selenium, Cypress, Playwright
- Postman, Newman
- JMeter, Gatling

### Test Impact Analysis
- Selective test execution
- Test analytics
- Test coverage
- Code coverage
- Test pyramids
- Optimizing test feedback

## CI/CD Monitoring and Feedback

### Pipeline Analytics
- Pipeline insights
- Build and release analytics
- Test analytics
- Deployment metrics
- Resource utilization
- Bottleneck identification

### Pipeline Notifications
- Email notifications
- Microsoft Teams integration
- Slack integration
- Dashboard widgets
- Status badges
- Custom webhooks

### Application Monitoring
- Azure Monitor integration
- Application Insights
- Log Analytics
- Alerts and notifications
- Performance monitoring
- User behavior analytics

### Implementing Feedback Loops
- Pipeline feedback for developers
- Stakeholder feedback
- User feedback collection
- A/B testing
- Feature usage analysis
- Continuous improvement

## Advanced CI/CD Scenarios

### Microservices CI/CD
- Service-specific pipelines
- Composite builds
- Service contract testing
- Integration testing
- Deployment orchestration
- Service mesh integration

### Multi-platform Pipelines
- Cross-platform builds
- Multi-OS testing
- Cross-browser testing
- Mobile app deployment
- Hybrid cloud deployments
- Edge device deployment

### Scaling CI/CD
- Self-hosted agent pools
- Elastic build infrastructure
- Parallel execution
- Caching strategies
- Resource optimization
- Cost management

### Database DevOps
- Schema version control
- Migration-based deployment
- State-based deployment
- Data synchronization
- Rollback strategies
- Data masking for non-production

## Best Practices

### Pipeline Design Patterns
- Pipeline as code
- Template reuse
- DRY principles
- Standardized stages
- Environmental parity
- Artifact promotion

### Optimizing Pipeline Performance
- Caching dependencies
- Parallel execution
- Optimizing task order
- Selective testing
- Resource allocation
- Build agents sizing

### CI/CD Implementation Strategy
- Starting small
- Iterative improvement
- Measuring success
- Team engagement
- Documentation
- Training and enablement

### Troubleshooting
- Common pipeline issues
- Build failure analysis
- Deployment debugging
- Agent diagnostics
- Pipeline logs
- Recovery strategies

## Hands-On Exercises

1. Create a multi-stage YAML pipeline for a web application
2. Implement infrastructure as code with ARM templates
3. Configure automated testing in the CI pipeline
4. Set up blue-green deployment for an Azure App Service
5. Implement database deployment with schema migration
6. Configure release gates with Azure Monitor
7. Implement container-based CI/CD for a microservice architecture
8. Create a pipeline dashboard with key metrics