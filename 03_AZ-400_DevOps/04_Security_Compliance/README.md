# Security and Compliance (AZ-400)

## DevSecOps Fundamentals

### What is DevSecOps?
- **Definition**: Integration of security practices within the DevOps process
- **Core Principles**:
  - Security as code
  - Shared responsibility for security
  - Early and continuous security testing
  - Automation of security controls
  - Building security expertise in development teams
- **Shift Left Security**: Moving security earlier in the development lifecycle

### Benefits of DevSecOps
- Earlier detection of vulnerabilities
- Reduced remediation costs
- Improved compliance posture
- Faster time to market for secure applications
- Increased collaboration between security and development
- Continuous security validation

### DevSecOps Framework
- **Security Requirements**: Defining security needs early
- **Threat Modeling**: Identifying potential threats and mitigations
- **Secure Coding**: Following best practices during development
- **Security Testing**: Continuous validation of code security
- **Security Monitoring**: Ongoing security observation in production
- **Incident Response**: Handling security events efficiently

## Secure Development Lifecycle

### Secure Design Principles
- **Defense in Depth**: Multiple layers of security controls
- **Principle of Least Privilege**: Minimal access rights
- **Separation of Duties**: Dividing critical functions
- **Privacy by Design**: Building in data protection
- **Fail Secure**: Secure default when errors occur
- **Attack Surface Reduction**: Minimizing exposure

### Threat Modeling
- **STRIDE Methodology**:
  - Spoofing
  - Tampering
  - Repudiation
  - Information disclosure
  - Denial of service
  - Elevation of privilege
- **Data Flow Diagrams**: Visualizing system interactions
- **Trust Boundaries**: Identifying security domain transitions
- **Threat Libraries**: Using known threat patterns
- **Mitigations**: Countermeasures for identified threats
- **Tooling**: Microsoft Threat Modeling Tool, OWASP Threat Dragon

### Security Requirements
- Security user stories
- Abuse cases
- Compliance requirements mapping
- Security acceptance criteria
- Non-functional security requirements
- Risk-based security prioritization

### Secure Coding Practices
- Input validation
- Output encoding
- Authentication and authorization
- Session management
- Error handling
- Data protection
- API security
- Language-specific secure coding standards

## Security Testing in the Pipeline

### Static Application Security Testing (SAST)
- **Purpose**: Finding vulnerabilities in source code
- **Integration Points**: IDE, PR validation, CI pipeline
- **Common Tools**:
  - SonarQube
  - Microsoft Security Code Analysis
  - Checkmarx
  - Fortify
  - ESLint security plugins
- **Implementation in Azure Pipelines**:
  - Task configuration
  - Quality gates
  - Policy enforcement
  - Results visualization

### Software Composition Analysis (SCA)
- **Purpose**: Finding vulnerabilities in dependencies
- **Integration Points**: Build, dependency management
- **Common Tools**:
  - OWASP Dependency Check
  - WhiteSource
  - Snyk
  - GitHub Dependabot
  - Black Duck
- **Implementation in Azure Pipelines**:
  - Task configuration
  - Version control integration
  - Vulnerability reporting
  - Auto-remediation options

### Dynamic Application Security Testing (DAST)
- **Purpose**: Finding vulnerabilities in running applications
- **Integration Points**: QA environment, staging
- **Common Tools**:
  - OWASP ZAP
  - Burp Suite
  - Arachni
  - Netsparker
  - Acunetix
- **Implementation in Azure Pipelines**:
  - Environment configuration
  - Test automation
  - Blocking vs. non-blocking issues
  - Continuous scanning

### Interactive Application Security Testing (IAST)
- **Purpose**: Finding vulnerabilities during test execution
- **Integration Points**: Test automation, QA environment
- **Common Tools**:
  - Contrast Security
  - Checkmarx Interactive
  - Seeker by Synopsys
- **Implementation in Azure Pipelines**:
  - Agent instrumentation
  - Test integration
  - Result collection
  - Continuous feedback

### Container Security Scanning
- Image vulnerability scanning
- Base image security
- Runtime security monitoring
- Registry security
- Container configuration validation
- Implementation in Azure Container Registry

### Infrastructure as Code (IaC) Security
- ARM template analysis
- Terraform plan validation
- CloudFormation security checks
- Bicep security validation
- Kubernetes manifest scanning
- Policy enforcement for infrastructure

## Security in Source Control

### Repository Security
- Branch protection policies
- Required reviewers
- Status checks enforcement
- Code signing verification
- Prevent force pushing
- Protecting sensitive branches

### Secret Management in Source Code
- Identifying secrets in code
- Git hooks for secrets detection
- Avoiding hardcoded credentials
- Secure alternatives for configuration
- Pre-commit scanning
- Secrets scanning in PRs

### Code Review Security Best Practices
- Security-focused code reviews
- Security checklist for reviewers
- Automated security comments
- Knowledge sharing during reviews
- Security-specific approval roles
- Review documentation

### Secure Branching Strategies
- Trunk-based development
- Feature branches with security validation
- Environment-specific branches
- Release branch protection
- Hotfix process for security issues
- Automating policy enforcement

## Secure CI/CD Pipeline Implementation

### Pipeline Security Architecture
- Principle of least privilege
- Segregation of duties
- Defense in depth
- Non-repudiation
- Chain of custody
- Audit trail

### Securing Build Agents
- Agent isolation
- Ephemeral build agents
- Just-in-time provisioning
- Agent hardening
- Agent pool security
- Self-hosted agent security

### Credential and Secret Management
- Azure Key Vault integration
- Pipeline variables security
- Service connection management
- Just-in-time access to secrets
- Secret rotation
- Audit and monitoring

### Artifact Security
- Artifact signing
- Artifact verification
- Artifact integrity checking
- Secure storage
- Access control
- Provenance tracking

### Pipeline Security Controls
- Separation of pipeline stages
- Approval workflows
- Environment security
- Privileged pipeline execution
- Secure configuration
- Pipeline scanning

## Secure Release and Deployment

### Secure Deployment Practices
- Blue-green deployments
- Canary releases
- Feature flags for security
- Rollback capabilities
- Progressive exposure
- Automated security validation

### Environment Security
- Development/Test/Production isolation
- Data classification by environment
- Environment-specific security controls
- Secure access to environments
- Automated environment configuration
- Configuration drift detection

### Post-Deployment Security Validation
- Automated security scanning
- Smoke testing security aspects
- Compliance validation
- Security acceptance testing
- Penetration testing integration
- Security signoff process

### Release Gates for Security
- Security scan gates
- Vulnerability assessment gates
- Compliance check gates
- Security approval gates
- Security metric thresholds
- Security incident gates

## Identity and Access Management

### Azure AD for DevOps
- Azure AD authentication
- Azure DevOps integration with Azure AD
- Single sign-on (SSO)
- Multi-factor authentication (MFA)
- Conditional access policies
- Groups and role assignments

### Managing DevOps Access Control
- Organization-level security
- Project-level security
- Repository security
- Build and release pipeline security
- Artifact feed security
- Test security

### Service Principal Security
- Creating and managing service principals
- Just-enough access (JEA)
- Credential rotation
- Monitoring service principal activity
- Automated service principal lifecycle
- Service connection security

### Privileged Access Management
- Just-in-time access
- Temporary elevated permissions
- Break-glass accounts
- Access request workflows
- Access reviews
- Privileged session monitoring

## Compliance as Code

### Regulatory Compliance Integration
- **Common Frameworks**:
  - SOC 1/2
  - PCI DSS
  - HIPAA
  - GDPR
  - ISO 27001
  - FedRAMP
- **Compliance as Code Implementation**:
  - Automated compliance checks
  - Compliance documentation generation
  - Evidence collection
  - Audit trail automation
  - Compliance dashboards
  - Continuous compliance monitoring

### Azure Policy Integration
- Policy definition and assignment
- Initiative definitions
- Compliance reporting
- Remediation tasks
- Policy exemptions
- Integration with pipeline

### Security Center and Defender for Cloud
- Security controls assessment
- Compliance standards dashboard
- Continuous security assessment
- Resource security hygiene
- Recommendations integration
- Alert management

### Azure Pipelines Security and Compliance
- Secure development validation
- Compliance scanning
- Security status reporting
- Audit history
- Security gate enforcement
- Evidence generation

## Security Monitoring and Response

### Application Security Monitoring
- Azure Application Insights
- Security-specific telemetry
- Anomaly detection
- Alert configuration
- Security dashboards
- Integration with security tools

### Container Security Monitoring
- Azure Container Monitoring
- Azure Defender for Containers
- Kubernetes security monitoring
- Image runtime scanning
- Container behavior monitoring
- Workload protection

### Azure Security Monitoring
- Log Analytics for security
- Azure Sentinel integration
- Security alerts processing
- SIEM integration
- Azure Monitor alerting
- Metric-based security monitoring

### Security Incident Response
- Incident response workflow
- Integration with ITSM tools
- Automated response actions
- Playbook automation
- Post-incident reviews
- Continuous improvement

## Cloud Security Posture Management

### Azure Security Posture
- Azure Defender for Cloud
- Secure score
- Asset inventory
- Security recommendations
- Regulatory compliance
- Security baseline

### Multi-cloud Security
- Cross-cloud security controls
- Unified security monitoring
- Common policy enforcement
- Identity integration
- Compliance reporting
- Risk prioritization

### Security Benchmark Implementation
- CIS benchmarks
- Microsoft security baselines
- Industry benchmarks
- Compliance frameworks
- Customizing benchmarks
- Automated validation

### Security Score Improvement
- Prioritizing security recommendations
- Risk-based remediation
- Tracking security posture over time
- Security debt management
- Security ROI analysis
- Continuous security improvement

## Best Practices

### DevSecOps Implementation Strategy
- Building a security champions program
- Integrating security into agile processes
- Tool selection and standardization
- Security metrics and KPIs
- Training and awareness
- Continuous improvement

### Secure Development Training
- Developer security training
- Security awareness for operations
- Secure coding guidelines
- Hands-on security exercises
- Security certification
- Ongoing security education

### Security Automation Strategy
- Identifying automation opportunities
- Tool selection and integration
- Balancing automation with manual review
- Measuring automation effectiveness
- Maintenance and updates
- Continuous expansion

### Security Governance
- Security policies and standards
- Roles and responsibilities
- Decision-making structures
- Risk management process
- Security review cadence
- Exception management

## Hands-On Exercises

1. Implement branch protection policies and PR security validation
2. Set up SAST scanning in an Azure Pipeline
3. Integrate dependency scanning for open-source vulnerabilities
4. Configure Azure Key Vault for pipeline secrets management
5. Implement security gates in a release pipeline
6. Set up Azure Policy for IaC security validation
7. Configure security monitoring for Azure resources
8. Create a compliance dashboard for security status reporting