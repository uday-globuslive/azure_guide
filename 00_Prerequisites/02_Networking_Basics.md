# Networking Basics

This guide provides essential networking concepts that form the foundation for understanding Azure networking services and cloud connectivity.

## TCP/IP Fundamentals

### The TCP/IP Model
The TCP/IP (Transmission Control Protocol/Internet Protocol) model is a conceptual framework used for network communications:

1. **Link Layer**: Physical and data link functionality (Ethernet, Wi-Fi)
2. **Internet Layer**: Addressing and routing (IP)
3. **Transport Layer**: End-to-end communication (TCP, UDP)
4. **Application Layer**: Application-specific protocols (HTTP, DNS, SMTP)

### IP Addressing

#### IPv4 Addressing
- **Format**: 32-bit address represented as four octets (e.g., 192.168.1.1)
- **Address Classes**:
  - Class A: 1-126 (Large networks, 16.7 million hosts)
  - Class B: 128-191 (Medium networks, 65,000 hosts)
  - Class C: 192-223 (Small networks, 254 hosts)
  - Class D: 224-239 (Multicast)
  - Class E: 240-255 (Experimental)
- **Special Addresses**:
  - 127.0.0.1: Loopback (localhost)
  - 169.254.x.x: Link-local (APIPA)
  - Private ranges:
    - 10.0.0.0/8 (Class A)
    - 172.16.0.0/12 (Class B)
    - 192.168.0.0/16 (Class C)

#### IPv6 Addressing
- **Format**: 128-bit address represented in eight groups of hexadecimal digits (e.g., 2001:0db8:85a3:0000:0000:8a2e:0370:7334)
- **Shortened Notation**: Eliminate leading zeros and replace consecutive zero groups with ::
- **Types**:
  - Global Unicast: Public, globally routable
  - Link-Local: fe80::/10
  - Unique Local: fc00::/7 (similar to private IPv4)
  - Multicast: ff00::/8

### Subnetting
Subnetting divides a network into smaller, more manageable segments.

#### CIDR Notation
Classless Inter-Domain Routing (CIDR) notation specifies IP addresses and their associated routing prefix:
- Format: IP address/prefix length (e.g., 192.168.1.0/24)
- Prefix length indicates how many bits of the address identify the network portion

#### Subnet Mask
- Identifies which portion of an IP address refers to the network and which refers to hosts
- Examples:
  - /24 = 255.255.255.0 (254 hosts)
  - /16 = 255.255.0.0 (65,534 hosts)
  - /8 = 255.0.0.0 (16,777,214 hosts)

#### Subnet Calculation
- **Number of Subnets**: 2^n (where n is the number of borrowed bits)
- **Number of Hosts per Subnet**: 2^m - 2 (where m is the number of host bits)
- **First Usable Host**: Network address + 1
- **Last Usable Host**: Broadcast address - 1
- **Broadcast Address**: Last address in the subnet range

### Routing
Routing is the process of selecting paths in a network along which to send data.

#### Routing Basics
- **Routing Table**: Database of network destinations and how to reach them
- **Default Gateway**: Router used when no specific route is known
- **Metrics**: Values used to determine the best path when multiple routes exist

#### Routing Protocols
- **Static Routing**: Manually configured routes
- **Dynamic Routing**: Automatic route discovery and updates
  - **Interior Gateway Protocols (IGPs)**:
    - RIP: Routing Information Protocol
    - OSPF: Open Shortest Path First
    - EIGRP: Enhanced Interior Gateway Routing Protocol
  - **Exterior Gateway Protocols (EGPs)**:
    - BGP: Border Gateway Protocol (used on the internet)

## DNS (Domain Name System)

### DNS Fundamentals
DNS translates human-readable domain names into IP addresses computers can understand.

#### DNS Hierarchy
- **Root Servers**: Manage the root zone (represented by a dot)
- **Top-Level Domains (TLDs)**: .com, .org, .net, country codes (.uk, .de)
- **Second-Level Domains**: example.com, microsoft.com
- **Subdomains**: www.example.com, mail.example.com

#### DNS Records
- **A Record**: Maps a domain to an IPv4 address
- **AAAA Record**: Maps a domain to an IPv6 address
- **CNAME Record**: Creates an alias pointing to another domain
- **MX Record**: Specifies mail servers for the domain
- **TXT Record**: Stores text information (often for verification)
- **NS Record**: Indicates which servers are authoritative for the domain
- **SOA Record**: Contains administrative information about the zone
- **PTR Record**: Reverse lookup (IP to domain)

#### DNS Resolution Process
1. Client queries local recursive resolver
2. If not cached, resolver queries root servers
3. Root servers direct to appropriate TLD servers
4. TLD servers direct to authoritative name servers
5. Authoritative servers provide the final answer
6. Resolver caches the answer and returns it to the client

#### DNS Zones
- **Forward Lookup Zones**: Domain name to IP resolution
- **Reverse Lookup Zones**: IP to domain name resolution
- **Primary Zone**: Main copy of the zone
- **Secondary Zone**: Read-only copy for redundancy
- **Stub Zone**: Contains only NS, SOA, and A records for authoritative servers

## Firewalls and Network Security

### Firewall Fundamentals
A firewall is a network security device that monitors and filters incoming and outgoing network traffic.

#### Types of Firewalls
- **Packet Filtering Firewall**: Basic filtering based on packet headers
- **Stateful Inspection Firewall**: Tracks the state of active connections
- **Application Layer Firewall**: Analyzes application-level protocols
- **Next-Generation Firewall (NGFW)**: Combines traditional firewall with advanced features
- **Web Application Firewall (WAF)**: Specifically protects web applications

#### Firewall Rules
- **Components**:
  - Source/destination IP address
  - Source/destination port
  - Protocol (TCP, UDP, ICMP)
  - Action (allow, deny, drop)
- **Rule Evaluation**: Often follows "first match" principle
- **Default Policies**: What happens when no rule matches (allow or deny)

#### Network Address Translation (NAT)
- **Purpose**: Conserve IPv4 addresses and provide security by hiding internal addresses
- **Types**:
  - **Source NAT (SNAT)**: Changes source address (outbound connections)
  - **Destination NAT (DNAT)**: Changes destination address (inbound connections)
  - **Port Address Translation (PAT)**: Many internal hosts share one external IP

### Network Security Concepts

#### Defense in Depth
- Multiple layers of security controls throughout the network
- Provides redundancy if one layer fails

#### Segmentation
- Dividing a network into isolated segments
- Limits lateral movement in case of a breach
- Implemented with:
  - VLANs (Virtual Local Area Networks)
  - Subnets
  - Firewalls between segments

#### Access Control Lists (ACLs)
- Rules that filter traffic based on specific criteria
- Applied to network devices (routers, switches, firewalls)
- Can be stateless or stateful

#### Intrusion Detection/Prevention
- **Intrusion Detection System (IDS)**: Monitors and alerts on suspicious activity
- **Intrusion Prevention System (IPS)**: Actively blocks detected threats
- **Detection Methods**:
  - Signature-based: Known patterns
  - Anomaly-based: Deviations from normal behavior
  - Heuristic: Rule-based analysis

## Load Balancing

### Load Balancing Fundamentals
Load balancing distributes network traffic across multiple servers to ensure no single server becomes overwhelmed.

#### Benefits of Load Balancing
- **High Availability**: Continues functioning if servers fail
- **Scalability**: Easily add or remove servers as needed
- **Efficiency**: Optimal resource utilization
- **Flexibility**: Different balancing methods for different needs

#### Load Balancing Methods
- **Round Robin**: Distributes requests sequentially
- **Least Connections**: Sends to server with fewest active connections
- **Weighted**: Distributes based on server capacity
- **IP Hash**: Routes based on client IP address (session persistence)
- **URL Hash**: Routes based on requested URL

#### Load Balancer Types
- **Layer 4 (Transport)**: Distributes based on IP address and port
- **Layer 7 (Application)**: Makes decisions based on application-level data
- **Hardware Load Balancers**: Physical appliances
- **Software Load Balancers**: Virtual appliances or services
- **Global Load Balancers**: Distribute traffic across multiple regions

#### Health Checks
- Periodic tests to verify server health
- Unhealthy servers removed from the pool
- Checks can be:
  - TCP connection tests
  - HTTP/HTTPS requests
  - Custom application checks

## VPN (Virtual Private Network)

### VPN Fundamentals
A VPN extends a private network across a public network, enabling users to send and receive data securely.

#### VPN Types
- **Site-to-Site VPN**: Connects entire networks together
  - Common for connecting branch offices to headquarters
  - Typically implemented on dedicated VPN devices/routers
- **Remote Access VPN**: Connects individual users to a network
  - Enables remote workers to access company resources
  - Client software on user devices

#### VPN Protocols
- **IPsec (Internet Protocol Security)**:
  - Framework of protocols for secure IP communications
  - Can operate in tunnel mode (entire packet) or transport mode (payload only)
- **SSL/TLS VPN**:
  - Uses web browsers as VPN clients
  - Often requires fewer firewall changes
- **PPTP (Point-to-Point Tunneling Protocol)**:
  - Older protocol, less secure
  - Simple to set up
- **L2TP/IPsec (Layer 2 Tunneling Protocol)**:
  - L2TP provides tunneling, IPsec provides encryption
  - More secure than PPTP
- **OpenVPN**:
  - Open-source VPN solution
  - Flexible and secure

#### VPN Components
- **Tunneling**: Encapsulating one protocol within another
- **Encryption**: Protecting data from unauthorized viewing
- **Authentication**: Verifying identity of connecting parties
- **Key Exchange**: Securely establishing encryption keys

### VPN Security Considerations
- **Split Tunneling**: Only specific traffic goes through VPN
- **Multi-Factor Authentication**: Additional security layer
- **Connection Policies**: Rules about when/how VPNs connect
- **Logging and Monitoring**: Tracking VPN usage

## Networking Terminology and Concepts

### Basic Network Components
- **Router**: Connects different networks, forwards packets based on IP address
- **Switch**: Connects devices within a network, forwards based on MAC address
- **Hub**: Simple device that forwards all data to all connected devices
- **Bridge**: Connects two network segments, learns which devices are on each side
- **Repeater**: Amplifies signal to extend network distance
- **Gateway**: Connection point between different networks or protocols

### Network Topologies
- **Bus**: All devices connected to a single cable
- **Star**: All devices connected to a central hub/switch
- **Ring**: Devices connected in a circular pattern
- **Mesh**: Devices interconnected with redundant connections
- **Hybrid**: Combination of different topologies

### Bandwidth and Throughput
- **Bandwidth**: Maximum theoretical data transfer rate
- **Throughput**: Actual amount of data successfully transferred
- **Latency**: Time delay before data transfer begins
- **Jitter**: Variation in packet delay
- **Packet Loss**: Percentage of packets not delivered

### Common Network Protocols
- **HTTP/HTTPS**: Web browsing
- **SMTP/POP3/IMAP**: Email
- **FTP/SFTP**: File transfer
- **SSH**: Secure terminal
- **RDP**: Remote desktop
- **SNMP**: Network management
- **NTP**: Time synchronization

This overview provides the essential networking concepts that will help you build your knowledge of Azure networking services and cloud connectivity. The subsequent sections will build upon these fundamentals to explain security, cloud concepts, and specific Azure implementations.