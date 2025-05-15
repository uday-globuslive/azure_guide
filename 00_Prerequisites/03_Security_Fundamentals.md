# Security Fundamentals

This guide provides an overview of essential security concepts that form the foundation for understanding Azure security services and best practices.

## Authentication vs. Authorization

### Authentication
Authentication is the process of verifying the identity of a user, system, or entity.

#### Authentication Factors
- **Something You Know**: Passwords, PINs, security questions
- **Something You Have**: Smart cards, security tokens, mobile devices
- **Something You Are**: Biometrics (fingerprints, facial recognition, voice)
- **Somewhere You Are**: Location-based factors
- **Something You Do**: Behavioral biometrics (typing patterns, gestures)

#### Authentication Methods
- **Password-based**: Traditional username and password
- **Multi-Factor Authentication (MFA)**: Combines two or more authentication factors
- **Certificate-based**: Uses digital certificates
- **Biometric**: Uses physical characteristics
- **Token-based**: Hardware or software tokens generating one-time codes
- **Single Sign-On (SSO)**: Authenticate once to access multiple applications

#### Common Authentication Protocols
- **LDAP (Lightweight Directory Access Protocol)**: Directory services access
- **Kerberos**: Authentication protocol using tickets
- **SAML (Security Assertion Markup Language)**: XML-based authentication between services
- **OAuth**: Authorization framework for third-party access
- **OpenID Connect**: Authentication layer on top of OAuth
- **FIDO (Fast Identity Online)**: Passwordless authentication standards

### Authorization
Authorization is the process of determining what a verified identity is allowed to do.

#### Authorization Models
- **Role-Based Access Control (RBAC)**: Access based on assigned roles
- **Attribute-Based Access Control (ABAC)**: Access based on user/resource attributes
- **Mandatory Access Control (MAC)**: Access based on security labels
- **Discretionary Access Control (DAC)**: Access controlled by resource owner
- **Rule-Based Access Control**: Access based on specific rules

#### Authorization Concepts
- **Principle of Least Privilege**: Grant minimum access necessary
- **Separation of Duties**: Critical tasks divided among multiple people
- **Need to Know**: Access only to information required for job
- **Privilege Escalation**: Gaining higher access privileges
- **Just-In-Time Access**: Temporary access granted when needed

#### Permission Management
- **Access Control Lists (ACLs)**: Lists specifying access rights
- **Permissions**: Specific actions that can be performed
- **Delegation**: Transferring access rights to another entity
- **Revocation**: Removing access rights
- **Auditing**: Recording access attempts and changes

## Encryption

### Encryption Fundamentals
Encryption is the process of converting information into a code to prevent unauthorized access.

#### Types of Encryption
- **Symmetric Encryption**: Single key for encryption and decryption
  - Faster, but key distribution is a challenge
  - Examples: AES, DES, 3DES
- **Asymmetric Encryption**: Public/private key pairs
  - Slower, but better for secure communications
  - Examples: RSA, ECC, DSA
- **Hybrid Encryption**: Combines both, using asymmetric to exchange symmetric keys
  - Example: TLS/SSL protocol

#### Common Encryption Algorithms
- **AES (Advanced Encryption Standard)**: Symmetric block cipher
  - Key sizes: 128, 192, 256 bits
  - Current standard for sensitive information
- **RSA (Rivest-Shamir-Adleman)**: Asymmetric algorithm
  - Uses factoring large numbers
  - Common key sizes: 2048, 4096 bits
- **ECC (Elliptic Curve Cryptography)**: Asymmetric algorithm
  - Shorter key lengths, good for mobile/constrained environments
- **ChaCha20-Poly1305**: Modern symmetric cipher/MAC combination
  - Good performance on devices without hardware acceleration

#### Encryption Use Cases
- **Data at Rest**: Stored data (disk encryption, database encryption)
- **Data in Transit**: Data being transferred (HTTPS, VPN, SFTP)
- **Data in Use**: Data in memory (confidential computing, secure enclaves)
- **End-to-End Encryption**: Data encrypted from sender to receiver

#### Encryption Concepts
- **Key Management**: Generation, storage, rotation, and destruction of keys
- **Digital Signatures**: Verify authenticity and integrity of messages
- **Hash Functions**: One-way functions that create fixed-length outputs
  - Examples: SHA-256, SHA-3, BLAKE2
- **Salt**: Random data added to input before hashing (prevents rainbow table attacks)
- **Initialization Vector (IV)**: Random value used to ensure encrypted repetitive data looks different

## Certificates

### Digital Certificate Basics
Digital certificates are electronic credentials that establish the identity of entities on the internet.

#### Certificate Components
- **Subject**: Entity the certificate is issued to
- **Issuer**: Certificate Authority (CA) that issued the certificate
- **Public Key**: The subject's public key
- **Validity Period**: Start and end dates
- **Serial Number**: Unique identifier
- **Digital Signature**: Signature of the issuer
- **X.509**: Standard defining the format of public key certificates

#### Certificate Authorities (CAs)
- **Root CAs**: Top-level authorities that are implicitly trusted
- **Intermediate CAs**: Issue certificates on behalf of root CAs
- **Public CAs**: Issue certificates to the public (DigiCert, Let's Encrypt)
- **Private CAs**: Issue certificates within an organization
- **Trust Chain**: Path from end-entity certificate to trusted root CA

#### Certificate Types
- **SSL/TLS Certificates**: Secure websites (HTTPS)
  - Domain Validation (DV): Validates domain ownership
  - Organization Validation (OV): Validates organization
  - Extended Validation (EV): Most rigorous validation
- **Client Certificates**: Authenticate user identity
- **Code Signing Certificates**: Verify software integrity
- **Email Certificates (S/MIME)**: Secure email

#### Certificate Lifecycle
- **Certificate Request/Generation**: Creating Certificate Signing Request (CSR)
- **Validation**: Verifying identity and ownership
- **Issuance**: CA signs and issues the certificate
- **Installation**: Deploying on servers, devices, or applications
- **Renewal**: Extending validity before expiration
- **Revocation**: Invalidating certificates before expiration
  - Certificate Revocation List (CRL)
  - Online Certificate Status Protocol (OCSP)

### Public Key Infrastructure (PKI)
PKI is the framework of hardware, software, policies, and procedures needed to create, manage, distribute, use, store, and revoke digital certificates.

#### PKI Components
- **Certificate Authority (CA)**: Issues certificates
- **Registration Authority (RA)**: Verifies requesting entity's identity
- **Certificate Database**: Stores certificate information
- **Certificate Store**: Location where certificates are stored on devices
- **Certificate Policy**: Rules governing certificate issuance and usage

#### PKI Operations
- **Certificate Issuance**: Process of creating and signing certificates
- **Certificate Distribution**: Delivering certificates to users/systems
- **Key Backup and Recovery**: Procedures for recovering lost keys
- **Certificate Validation**: Verifying certificate authenticity and status
- **Certificate Revocation**: Process of invalidating certificates

## Identity Management

### Identity Concepts
Identity management involves controlling information that authenticates and authorizes users.

#### Identity Components
- **Digital Identity**: Collection of attributes about an entity
- **Identifier**: Unique value that represents an identity (username, email)
- **Credentials**: Information used to authenticate (password, certificate)
- **Attributes**: Characteristics that describe an identity (name, department)
- **Profile**: Collection of attributes for a specific context
- **Entitlements**: Rights and privileges assigned to an identity

#### Identity Lifecycle
- **Provisioning**: Creating and setting up user accounts
- **Self-Service**: Users manage aspects of their own identity
- **Federation**: Linking identities across multiple systems
- **Deprovisioning**: Removing accounts when no longer needed

#### Identity Repositories
- **Directory Services**: Centralized identity storage and management
  - Microsoft Active Directory
  - LDAP directories
  - Azure Active Directory (Azure AD)
- **Identity Databases**: Custom databases storing identity information
- **Identity Metasystems**: Systems that manage identities across repositories

### Access Management

#### Single Sign-On (SSO)
- **Benefits**: Reduced password fatigue, improved user experience
- **Implementation Methods**:
  - Kerberos tickets
  - SAML assertions
  - OAuth tokens/OpenID Connect
  - Integrated Windows Authentication

#### Identity Federation
- **Definition**: Trust relationship between identity providers
- **Protocols**: SAML, WS-Federation, OAuth/OpenID Connect
- **Use Cases**: Cross-organization collaboration, cloud service access
- **Benefits**: Reduces redundant identities, simplifies management

#### Privileged Access Management (PAM)
- **Definition**: Managing and auditing privileged accounts
- **Just-In-Time Access**: Temporary elevation of privileges
- **Session Monitoring**: Recording administrative sessions
- **Credential Vaulting**: Secure storage of privileged credentials
- **Privileged Session Management**: Controlling and monitoring privileged sessions

#### Identity Governance
- **Access Reviews**: Periodic validation of access rights
- **Access Certification**: Formal verification of appropriate access
- **Segregation of Duties**: Preventing conflicts of interest
- **Role Management**: Defining and maintaining access roles
- **Compliance Reporting**: Documenting identity compliance

## Cybersecurity Concepts

### Defense in Depth
Defense in depth is a layered approach to security, using multiple mechanisms to protect systems and data.

#### Security Layers
- **Physical Security**: Controlling physical access to resources
- **Identity and Access**: Authentication and authorization
- **Perimeter Security**: Firewalls, proxies, gateways
- **Network Security**: Network segmentation, intrusion detection
- **Compute Security**: Endpoint protection, vulnerability management
- **Application Security**: Secure coding, application firewalls
- **Data Security**: Encryption, data loss prevention

### Threat Vectors
Common paths that attackers use to compromise systems.

#### Common Threat Vectors
- **Email**: Phishing, malware attachments
- **Web**: Drive-by downloads, malicious websites
- **Removable Media**: Infected USB drives
- **Network**: Scanning, man-in-the-middle attacks
- **Social Engineering**: Manipulating users to divulge information
- **Supply Chain**: Compromising software or hardware during development
- **Insider Threats**: Malicious or negligent employees

### Common Attack Types
- **Malware**: Software designed to harm or compromise systems
  - Viruses, worms, trojans, ransomware, spyware
- **Phishing**: Deceptive attempts to steal sensitive information
- **Man-in-the-Middle**: Intercepting communications
- **Denial of Service**: Overwhelming systems to cause disruption
- **SQL Injection**: Attacking databases through malformed queries
- **Cross-Site Scripting (XSS)**: Injecting malicious code into web pages
- **Password Attacks**: Brute force, dictionary attacks, credential stuffing

### Security Controls
Measures implemented to mitigate security risks.

#### Types of Controls
- **Preventive**: Stop incidents before they occur
- **Detective**: Identify when incidents occur
- **Corrective**: Fix issues after they occur
- **Deterrent**: Discourage security violations
- **Recovery**: Restore operations after incidents

#### Technical Controls
- **Authentication and Authorization**: Verifying identities, managing access
- **Firewalls**: Filter network traffic
- **Intrusion Detection Systems (IDS)**: Detect suspicious activity
- **Intrusion Prevention Systems (IPS)**: Actively prevent attacks
- **Endpoint Protection**: Secure end-user devices
- **Data Loss Prevention (DLP)**: Prevent unauthorized data disclosure

#### Administrative Controls
- **Policies and Procedures**: Documented security rules
- **Security Awareness Training**: Educating users
- **Personnel Security**: Background checks, job rotation
- **Risk Management**: Assessing and mitigating risks
- **Compliance Management**: Ensuring adherence to requirements

#### Physical Controls
- **Access Controls**: Locks, badges, biometrics
- **Environmental Controls**: Temperature, humidity controls
- **Physical Monitoring**: CCTV, security guards
- **Secure Disposal**: Proper destruction of media

This overview provides the essential security concepts that will help you build your knowledge of Azure security services and best practices. The subsequent sections will build upon these fundamentals to explain cloud-specific security concepts and Azure security implementations.