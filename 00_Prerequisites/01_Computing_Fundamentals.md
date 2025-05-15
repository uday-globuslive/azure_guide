# Computing Fundamentals

This guide explains the essential computing concepts that serve as building blocks for understanding Azure services and cloud computing in general.

## Operating Systems Basics

### What is an Operating System?
An operating system (OS) is system software that manages computer hardware, software resources, and provides common services for computer programs. It acts as an intermediary between applications and hardware.

### Windows Operating System
Windows is a family of operating systems developed by Microsoft. Here are key concepts:
- **Windows Server**: Enterprise version optimized for server workloads and services
- **Services**: Background processes that run regardless of user login status
- **Active Directory**: Directory service for identity management and authentication
- **Registry**: Hierarchical database that stores configuration settings
- **PowerShell**: Command-line shell and scripting language for automation

### Linux Operating System
Linux is an open-source Unix-like operating system. Key concepts include:
- **Distributions**: Different versions of Linux (Ubuntu, Red Hat, CentOS, etc.)
- **Shell**: Command-line interface (Bash, Zsh, etc.) for interacting with the system
- **Package Management**: Tools like apt, yum, or dnf for installing software
- **File System Hierarchy**: Directory structure (/etc, /var, /home, etc.)
- **Permissions**: File access controls through read/write/execute permissions

### Basic Administrative Tasks
- **User Management**: Creating, modifying, and deleting user accounts
- **Software Installation/Updates**: Managing applications and system updates
- **Service Management**: Starting, stopping, and configuring services
- **File System Operations**: Managing files and directories
- **System Monitoring**: Checking resource usage and system health

## Virtualization Concepts

### What is Virtualization?
Virtualization is the process of creating a software-based, or virtual, representation of something, such as virtual applications, servers, storage, and networks.

### Types of Virtualization
- **Server Virtualization**: Running multiple virtual servers on a single physical server
- **Desktop Virtualization**: Hosting desktop environments on a central server
- **Application Virtualization**: Running applications in an isolated environment
- **Network Virtualization**: Creating virtual networks independent of physical hardware
- **Storage Virtualization**: Pooling physical storage from multiple devices into a single storage unit

### Hypervisors
A hypervisor is software that creates and runs virtual machines (VMs).
- **Type 1 (Bare Metal)**: Runs directly on hardware (Hyper-V, VMware ESXi, KVM)
- **Type 2 (Hosted)**: Runs on top of an operating system (VirtualBox, VMware Workstation)

### Virtual Machines (VMs)
- **Definition**: Software emulation of a physical computer
- **Components**: Virtual CPUs, memory, storage, and network interfaces
- **Benefits**: Resource isolation, flexibility, and better hardware utilization
- **Limitations**: Performance overhead, resource consumption

### Containers
- **Definition**: Lightweight, standalone packages that include everything needed to run an application
- **Compared to VMs**: Share the host OS kernel, start faster, use fewer resources
- **Container Engines**: Docker, containerd, CRI-O
- **Use Cases**: Microservices, CI/CD environments, application isolation

## Storage Concepts

### Storage Types

#### Block Storage
- **Definition**: Storage that divides data into blocks of equal size
- **Characteristics**: Data accessed at the block level, raw storage
- **Use Cases**: Databases, virtual machine disks, applications requiring low latency
- **Examples**: SAN, local disks, Azure Disk Storage

#### File Storage
- **Definition**: Hierarchical storage presenting data as files and folders
- **Characteristics**: Accessed through file system protocols (SMB, NFS)
- **Use Cases**: Document sharing, application data, home directories
- **Examples**: NAS, file shares, Azure Files

#### Object Storage
- **Definition**: Storage that manages data as objects with metadata
- **Characteristics**: Highly scalable, accessed via HTTP/HTTPS
- **Use Cases**: Large unstructured data, media files, backups
- **Examples**: Azure Blob Storage, Amazon S3

### RAID (Redundant Array of Independent Disks)
RAID combines multiple disk drives into a logical unit for data redundancy or performance improvement.

#### Common RAID Levels
- **RAID 0**: Striping (improved performance, no redundancy)
- **RAID 1**: Mirroring (full redundancy, limited capacity)
- **RAID 5**: Striping with parity (good balance of redundancy and performance)
- **RAID 10**: Combination of mirroring and striping (high performance and redundancy)

### Storage Performance Metrics
- **IOPS (Input/Output Operations Per Second)**: Number of read/write operations
- **Throughput**: Amount of data moved in a given time (MB/s)
- **Latency**: Time taken for an I/O operation to complete
- **Capacity**: Total usable storage space

### Data Protection Concepts
- **Backups**: Point-in-time copies of data stored separately
- **Snapshots**: Point-in-time copies that capture the state of a storage volume
- **Replication**: Duplicating data to another location for redundancy
- **Retention Policies**: Rules that determine how long data is kept

## Backup and Disaster Recovery

### Backup Concepts
- **Full Backup**: Complete copy of all selected data
- **Incremental Backup**: Backs up only the data changed since the last backup
- **Differential Backup**: Backs up data changed since the last full backup
- **Snapshot**: Point-in-time image of data
- **Backup Rotation**: Schedule for cycling backup media or files

### Backup Best Practices
- **3-2-1 Rule**: Keep 3 copies of data, on 2 different media types, with 1 copy offsite
- **Backup Testing**: Regular testing of restoration procedures
- **Encryption**: Protecting backup data at rest and in transit
- **Monitoring**: Verifying backup job success and failure

### Disaster Recovery Planning
- **RPO (Recovery Point Objective)**: Maximum acceptable data loss
- **RTO (Recovery Time Objective)**: Maximum acceptable time to restore service
- **Hot Site**: Fully operational alternate site
- **Warm Site**: Partially configured alternate site
- **Cold Site**: Basic facility requiring setup before use
- **DRaaS (Disaster Recovery as a Service)**: Cloud-based DR solutions

### Business Continuity Concepts
- **Business Impact Analysis**: Identifying critical functions and resources
- **Recovery Strategies**: Plans for different disaster scenarios
- **Crisis Communication**: Procedures for internal and external communication
- **Testing and Exercises**: Verifying DR plan effectiveness

## Monitoring and Management

### System Monitoring Basics
- **Performance Metrics**: CPU, memory, disk, network usage
- **Logs**: System logs, application logs, security logs
- **Alerts**: Notifications based on threshold violations
- **Dashboards**: Visual representation of system status

### Common Monitoring Tools
- **Operating System Tools**: Task Manager (Windows), top/htop (Linux)
- **Monitoring Platforms**: Nagios, Zabbix, Prometheus
- **Log Management**: ELK Stack (Elasticsearch, Logstash, Kibana), Splunk
- **Cloud Monitoring**: Azure Monitor, AWS CloudWatch, Google Cloud Monitoring

### IT Service Management (ITSM)
- **ITIL Framework**: Best practices for IT service delivery
- **Service Desk**: Single point of contact for IT support
- **Change Management**: Process for making controlled IT changes
- **Incident Management**: Restoring normal service operation quickly
- **Problem Management**: Identifying and addressing root causes

### Automation Concepts
- **Scripts**: Executable files with commands for automation
- **Configuration Management**: Tools like Ansible, Chef, Puppet
- **Infrastructure as Code**: Defining infrastructure in code files
- **Runbooks**: Collection of procedures for routine operations

This overview provides the foundational understanding of computing concepts that will help you build your knowledge of Azure services and cloud computing in general. The subsequent sections will build upon these fundamentals to explain networking, security, and cloud-specific concepts.