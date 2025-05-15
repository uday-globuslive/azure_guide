# Azure Pricing and Service Level Agreements (AZ-900)

## Azure Subscription Options

### Free Account
- $200 credit for the first 30 days
- 12 months of free services
- Always free services (25+ services)
- Good for learning and experimentation

### Pay-As-You-Go
- No upfront costs or commitments
- Pay only for what you use
- Hourly billing for most services
- Suitable for variable workloads

### Enterprise Agreement (EA)
- For larger organizations
- Negotiated pricing with Microsoft
- Commitment to annual spend
- Increased flexibility and cost management features

### Cloud Solution Provider (CSP)
- Purchase through Microsoft partners
- Partners provide direct billing and support
- Often bundled with additional services

## Factors Affecting Costs

### Resource Type
- Different services have different pricing models
- Premium services cost more than standard offerings
- Specialized hardware increases costs

### Service Consumption
- Pay for what you use
- More usage = more cost
- Idle resources still incur costs unless specifically deallocated

### Maintenance
- Always-on vs. development/test environments
- Scheduled auto-shutdown for dev/test VMs
- Clean up unused resources

### Geography
- Prices vary by region
- Data transfer costs between regions
- Consider proximity to users for performance and costs

### Network Traffic
- Inbound data transfer is typically free
- Outbound data transfer has costs that vary by region
- Inter-region transfers incur costs

### Instance Types
- Size of VMs affects pricing (CPU, RAM, storage)
- Reserved Instances vs. Spot Instances vs. Pay-as-you-go
- Specialized instance types for GPU, high memory, etc.

## Cost Management Tools

### Azure Pricing Calculator
- Estimate costs before deployment
- Compare different service options
- Build complex solution estimates

### Azure Cost Management + Billing
- Set budgets and alerts
- View cost trends
- Analyze and optimize spending
- Cost allocation and chargeback

### Azure Advisor Cost Recommendations
- Identifies idle and underutilized resources
- Suggests right-sizing for VMs
- Recommends reserved instances when beneficial

### Azure Resource Tags
- Track costs by department, project, or environment
- Essential for cost allocation
- Enables detailed cost analysis

## Service Level Agreements (SLAs)

### What is an SLA?
- Formal agreement between service provider and customer
- Defines the expected service level
- Specifies remedies if SLAs are not met
- Not the same as a guarantee

### SLA Components
- Performance targets (uptime, throughput, latency)
- How performance is measured
- Service credits for missed SLAs
- Exclusions and limitations

### Common Azure SLAs
- Virtual Machines: 99.9% (single VM), 99.95% (availability set), 99.99% (availability zone)
- Azure App Service: 99.95%
- Azure SQL Database: 99.99% (premium tier)
- Azure Storage: 99.9% to 99.99% (depending on redundancy)

### Composite SLAs
- When multiple services are used together, multiply the SLAs
- Example: 99.95% × 99.9% = 99.85% 
- Complex architectures require careful SLA calculations

### Improving Application SLAs
- Use availability sets for VMs
- Implement zone-redundant services
- Use geo-redundant storage
- Implement retry logic in applications
- Design for disaster recovery

## Azure Service Lifecycle

### Private Preview
- Limited customer access
- Early testing and feedback
- No SLAs or pricing guarantees
- Not for production use

### Public Preview
- Available to all customers
- Testing at scale before GA
- Limited or no SLAs
- Discounted pricing

### General Availability (GA)
- Full production-ready service
- Complete documentation
- Full support and SLAs
- Standard pricing

## Total Cost of Ownership (TCO)

### TCO Calculator
- Compare on-premises vs. Azure costs
- Include hardware, software, operations, and facilities
- Generate detailed reports for stakeholders

### Cost Optimization Strategies
- Right-sizing resources
- Reserved Instances for predictable workloads
- Autoscaling for variable workloads
- Spot VMs for interruptible workloads
- Dev/Test subscription pricing
- Azure Hybrid Benefit

## Hands-On Exercises

1. Use the Azure Pricing Calculator to estimate costs for a solution
2. Implement resource tags and analyze costs by tag
3. Set up budgets and alerts in Azure Cost Management
4. Calculate composite SLAs for a multi-tier application
5. Use the TCO Calculator to compare on-premises vs. Azure costs