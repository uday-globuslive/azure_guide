# Virtual Networking (AZ-104)

## Azure Virtual Network Fundamentals

### VNet Concepts
- **Virtual Network (VNet)**: Isolated network in Azure
- **Subnet**: Segment of a VNet with its own address range
- **Address space**: IPv4 and IPv6 CIDR blocks assigned to a VNet
- **Public vs. private IP addresses**: Scope and assignment methods
- **Network interface (NIC)**: Connection point between VM and VNet
- **MAC addresses**: Hardware-level addressing for VMs

### VNet Design and Planning
- Address space planning and CIDR notation
- Subnet segmentation strategies
- Regional considerations
- Subscription and organizational boundaries
- Integration with on-premises networks
- DNS configuration and planning

### VNet Creation and Management
- Portal, CLI, PowerShell, and ARM template methods
- Modifying address spaces and subnets
- VNet diagnostics and troubleshooting
- Resource move operations
- Tagging and organization

## Network Security

### Network Security Groups (NSGs)
- Security rule components
  - Source/destination (IP addresses, service tags, application security groups)
  - Protocol (TCP, UDP, ICMP, Any)
  - Source/destination port
  - Direction (inbound, outbound)
  - Priority (100-4096)
  - Action (Allow, Deny)
- Association with subnets and NICs
- Default rules and their implications
- NSG flow logs and traffic analytics
- Service tags for simplified management

### Application Security Groups (ASGs)
- Purpose and use cases
- Grouping VMs by application role
- Simplified security rule management
- Combining ASGs with NSGs

### Azure Firewall
- Cloud-native firewall as a service
- Rule types
  - Network rules (Layer 3-4)
  - Application rules (Layer 7)
  - NAT rules
- Threat intelligence integration
- Forced tunneling
- Centralized management and logging
- Firewall Manager for multi-tenant environments

### Azure DDoS Protection
- Basic protection (free with every VNet)
- Standard protection (paid service)
- Real-time metrics and alerts
- Post-attack mitigation reports
- Cost protection for scaled-out resources

### Web Application Firewall (WAF)
- Integration with Application Gateway and Front Door
- OWASP rule sets
- Custom rules and exclusions
- Monitoring and logging

## Network Connectivity

### Virtual Network Peering
- VNet-to-VNet connectivity within and across regions
- Transitive connectivity limitations
- Gateway transit configuration
- Service chaining with NVAs
- Managing peerings at scale

### VPN Connections
- Site-to-Site VPN
  - VPN Gateway types and SKUs
  - BGP support
  - Active-active configuration
  - Custom IPsec/IKE policies
- Point-to-Site VPN
  - Protocol options (OpenVPN, SSTP, IKEv2)
  - Authentication methods
  - Client configuration
- VNet-to-VNet VPN

### ExpressRoute
- Private connectivity to Microsoft cloud services
- Connectivity models
  - CloudExchange colocation
  - Point-to-point Ethernet connection
  - Any-to-any (IPVPN) connection
- ExpressRoute circuit SKUs and limitations
- ExpressRoute Direct for higher bandwidth
- ExpressRoute Global Reach for site-to-site connectivity

### Azure Virtual WAN
- Global transit network architecture
- Hub and spoke topology
- Branch connectivity automation
- Routing optimization and configuration
- Integration with SD-WAN solutions

## Network Routing and Traffic Management

### User-Defined Routes (UDRs)
- Custom routing tables
- Next hop types
  - Virtual network gateway
  - Virtual network
  - Internet
  - Virtual appliance
  - None
- Route propagation control
- Association with subnets

### Network Virtual Appliances (NVAs)
- Third-party security appliances
- Routing considerations
- High-availability configurations
- Monitoring and scaling

### Border Gateway Protocol (BGP)
- Dynamic routing protocol
- ExpressRoute and VPN Gateway integration
- Route exchange and selection
- AS numbers and peering

### Azure Load Balancer
- Public vs. Internal load balancers
- SKUs (Basic vs. Standard)
- Frontend IP configurations
- Backend pools
- Health probes
- Load balancing rules
- Outbound rules
- HA ports
- Floating IP configuration

### Traffic Manager
- Global DNS-based traffic routing
- Routing methods
  - Priority
  - Weighted
  - Performance
  - Geographic
  - Subnet
  - MultiValue
- Endpoint monitoring
- Nested profiles

### Application Gateway
- Layer 7 load balancing
- SKUs (Standard, WAF, v2 variants)
- SSL termination and end-to-end encryption
- Cookie-based session affinity
- URL-based routing
- Multi-site hosting
- WebSocket and HTTP/2 support
- Autoscaling and zone redundancy

### Front Door
- Global entry point service
- Anycast with split TCP
- Application acceleration
- Global HTTP load balancing
- URL-based routing
- Session affinity
- End-to-end TLS
- WAF integration

## Private Access and Service Endpoints

### Private Link and Private Endpoints
- Private connectivity to PaaS services
- Private IP addressing for services
- DNS integration and configuration
- Network security considerations
- Monitoring and logging

### Service Endpoints
- Direct connection to Azure services
- Service-specific endpoints and routing
- Network rule-based access control
- Regional vs. global service endpoints
- Comparison with Private Link

### Azure Bastion
- Secure RDP/SSH access to VMs
- Browser-based connectivity
- No public IP requirement for VMs
- Integration with Azure AD
- Monitoring and logging

## Network Management and Monitoring

### Network Watcher
- Topology visualization
- Connection monitor
- NSG flow logs
- IP flow verify
- Next hop determination
- VPN troubleshooting
- Packet capture

### Azure Monitor for Networks
- Metrics and logs collection
- Network insights
- Connection monitor
- Traffic Analytics
- Alerts and notifications

### DNS Management
- Azure DNS for public domains
- Private DNS zones for internal resolution
- DNS record management
- DNS query logs
- Integration with other Azure services

## Advanced Networking Features

### Virtual Network NAT
- Outbound connectivity management
- Public IP address sharing
- Simplification of outbound connectivity
- Scaling and high availability

### IPv6 Support
- Dual-stack VNets
- Public IPv6 addresses
- Load balancing for IPv6
- VPN and ExpressRoute limitations

### Subnet Delegation
- Dedicating a subnet to a specific service
- Service-specific subnet configuration
- Automatic management by service provider
- Limitations and considerations

### Azure Virtual Network Manager
- Centralized network management
- Security admin configuration
- Connectivity topology configuration
- Hub and spoke management
- Cross-tenant management

### Network Service Mesh
- Service mesh concepts
- Traffic management
- Security and encryption
- Observability and monitoring

## Hybrid Cloud Networking

### Azure Arc Network Architecture
- Extending Azure networking to hybrid environments
- Kubernetes integration
- Monitoring and management
- Security considerations

### Multi-Cloud Networking
- Connectivity between cloud providers
- Consistent security policies
- Traffic optimization
- Management and monitoring

### Edge Computing Networking
- Azure Stack HCI networking
- Azure Stack Edge networking
- IoT connectivity options
- Edge zones and private MEC

## Network Design Patterns

### Hub and Spoke Topology
- Central connectivity hub
- Workload segregation in spokes
- Transitivity and routing considerations
- Scaling and performance planning

### Micro-Segmentation
- Zero-trust network design
- Granular security controls
- Monitoring and anomaly detection
- Implementation with NSGs and firewalls

### Perimeter Networks (DMZ)
- Internet-facing services protection
- Multiple security layers
- Inspection and filtering
- Compliance requirements

### Global Network Architecture
- Multi-region design
- Traffic management and optimization
- Disaster recovery considerations
- Cost optimization strategies

## Best Practices

### Design Recommendations
- Address space planning
- Subnet sizing and allocation
- Security implementation
- Performance optimization

### Operational Excellence
- Infrastructure as Code for networking
- Monitoring and alerting
- Disaster recovery planning
- Regular security reviews

### Cost Optimization
- Right-sizing resources
- Traffic optimization
- Hybrid connectivity cost management
- Reserved instances for networking services

## Hands-On Exercises

1. Design and implement a hub and spoke network topology
2. Configure site-to-site VPN connectivity with BGP
3. Implement Azure Firewall with threat intelligence-based filtering
4. Set up Private Link for secure access to PaaS services
5. Configure load balancing for a multi-tier application
6. Implement network monitoring with Network Watcher and Azure Monitor
7. Secure virtual network traffic with NSGs and ASGs