**Exam overview**
- Exam Duration: 170 mins
- Exam format: 65 questions, either multiple choice or multiple response
- Cost: 300 USD
- Exam domains
  - Domain 1: Network Design (30% of scored content)
    - Summary: edge network, DNS, load balancing, logging & monitoring, routing btw on pre-mises and AWS; multiple AWS accounts/regions/VPCs
    - content distribution networks: Amazon CloudFront
    - global traffic management: AWS Global Accelerator
    - other srvices: Elastic Load Balancing [ELB], Amazon API Gateway
    - DNS solution: DNS protocol, DNS logging and monitoring, Amazon Route 53 features that meet public, private, hybrid env, domain registration
    - Load balancing: how load balancing works in OSI layers 3/4/7,  types of load balancers, Connectivity patterns (internal, external), scaling factors. E.g Global Accelerator, CloudFront, AWS WAF, Route 53, Amazon Elastic Kubernetes Service [Amazon EKS], AWS Certificate Manager [ACM]), configuration options (proxy protocol, cross-zone load balancing, session affinity, routing algo, TCP, GENEVE, IP compared with instance), AWS Load Balancer Controller for Kubernetes clusters, encryption and authentication (TLS termination, TLS passthrough)
    - logging and monitoring requirements
    - routing strategy between on-premises and AWS
    - routing strategy that include multiple AWS accounts, AWS Regions, and VPCs
  - Domain 2: Network Implementation (26% of scored content)
    - Summary: routing btw on pre-mises and AWS; multiple AWS accounts/regions/VPCs, coomplex hybrid and multi-account DNS achitecture, automate and configure network infra
  - Domain 3: Network Management and Operation (20% of scored content)
    - Summary: maintain routing and connectivity, optimize connectivity
  - Domain 4: Network Security, Compliance, and Governance (24% of scored content)
    - Summary: implement security features, validate and audit security, implement and maintain confidentiality of data

**Study resources**
- [Exam Prep Official Practice Question Set: AWS Certified Advanced Networking - Specialty (ANS-C01 - English)](https://explore.skillbuilder.aws/learn/courses/12676/aws-certified-advanced-networking-specialty-official-practice-question-set-ans-c01-english)
- [AWS official refresh AWS knowledge and skills](https://skillbuilder.aws/exam-prep/advanced-networking-specialty)
  - AWS Networking Basics (2h)
   - Architecting on AWS 
   - AWS Security Best Practices
   - Cloud Operations on AWS
   - Monitoring and Troubleshooting （1h)
   - AWS Networking Practical Approaches (3h)
   - AWS Jam Journey: Networking
   - Amazon VPC Networking Basics
   - Comparing Amazon Virtual Private Cloud (VPC) Peering and AWS Transit Gateway
   - Performing a Basic Audit of your AWS Environment
- Github notes
  - https://github.com/Ernyoke/certified-aws-advanced-networking-specialty
  - https://github.com/davidawcloudsecurity/learn-aws-certified-advanced-networking?tab=readme-ov-file
- OnDemand course (Paid)
  - [Udemy Neal Davis ~ 10 hours](https://www.udemy.com/course/aws-advanced-networking-specialty-ans/?couponCode=ST14MT150425G1)
  - Pluralsight Andry Estes ~ 39 hours](https://app.pluralsight.com/paths/certificate/aws-certified-advanced-networking-specialty-ans-c01)
- Hands-on labs
  - https://github.com/pluralsight-cloud/aws-certified-advanced-networking-specialty 
 
**Introduction**
- learn: design, implement, operate and maintain hybrid and cloud-based network, tasks automation, secure AWS network
- hands-on: manage AWS VPC, implement hybrid connectivity with on-premises network, monitor & performance
- prerequisites network knowledge: IPV2 and IPv6 add, Basic IPv4 subnetting, IP routing
- prerequisites AWS knowledge: IAM, EC2, CloudWatch, CloudTrail, Config, S3 buckets, CloudFormation, VPC
- tools to use: Boto3 (AWS SDK for Python to interact with AWS services), AWS CLI (Control AWS services from terminal), [IP/CIDR calculator](https://cidr.xyz/), ipcalc (python script)
- shared responsibility model:
  - AWS of the cloud: compute, storage, db, networking, hardware, physical security, global infra)
  - Customer in the cloud: data, identity management, platform, app, OS, network & firewall config, encryption, network traffic protection

# Design and Implement AWS networks
**Intro**
- what features:
- whwat considerations:
- different parts of VPC
- troubleshoot and secure VPC

### AWS Global Infrastructure 
**Core Components**
- **Regions**: Physically isolated locations worldwide with multiple Availability Zones (AZs). E.g `us-east-1`
- **Availability Zones (AZs)**: One or more data centers with independent power, networking, and connectivity. Low-latency links connect AZs. E.g `us-east-1a`. **Availability Zone ID** uniquely identify each physical AZ (use for cross account architecture). E.g `use1-az1`
- **Edge Locations**: Used by Amazon CloudFront to cache content closer to users.
- **Regional Edge Caches**: Act as a mid-tier cache between edge locations and origin servers.
- **Local Zones**: Extend AWS services closer to users in metro areas for ultra-low latency.
- **Wavelength Zones**: Embed AWS services within 5G networks of telco providers.

**Networking Impact**
- **High Availability**:
  - Use multiple AZs for fault-tolerant design.
  - Services like VPC subnets are AZ-specific; services like IAM and S3 are global/regional.
- **Latency Optimization**:
  - Use CloudFront, Route 53, and Local Zones for low-latency delivery.
- **Resiliency**:
  - Inter-AZ traffic is high-throughput and low-latency.
  - Design for multi-AZ and multi-Region redundancy.

**Region Design Considerations**
- **Service Availability** varies by region.
- **Compliance & Data Residency**:
  - Select regions to meet regulatory requirements (e.g., GDPR).
- **Pricing** differs between regions.

**AWS Global Network**
- AWS owns a **private global backbone** network.
- **Direct Connect** uses AWS backbone, avoiding the public internet.
- Designed for **low-latency, high-throughput** communication between regions and AZs.

**Global vs. Regional Services**
| Global Services                    | Regional Services            |
|-----------------------------------|------------------------------|
| IAM, Route 53, CloudFront, WAF    | EC2, VPC, RDS, Lambda        |

**Design Best Practices**
- Use **VPC Endpoints** to avoid public internet when connecting to AWS services.
- For **multi-region** design:
  - Use **inter-region VPC Peering** or **Transit Gateway peering**.
  - Note: Inter-region VPC peering is non-transitive.
- Monitor and troubleshoot using:
  - **VPC Flow Logs**
  - **Reachability Analyzer**
  - **CloudWatch**

### Private and Public AWS Services
- Public AWS Services: Services accessed over the public internet (Without VPC). E.g S3, Lambda, SNS/SQS, DynamoDB, API Gateway, CloudFront. Secure via IAM policies, VPC endpoints, WAF or CloudFront.
- Private AWS Services: Services not exposed to the public internet (contained within your VPC); require private IP address to access. E.g EC2, RDS, ElastiCache, EFS, PrivateLink . Secure via security groups, network ACLs, Route tables

### Disaster Recovery and High Availability
| Aspect              | High Availability (HA)                         | Fault Tolerance (FT)                             |
|---------------------|-----------------------------------------------|--------------------------------------------------|
| Definition          | Ensures a system is **continuously operational** with minimal downtime.  | Ensures a system **continues to operate perfectly** even when components fail.  |
| Goal                | Minimize **downtime**                         | Ensure **zero impact** during failures           |
| Downtime Tolerance  | Minimal downtime allowed                      | No downtime allowed                              |
| Recovery            | May require **failover** or **restart**       | No recovery needed; system runs **seamlessly**   |
| Cost                | Lower cost than FT                            | Higher cost due to **redundancy**                |
| Example             | Active-Passive Load Balancer                  | RAID 1, EC2 with auto-recovery, redundant power  |

### VPC Basic Networking Design
**What Is a VPC?**
- A logically isolated network within AWS cloud.
- You define IP ranges, subnets, route tables, gateways, and security settings.
- Acts like your own private data center in the cloud.

**CIDR Block**
- Calculate the number of IPv4 addresses in a /24 subnet IP CIDR.
  Example: `10.0.0.0/24` → 32 - /24 = 2^8 = 256 host IPs - 5 reserved IPs = 251 available host IPs

**Subnets**
| Type        | Description |
|-------------|-------------|
| **Public**  | Has a route to the internet via IGW. |
| **Private** | No direct route to internet. Access via NAT Gateway. |
- One subnet = one AZ.
- Multiple subnets = multi-AZ architecture (for HA).

**VPC Core Components**
| Component           | Description |
|---------------------|-------------|
| **Subnets**         | Divide VPC into smaller networks (public/private). |
| **Route Tables**    | Define network traffic paths. |
| **Internet Gateway (IGW)** | Enables internet access for public subnets. |
| **NAT Gateway/Instance** | Allows private subnets to access the internet. |
| **Security Groups** | Stateful firewall rules at the instance level. |
| **Network ACLs**    | Stateless firewall rules at the subnet level. |
| **Elastic IPs**     | Static public IP addresses. |
| **DHCP Options Set**| Controls DNS resolution behavior in the VPC. |

**Internet Gateway vs. NAT Gateway**
| Feature              | Internet Gateway             | NAT Gateway                      |
|----------------------|------------------------------|----------------------------------|
| Public IP required?  | Yes                          | Yes (Elastic IP attached)        |
| Direction            | Inbound & Outbound           | Outbound only                    |
| Use case             | Public access (e.g. web app) | Internet access for private subnets |

**VPC Peering**
- Connect two VPCs (same or different regions/accounts).
- Uses AWS backbone; **no transitive peering**.
- Must update route tables manually.

**VPC Endpoints**
| Type           | Description |
|----------------|-------------|
| **Interface Endpoint** | Private IP access to AWS services via ENI (e.g. S3, SNS). |
| **Gateway Endpoint**   | For **S3 and DynamoDB** only. Uses route table entry. |

**VPC Flow Logs**
- Capture **network traffic metadata** for analysis.
- Can be sent to **CloudWatch Logs** or **S3**.
- Useful for troubleshooting or compliance.

**VPC Limits (default, can be increased)**
- 5 VPCs per region.
- 200 subnets per VPC.
- 500 security groups per VPC.
- 50 route tables per VPC.

**Tips for AWS Advanced Networking Exam – VPC Focus**
- Understand CIDR math
  - Be quick with subnetting calculations
  - Know how many IPs are in `/16`, `/24`, `/28`, etc
  - Remember: 5 IPs are reserved per subnet (first 4 + last)
- Know routing logic
  - How routing tables determine traffic flow
  - Difference between local routes, IGW, NAT, and VPC peering entries
- VPC Peering vs Transit Gateway vs PrivateLink
  - VPC Peering: One-to-one, no transitive routing.
  - Transit Gateway (TGW): One-to-many, scalable hub-spoke.
  - PrivateLink: Service-centric, internal access via ENI.
- Security Groups vs NACLs
  - Security Groups: Stateful, instance-level.
  - NACLs: Stateless, subnet-level, order of rules matters.
- Internet Gateway vs NAT Gateway
  - Understand which subnet requires which component
  - Know use cases: public-facing EC2 vs. internal EC2 needing updates
- VPC Endpoints
  - Gateway endpoints: for S3/DynamoDB only, free, added in route tables.
  - Interface endpoints: private IPs, cost per hour + data, supported for most AWS services.
- IPv6 in VPCs
  - Dual-stack enabled.
  - No NAT with IPv6.
  - IGW required for IPv6 internet access.
- VPC Flow Logs & Reachability Analyzer
  - VPC Flow Logs for traffic metadata (no payload).
  - Use Reachability Analyzer for **packet path tracing**.
- Hybrid Networking
  - Understand how VPC connects to on-prem via **Site-to-Site VPN** or **Direct Connect**.
  - Know HA configurations: multiple tunnels, BGP, VGW.

**Default VPC**

**Subnets, VPC Routers, and Route tables**

**IP Party**

**Elastic Network Interface, Elastic IP and Internet Gateway**

**Traffic control: Network Access Control Lists and Security Groups**

**NAT Gateway**

VPC Endpoint

VPC Peering

VPCs with Jump Server or Session Manager  
IPv6 in AWS VPC

Reachabiliyt Analyzer

VPC Flow Logs

Network Performance

Managing Global Network with AWS Cloud WAN

**AWS Global Infrastructure**

**Private and Public AWS Services**

**Disaster Recovery and High Availability**

**VPC Basic Networking Design**

# Configure Network Integration with Application Services

# Hybrid Networking Basis and VPNs

# AWS Direct Connect and Hybrid DNS

# Transitive Networking 

# Security and Compliance

# Automate AWS tasks

# Labs

      
