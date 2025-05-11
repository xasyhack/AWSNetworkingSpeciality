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
 
## Introduction
- learn: design, implement, operate and maintain hybrid and cloud-based network, tasks automation, secure AWS network
- hands-on: manage AWS VPC, implement hybrid connectivity with on-premises network, monitor & performance
- prerequisites network knowledge: IPV2 and IPv6 add, Basic IPv4 subnetting, IP routing
- prerequisites AWS knowledge: IAM, EC2, CloudWatch, CloudTrail, Config, S3 buckets, CloudFormation, VPC
- tools to use: Boto3 (AWS SDK for Python to interact with AWS services), AWS CLI (Control AWS services from terminal), [IP/CIDR calculator](https://cidr.xyz/), ipcalc (python script)
- shared responsibility model:
  - AWS of the cloud: compute, storage, db, networking, hardware, physical security, global infra)
  - Customer in the cloud: data, identity management, platform, app, OS, network & firewall config, encryption, network traffic protection

## Design and Implement AWS networks

### 📖 AWS Global Infrastructure 
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
 
📝 Exam sample
You are designing a global application with latency-sensitive users in Europe, Asia, and North America.  
Your application needs to ensure:
- Low-latency user access
- Highly available endpoints
- Automatic routing to healthy regions if one fails
Which **AWS global infrastructure features** should you use to meet the above requirements?
- **Amazon CloudFront** for caching content at edge locations
- **AWS Global Accelerator** for low-latency routing to regional endpoints
- **Route 53 with health checks** for DNS-based failover
- **Deploy resources in multiple AWS Regions** for high availability

### 📖 Private and Public AWS Services
- Public AWS Services: Services accessed over the public internet (Without VPC). E.g S3, Lambda, SNS/SQS, DynamoDB, API Gateway, CloudFront. Secure via IAM policies, VPC endpoints, WAF or CloudFront.
- Private AWS Services: Services not exposed to the public internet (contained within your VPC); require private IP address to access. E.g EC2, RDS, ElastiCache, EFS, PrivateLink . Secure via security groups, network ACLs, Route tables

📝 Exam sample
Your company runs an internal application hosted on **EC2 instances in private subnets**.  
The app needs to:
- Access **Amazon S3** to retrieve files
- Access **Amazon DynamoDB** for metadata lookup
- Maintain strict compliance by ensuring **no internet access** is allowed
As the network engineer, which solution should you implement to meet these requirements **without using a NAT Gateway or Internet Gateway**?
- **Create a VPC Gateway Endpoint** for S3 and DynamoDB
- Gateway Endpoints enable **private, secure** access to supported services without routing through the internet.

### 📖 Disaster Recovery and High Availability
| Aspect              | High Availability (HA)                         | Fault Tolerance (FT)                             |
|---------------------|-----------------------------------------------|--------------------------------------------------|
| Definition          | Ensures a system is **continuously operational** with minimal downtime.  | Ensures a system **continues to operate perfectly** even when components fail.  |
| Goal                | Minimize **downtime**                         | Ensure **zero impact** during failures           |
| Downtime Tolerance  | Minimal downtime allowed                      | No downtime allowed                              |
| Recovery            | May require **failover** or **restart**       | No recovery needed; system runs **seamlessly**   |
| Cost                | Lower cost than FT                            | Higher cost due to **redundancy**                |
| Example             | Active-Passive Load Balancer                  | RAID 1, EC2 with auto-recovery, redundant power  |

📝 Exam sample
A financial services company runs a **mission-critical payment processing application** in **us-east-1**, with strict SLA requirements.
The architecture must:
- Be **highly available**
- **Recover within minutes** in case of a regional failure
- Avoid **data loss**
- Minimize **operational overhead**

Which design should you implement?
- **Active-Active Multi-Region deployment**
- Use **Route 53 with latency-based routing + health checks**
- Use **Amazon Aurora Global Database** or **cross-region RDS read replicas**
- Use **S3 Cross-Region Replication** for object durability
- Use **Global DynamoDB tables** (if applicable)
- Move to **Multi-AZ deployment**
- Place EC2s behind **ALB** across multiple AZs
- Enable **RDS Multi-AZ**

### 📖 VPC Basic Networking Design
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
 
📝 Exam sample
You are designing a VPC for a new 3-tier web application in the **us-west-2** region.  
The application requires:
- Public access to the frontend
- Secure backend services not exposed to the internet
- Access to AWS services like S3 without traversing the internet
- Centralized logging to a third-party provider
Correct Design:
- Create a **VPC** with 6 subnets across **2 AZs**:
  - **2 Public Subnets** for ALB and NAT Gateway
  - **2 Private Subnets** for app servers
  - **2 Private Isolated Subnets** for databases
- Use **Internet Gateway** for public access
- Attach **NAT Gateway** in public subnets for private subnet internet access (outbound only)
- Use **VPC Gateway Endpoints** for S3 and DynamoDB
- Use **Interface Endpoint (PrivateLink)** for access to third-party logging SaaS securely

| Problem                                    | Solution                                     |
|--------------------------------------------|----------------------------------------------|
| S3 access without NAT Gateway              | Use **Gateway Endpoint**                     |
| Secure API access to third-party service   | Use **Interface Endpoint (PrivateLink)**     |
| Cost-optimized architecture                | Use **1 NAT GW per AZ** (avoid cross-AZ data)|
| Resiliency                                 | Deploy subnets across **multiple AZs**       |

Your organization uses a multi-account AWS setup with **AWS Organizations**.  
You are tasked with designing a **shared network** architecture across accounts.
Requirements:
- Use a central VPC in a **Network Account**
- Application workloads are deployed in **Spoke Accounts**
- All accounts should access:
  - **Shared Services (e.g., Active Directory, logging)**
  - **Internet access** via NAT
  - **S3 and DynamoDB** without internet traffic
- All subnets must support **/20 level scalability**

1. **CIDR Planning**:
- Assign a large supernet to central VPC: `10.0.0.0/16`
- Divide into subnet blocks per AZ:
  - AZ1 Public: `10.0.0.0/20`
  - AZ1 Private: `10.0.16.0/20`
  - AZ1 Isolated: `10.0.32.0/20`
  - AZ2 Public: `10.0.48.0/20`
  - AZ2 Private: `10.0.64.0/20`
  - AZ2 Isolated: `10.0.80.0/20`

2. Networking Setup
- Enable **VPC Sharing** using AWS RAM (Resource Access Manager)
- Share subnets with Spoke Accounts
- Set up **VPC Peering or Transit Gateway** for centralized routing
- Use **VPC Endpoints** for S3/DynamoDB (Gateway Endpoints)
- Deploy **NAT Gateways** in public subnets of the network account

3. Routing Logic
| Route Table        | Destination           | Target                      |
|--------------------|-----------------------|-----------------------------|
| Private Subnet     | `0.0.0.0/0`           | NAT Gateway in public subnet|
| Public Subnet      | `0.0.0.0/0`           | Internet Gateway            |
| Isolated Subnet    | No internet routes    | None                        |
| All Subnets        | `10.0.0.0/16`         | Local                       |

Connectivity Features
| Feature                        | Used For                                      |
|-------------------------------|-----------------------------------------------|
| **Transit Gateway**           | Multi-account VPC mesh                        |
| **VPC Sharing**               | Centralized VPC infra                         |
| **PrivateLink Interface EPs** | Shared service access (e.g. AD, logging)      |
| **Gateway Endpoints**         | Private access to S3 & DynamoDB               |

### 📖 Default VPC
- Automatically created by AWS in each region (per account).

| Feature                 | Default VPC Behavior                           |
|-------------------------|------------------------------------------------|
| Subnets                | 1 public subnet per AZ in the region           |
| Internet Gateway (IGW) | Attached by default                            |
| Route Table            | Routes `0.0.0.0/0` to the IGW                  |
| Network ACLs           | Allow all inbound and outbound by default      |
| Security Group         | Default SG allows all outbound, no inbound     |
| CIDR Range             | `172.31.0.0/16`                                |
| Auto-assign Public IP  | Enabled by default                             |

📝 Exam sample
A developer launches an EC2 instance using the AWS Management Console without specifying any networking configurations.They expect the instance to be accessible via SSH from their corporate IP. However, they are unable to connect.
Setup Details:
- Region: `us-east-1`
- VPC: Default VPC is used automatically
- Subnet: Default public subnet
- Instance: Amazon Linux EC2 with public IP assigned
- Security Group: Default security group
- Route Table: Subnet route table includes `0.0.0.0/0 → IGW`
Problem:
- The issue is with the **default security group**, which **only allows inbound traffic from itself** (`source: sg-xxxxxx`).  
- It does **not allow inbound SSH** from external IPs. Security groups need to be **modified manually** to allow external access

### 📖 Subnets, VPC Routers, and Route tables
**Subnets**
- A **subnet** is a range of IP addresses in your VPC.
- **Types**:
  - **Public Subnet**: Has a route to an Internet Gateway (IGW).
  - **Private Subnet**: No direct route to IGW; uses NAT for outbound internet.
  - **Isolated Subnet**: No internet access (no IGW/NAT).
- **Subnet Sizing**:
  - Must be within the VPC CIDR range.
  - Cannot overlap with other subnets in the same VPC.
  - Size range: `/28` (16 IPs) to `/16` (65,536 IPs).
  - AWS reserves **5 IPs** per subnet (network address, broadcast address, etc.).
- Subnets are tied to a **single Availability Zone (AZ)**.
- **Multiple subnets can exist per AZ**, commonly for separating public/private workloads.

**VPC Router**
- The **VPC Router** is an implicit, AWS-managed component.
- It routes traffic **within the VPC and to external targets** based on route tables.
- Operates at **Layer 3 (Network Layer)**.
- You **cannot configure it directly** — behavior is controlled by **route tables**.
- VPC Router supports routing to:
  - `local` (default within VPC)
  - Internet Gateway (IGW)
  - NAT Gateway / NAT Instance
  - Virtual Private Gateway (VGW)
  - Transit Gateway (TGW)
  - VPC Peering
  - Gateway Load Balancer (GWLB)

**Route Tables**
- **Route Table** defines how traffic is directed within a VPC.
- Every subnet must be associated with exactly **one** route table.
- One route table is the **main** route table by default.

| Destination     | Target Example               | Use Case                           |
|-----------------|------------------------------|-------------------------------------|
| `local`         | Always present                | Intra-VPC communication             |
| `0.0.0.0/0`     | IGW or NAT Gateway            | Internet-bound traffic              |
| Specific CIDRs  | VGW, Peering, TGW             | VPN, Peered VPC, Transit routing    |

- **Custom Route Tables** can be created for:
  - Public subnets (route to IGW)
  - Private subnets (route to NAT Gateway)
  - VPN / TGW connectivity
    
- **Routing Logic**:
  - Longest prefix match wins
  - Example:
    - `10.0.0.0/16` → Peering
    - `10.0.1.0/24` → NAT
    - Traffic to `10.0.1.5` uses `10.0.1.0/24` route

**🧠 Exam Tips**
- Know when to use:
  - IGW vs NAT Gateway
  - Peering vs Transit Gateway
- Understand route table propagation:
  - VGW and TGW can **propagate routes** into route tables
- Isolated subnet = **no route** to IGW or NAT
- Public subnet = **must have**:
  - Route to IGW
  - Public IP / Elastic IP assigned to the instance
- Multiple subnets can share one route table
- Route tables apply at **subnet level**, not instance level

📝 Exam sample
You are designing a VPC with the following requirements:
- Application Load Balancer (ALB) must serve internet-facing traffic.
- Web servers must be deployed in private subnets and access external APIs on the internet.
- Database servers must not have any internet access.
- The architecture must span **2 Availability Zones (AZs)**.
What should the design look like?
Subnets:
- **Public Subnets** (1 per AZ):
  - For ALB + NAT Gateway
- **Private Subnets** (1 per AZ):
  - For EC2 web servers (outbound internet via NAT)
- **Isolated Subnets** (1 per AZ):
  - For RDS or database servers (no internet access)
Route Tables:
| Subnet Type     | Route Table Rules                                 |
|------------------|---------------------------------------------------|
| Public Subnet    | `0.0.0.0/0 → Internet Gateway`                    |
| Private Subnet   | `0.0.0.0/0 → NAT Gateway (in Public Subnet)`      |
| Isolated Subnet  | No route to `0.0.0.0/0`      
VPC Router:
- Implicitly handles local routing: `10.0.0.0/16 → local`
- Managed by AWS – no config required by user
- Used to route between subnets within a VPC

Your EC2 instance in a private subnet can't reach the internet. You've configured the route table to point `0.0.0.0/0 → IGW`. What's wrong?
- Use **NAT Gateway** in **public subnet**
- Modify private subnet route table: `0.0.0.0/0 → NAT Gateway`

### 📖 IP Party: BYOIP
- **Bring Your Own IP (BYOIP)** allows you to bring your **public IPv4 or IPv6 address ranges** to AWS.
- AWS advertises these IPs on your behalf, enabling you to:
  - Maintain existing IP reputation and branding.
  - Use static IPs across AWS services.
  - Support legacy systems requiring fixed IPs.
  - 
**Requirements**
- **IP Range**: Must be a **/24 IPv4** or **/56 IPv6** block.
- **Ownership**: IPs must be registered to your organization with a Regional Internet Registry (RIR) like **ARIN**, **RIPE**, or **APNIC**.
- **Authorization**: Create a **Route Origin Authorization (ROA)** for AWS ASNs **16509** and **14618**.
- **Verification**: Prove control via:
  - **X.509 certificate** if RIR supports RDAP.
  - **DNS TXT record** using Amazon VPC IP Address Manager (IPAM).
 
**Use Cases**
- **Global Accelerator**: Assign BYOIP addresses to accelerators for consistent IPs globally.
- **Network Load Balancer (NLB)**: Use BYOIP Elastic IPs for frontend IPs.
- **Legacy Applications**: Maintain existing IPs to avoid reconfiguration.
- **Content Localization**: Combine BYOIP with **RFC 8805** for precise geolocation control.
 
**BYOIP Workflow**
1. **Prepare**: Ensure IP range meets AWS requirements.
2. **Authorize**: Set up ROA with your RIR.
3. **Provision**: Submit BYOIP request in AWS.
4. **Advertise**: AWS begins advertising your IP range.
5. **Allocate**: Create Elastic IPs from your BYOIP pool.
6. **Assign**: Attach Elastic IPs to AWS resources (e.g., EC2, NLB).

**🧠 Exam Tips**
- **BYOIP** is ideal for scenarios requiring **static, recognizable public IPs**.
- **ROA** is mandatory for AWS to advertise your IP range.
- **Elastic IPs** from BYOIP pools can be used with services like **EC2**, **NAT Gateway**, and **NLB**.
- **Global Accelerator** supports BYOIP for consistent IP addresses across regions.
- **IP Reputation**: AWS may reject IP ranges with poor reputation.

📝 Exam sample
Your organization owns a block of public IP addresses that it wants to use within AWS for branding consistency and allowlisting on external partners' firewalls.
You are asked to make this block usable across **Elastic Load Balancers**, **CloudFront**, and **EC2 Elastic IPs**, with minimal downtime.
Setup Details:
- IP Range: `198.51.100.0/24` (Owned by your org, registered with RIR like ARIN)
- Services: Use with **EC2, CloudFront, ALB, and Global Accelerator**
- Region: Multi-region deployment (e.g., `us-east-1`, `eu-west-1`)
- Requirements:
  - Low-latency global content delivery
  - Use existing public IPs to avoid partner allowlist updates
Which steps are needed to allow use of the IPs in AWS services while ensuring high availability and global performance?
1. **Verify IP Ownership:**
   - Submit ownership proof via **AWS Support Center**
   - Must be an IP range from a RIR (ARIN, RIPE, etc.)
2. **Bring Your IP Range (BYOIP):**
   - Use **EC2 → IP Address Manager (IPAM)** or CLI:
     ```bash
     aws ec2 provision-byoip-cidr \
       --cidr 198.51.100.0/24 \
       --publicly-advertisable \
       --description "Our corp range"
     ```
3. **Advertise the range:**
   - After approval, use:
     ```bash
     aws ec2 advertise-byoip-cidr --cidr 198.51.100.0/24
     ```
4. **Allocate Elastic IPs from the range:**
   - These can now be attached to EC2 instances, ALBs, and Global Accelerator
5. **Use with Global Services:**
   - Assign to **Global Accelerator** for low-latency routing
   - Use for **CloudFront origins** or **custom ALB DNS**

### 📖 Elastic Network Interface, Elastic IP and Internet Gateway
**Elastic Network Interface (ENI)**
-  *virtual network interface* that can be attached to an EC2 instance within a VPC.
Key Properties
- **Primary private IP address** (mandatory)
- **Secondary private IPs** (optional)
- **Elastic IP** association (optional)
- **MAC address**, **Security Groups**, **Source/Dest Check**
Use Cases
- High availability (detach/attach to other instances)
- Multi-homed EC2s (for firewall, proxy, etc.)
- Failover between instances
- Network appliances

**Elastic IP (EIP)**
- A *static public IPv4 address* provided by AWS, used for dynamic remapping of public IPs.
- Assigned to an **ENI**, not directly to EC2
- Persistent across stop/start
- Default **1 EIP/account/region** (increase limit if needed)
- **Charged** when allocated but **not associated**
Use Cases
- Hosting internet-facing services with a static IP
- Rapid failover to standby instances
- NAT or bastion hosts

**Internet Gateway (IGW)**
- A horizontally scaled, redundant VPC component that allows **internet access** to/from the VPC.
-  **1 IGW per VPC**
- Supports IPv4
- **Stateful**: return traffic is allowed
- For IPv6, use **Egress-only IGW** for outbound-only scenarios
Requirements for Internet Access
1. VPC **attached** to an IGW
2. **Route Table** routes `0.0.0.0/0` to the IGW
3. **Subnet** must be public (with public IP auto-assigned or EIP)
4. **Security Group** allows inbound/outbound traffic
5. **NACLs** allow traffic

### 📖 Traffic control: Network Access Control Lists and Security Groups
**Security Groups (SGs)**
- A **virtual firewall** that controls **inbound and outbound traffic** for AWS resources (mainly EC2 instances).
- - **Stateful**: Return traffic is automatically allowed
- Applies at the **instance level**
- Supports **allow rules only** (no deny)
- **Inbound:** All traffic **DENIED** by default
- **Outbound:** All traffic **ALLOWED** by default
- Rules can be based on:
  - IP (CIDR)
  - Protocol (TCP, UDP, ICMP)
  - Port ranges
  - **Security Group ID** (for internal reference)

**Network Access Control Lists (NACLs)**
- A **subnet-level firewall** that controls **inbound and outbound traffic** for entire subnets.
- **Stateless**: Return traffic must be explicitly allowed
- Rule #, Protocol, Port Range, Source/Destination, Action (Allow/Deny)
- Applies at the **subnet level**
- Supports both **allow and deny** rules
- Rules are evaluated in order by **rule number** (lowest first)
- Default NACL: **Allows all** inbound and outbound traffic
- Custom NACL: **DENIES all** traffic until you add rules
Use Cases
- Add additional security filtering beyond Security Groups
- Quickly deny traffic from known bad IP ranges
- Logging and monitoring subnet-level access

| Feature             | Security Group (SG)         | Network ACL (NACL)               |
|---------------------|-----------------------------|----------------------------------|
| Scope               | EC2 Instance (ENI)           | Subnet                           |
| Stateful/Stateless  | **Stateful**                 | **Stateless**                    |
| Rules Type          | Allow only                   | Allow and Deny                   |
| Applied To          | ENI of EC2, RDS, ELB, etc.   | Entire Subnet                    |
| Evaluation Order    | All rules                    | Rule number order (lowest first) |
| Default Behavior    | Inbound denied, Outbound allowed | All allowed (default NACL)  |
| Use Cases           | Instance-level access control| Subnet-level control, blacklisting|

**Best Practices**
- Use **Security Groups** as primary access control.
- Use **NACLs** for:
  - Blocking specific IP ranges
  - Adding additional subnet-level defense
- Use **logging** (VPC Flow Logs) to audit access behavior.

### 📖 NAT Gateway
- A **Network Address Translation (NAT) Gateway** enables **instances in a private subnet** to initiate **outbound** traffic to the internet or other AWS services, but **prevents inbound traffic** from the internet.
- **Managed by AWS** (scalable and high availability in one AZ)
- **Outbound only** internet access
- Uses **Elastic IP**
- Only works with **IPv4**
- Must reside in a **public subnet**
- Highly available within an **Availability Zone (AZ)**

| Feature                 | NAT Gateway             | NAT Instance               |
|-------------------------|-------------------------|----------------------------|
| Type                    | Managed service         | EC2 instance               |
| HA by default           | Yes (per AZ)            | No (must configure manually)|
| Performance             | Scales automatically    | Manual instance type scaling|
| Maintenance             | AWS-managed             | User-managed                |
| Cost                    | More expensive          | Cheaper (for small workloads)|
| Logging                 | VPC Flow Logs           | Full control (can install tools)|

Setup Requirements
1. Deploy NAT Gateway in a **public subnet**
2. Attach an **Elastic IP**
3. Add a route in the **private subnet's route table**:
   Destination: 0.0.0.0/0
   Target: NAT Gateway ID
5. 4. Ensure security groups/NACLs allow traffic
  
Use Cases
- Private EC2 instances downloading updates or patches
- Access to external APIs from private apps
- Accessing S3 or DynamoDB via VPC endpoint alternatives

**Private NAT Gateway**
- A Private NAT Gateway (introduced in 2023) is a fully-managed Network Address Translation (NAT) service that allows private subnets to communicate with other private networks, such as:
- Other VPCs via VPC Peering or Transit Gateway
- On-premises networks via VPN or Direct Connect
- Private AWS services using VPC Endpoints (Interface/PrivateLink)
Use cases
- EC2 in private subnet accessing internal microservices in another VPC
- Hybrid cloud workloads needing access to on-prem apps
- Multi-tier architecture with separate shared services VPC
- Workloads with **no internet egress requirement**

| Feature                | Public NAT Gateway | Private NAT Gateway |
|------------------------|--------------------|----------------------|
| Internet Access         | ✅ Yes             | ❌ No                |
| Elastic IP Needed       | ✅ Yes             | ❌ No                |
| Internet Gateway Needed | ✅ Yes             | ❌ No                |
| Used for S3 access?     | ✅ Yes (unless using VPC Endpoint) | ❌ Use Gateway Endpoint |
| Use Case                | Private subnet to Internet | Private subnet to internal services |

**🧠 Exam Tips**
- Does **not** need IGW or EIP
- Combine with **Transit Gateway** or **VPC Peering** for cross-VPC
- For S3/DynamoDB access, use **Gateway VPC Endpoints**
- Great for **compliance-restricted environments**
- **Can't replace** public NAT gateway when internet is required
  
### 📖 VPC Endpoint
- A **VPC Endpoint** enables **private connections** between your VPC and AWS services **without using an Internet Gateway, NAT, VPN, or Direct Connect**.
Use Cases
- Secure communication with **SSM**, **KMS**, **S3**, or **Secrets Manager**
- Prevent EC2 instances from accessing AWS services over the public internet
- Lock down **S3 bucket access** to only within your VPC (via Gateway Endpoint)
- Access partner services or customer-managed services using PrivateLink

**Interface Endpoint**
- Powered by **AWS PrivateLink**
- Uses **Elastic Network Interfaces (ENIs)** with **private IPs** in your VPC
- Supports most **AWS services** (e.g., SSM, CloudWatch, KMS, API Gateway, etc.)
- Can also connect to **customer-managed services** and **partner services**
- Support security group and endpoint policies

**Gateway Endpoint**
- Only supports **Amazon S3** and **DynamoDB**
- Targets a **route table**, not an ENI
- Does **not use IP addresses** (so no ENI or security group)
- Support resource policies

| Feature                  | Interface Endpoint            | Gateway Endpoint           |
|--------------------------|-------------------------------|----------------------------|
| Used For                 | Most AWS services             | Only S3 and DynamoDB       |
| Networking               | ENI (Elastic Network Interface) | Route Table                |
| IP Addresses             | ✅ Yes                        | ❌ No                      |
| Security Group           | ✅ Yes                        | ❌ No                      |
| PrivateLink              | ✅ Yes                        | ❌ No                      |
| Supported in Transit GW  | ✅ Yes                        | ❌ No                      |
| Cost                     | 💰 Charged per hour + GB      | 💸 Free                    |

**🧠 Exam Tips**
- ❗ **Interface Endpoint = PrivateLink (ENI)** — supports security groups
- ❗ **Gateway Endpoint = Route Table Only** — supports S3/DynamoDB
- VPC Endpoints avoid public internet traversal
- Interface Endpoints can be used with **SSM** to access private EC2
- Use **VPC Endpoint Policies** to control what services/resources are accessible
- Combine with **NAT Gateway** carefully — ensure **specific routes** to endpoints override NAT path
  
### 📖 VPC Peering
- VPC Peering is a **network connection between two Virtual Private Clouds (VPCs)** that enables you to **route traffic privately** using **private IP addresses** without going through a gateway, VPN, or the internet.
- **One-to-One** relationship between two VPCs
- Traffic remains **within the AWS network**
- Supports **intra-region** and **inter-region** peering
- **No transitive peering**: You must create individual peering connections for each pair
- Works across **different AWS accounts** and **different regions**
Use Cases
- Access resources like **EC2, RDS, Lambda** across VPCs
- Enable communication between **different business units or teams**
- Connect **production** and **development** environments securely
How it works
1. VPC A initiates a **peering request** to VPC B
2. VPC B **accepts** the request
3. Update **route tables** in both VPCs to allow traffic
4. Adjust **security groups** and **NACLs** if necessary

Important Considerations
| Limitation           | Description                                                                      |
|----------------------|----------------------------------------------------------------------------------|
| ❌ Transitive Routing | No — cannot route traffic from VPC A → VPC B → VPC C                             |
| ❌ Overlapping CIDRs | VPCs must have **non-overlapping CIDR blocks**                                  |
| ⚠️ Route Tables       | Must manually add routes to each subnet in both VPCs                            |
| 🔐 IAM Controls       | Peering request/acceptance can be controlled with IAM policies                  |

  
| Feature              | VPC Endpoint                                                                 | VPC Peering                                                |
|----------------------|------------------------------------------------------------------------------|-------------------------------------------------------------|
| **Purpose**           | Connect to AWS services (S3, DynamoDB) or PrivateLink services privately | Establish **private connectivity** between two VPCs         |
| **Types**             | - Interface Endpoint (ENI)<br>- Gateway Endpoint (S3, DynamoDB only)         | Peering connection (only one type)                          |
| **Scope**             | Service-level access                                                         | VPC-to-VPC communication                                    |
| **Cross-Region**      | ✅ (Interface Endpoint supports it)                                          | ✅ Supported                                                |
| **Use Case**          | Access AWS services or SaaS without NAT/IGW and public IP                    | Share resources (EC2, RDS, etc.) between VPCs, Works across accounts and regions  |
| **Cost**              | Charged per hour + data transfer                                             | Data transfer charges (intra-region is cheaper)            |
| **Route Table**       | - Gateway Endpoint: Route table<br>- Interface: SG & DNS                     | Route table updates in both VPCs required                   |
| **Scalability**       | Scales via PrivateLink and multiple endpoints                               | Limited by number of peering connections per VPC            |

CLI Command
```bash
# Create peering connection
aws ec2 create-vpc-peering-connection \
  --vpc-id vpc-11111111 \
  --peer-vpc-id vpc-22222222

# Accept peering request
aws ec2 accept-vpc-peering-connection \
  --vpc-peering-connection-id pcx-abc123

# Add route to route table
aws ec2 create-route \
  --route-table-id rtb-12345678 \
  --destination-cidr-block 10.2.0.0/16 \
  --vpc-peering-connection-id pcx-abc123
```

**🧠 Exam Tips**
- **VPC Endpoint** = Private access to **services**
- **VPC Peering** = Private access to **resources in other VPCs**
- **No transitive routing** in either!
- **VPC Peering is regional by default** but can be extended **inter-region**
- **No DNS resolution by default** across peered VPCs (unless enabled)
  - Use `enableDnsResolutionFromRemoteVpc` & `enableDnsHostnames`
- **No transitive routing or edge-to-edge routing**
- **Security groups and NACLs must be updated** to allow traffic
- **Peering is not the best fit** for complex or large-scale mesh networks — consider **Transit Gateway**

### 📖 Jumping into VPCs with Jump Server or Session Manager  
- A **Bastion Host** is a jump box EC2 instance placed in a **public subnet** that acts as a gateway for admins to SSH into **private EC2 instances**.
- **Session Manager** is part of **AWS Systems Manager (SSM)** that allows secure, auditable access to EC2 instances **without SSH or bastion hosts**.
EC2 must have:
  - **SSM Agent installed**
  - **IAM role** with `AmazonSSMManagedInstanceCore` policy
  - **SSM endpoint** in private subnet (for no-internet access)
- VPC endpoints required for private-only SSM access
  - com.amazonaws.<region>.ssm
  - com.amazonaws.<region>.ec2messages
  - com.amazonaws.<region>.ssmmessages

| Feature                | Bastion Host                                | Session Manager (SSM)                         |
|------------------------|----------------------------------------------|-----------------------------------------------|
| Setup                  | EC2 instance in public subnet                | No EC2 needed                                 |
| Access Type            | SSH (port 22)                                | Secure browser/CLI access                     |
| Security Group         | Needs inbound SSH from your IP               | No inbound rules needed                       |
| IAM Requirement        | Optional                                     | Mandatory (SSM permissions)                   |
| Auditability           | Limited (CloudTrail logs only)               | Full logging to CloudWatch or S3              |
| Cost                   | EC2 cost + EIP (if used)                     | No extra EC2 cost                             |
| MFA                    | Not native                                   | Supported                                     |
| Network Dependency     | Requires internet/NAT to access private EC2  | Works in private subnet (SSM Agent + VPC endpoints) |
| Use Case               | Legacy SSH access                            | Secure, modern access without SSH             |

### 📖 Consideration for IPv6 in AWS VPC
VPC
- You can assign an **IPv6 CIDR block** when creating a VPC.
- Example: `/56` block allocated from Amazon pool.
- You can associate `/64` blocks to subnets.
Subnet
- **Dual-stack**: Can have both IPv4 and IPv6.
- Every subnet must have **separate routing** for IPv6.
Internet access
- IPv6 traffic uses **Internet Gateway** (IGW) directly, **no NAT required**.
- Add **route for `::/0`** to IGW for outbound IPv6.

### 📖 Reachabiliyt Analyzer
- Reachability Analyzer is an **automated network path tracing tool** that analyzes and **validates network paths** between two AWS resources **within a VPC or across VPCs (peered)**.
How It Works
1. You define a **source** and **destination** (EC2, ENI, Lambda, etc.).
2. The tool checks:
   - Route Tables
   - Security Groups
   - NACLs
   - Internet Gateway (IGW), NAT Gateway, Transit Gateway
   - VPC Peering / VPN / Direct Connect
3. Generates a **graphical and textual report** showing if the path is reachable or not.

### 📖 VPC Flow Logs
- **VPC Flow Logs** capture **IP traffic metadata** flowing to and from network interfaces (ENIs) in a VPC.

Where Can Flow Logs Be Created?
| Level           | Scope                                |
|-----------------|---------------------------------------|
| VPC             | Logs for all ENIs in the VPC          |
| Subnet          | Logs for all ENIs in the subnet       |
| Network Interface (ENI) | Logs for individual ENIs        |

Configuration Output Options
| Destination          | Notes                                                  |
|----------------------|--------------------------------------------------------|
| Amazon CloudWatch Logs | Near real-time log stream, searchable via Insights     |
| Amazon S3             | Cost-effective long-term storage, use Athena for query |
| Amazon Kinesis Data Firehose | For custom analytics or streaming destinations |

Log Format
- version account-id interface-id srcaddr dstaddr srcport dstport protocol packets bytes start end action log-status
2 123456789012 eni-abcde123 10.0.0.1 10.0.0.2 443 49152 6 10 840 1609459200 1609459260 ACCEPT OK
- Log Status Values
| Value    | Meaning                                                            |
|----------|--------------------------------------------------------------------|
| OK       | Traffic recorded successfully                                     |
| NODATA   | No traffic during the aggregation interval                        |
| SKIP     | Logs skipped (e.g., internal error or unsupported traffic type)    |

Security & IAM Permissions
- `ec2:CreateFlowLogs`
- `logs:CreateLogGroup`
- `logs:CreateLogStream`
- `logs:PutLogEvents`
- `s3:PutObject` (if using S3)
  
Limitations
- Flow Logs do **not capture**:
  - DNS traffic (use Route 53 logs instead)
  - Windows license activation traffic
  - Traffic to Amazon metadata (`169.254.169.254`)
  - ARP or DHCP packets
- Cannot change aggregation interval (1min default)

🧠 Pro Exam Tips
- Flow logs **only capture metadata**, not packet payloads.
- Always check IAM permissions if logs are not delivered.
- Use **Athena on S3 logs** for efficient, serverless querying.
- For VPC Peering: Flow Logs must be enabled in **both VPCs** to monitor cross-VPC traffic.
- Use in combo with **Reachability Analyzer** for path + visibility insights.

### 📖 Network Performance
Enhanced Networking (ENA & Intel VF)
- **ENA (Elastic Network Adapter)**:
  - Used by default for most **Nitro-based instances**.
  - Up to **100 Gbps** throughput.
  - Low CPU overhead and low latency.
  - Use ENA for performance-sensitive workloads.

- **Intel 82599 VF** (older):
  - Up to **10 Gbps** throughput.
  - Less common in new instance types.

Placement Groups
**Cluster** → Max performance, **Partition** → Isolation + scaling, **Spread** → Fault tolerance.
| Type              | Best For                           | Description |
|-------------------|-------------------------------------|-------------|
| Cluster           | **Low-latency, high throughput** between instances | Same AZ, same rack |
| Partition         | **Large distributed workloads** like HDFS, Kafka | Spread across partitions |
| Spread            | **Critical, isolated instances** | Spread across hardware racks and AZs |

Elastic Fabric Adapter (EFA)
- **Best for tightly-coupled HPC** (e.g., MPI apps).
- Provides **OS-bypass** for ultra-low latency.
- Only supported in **specific EC2 instances**.

MTU and Jumbo Frames
**Enable jumbo frames** in large payload or HPC scenarios.
- Standard MTU: **1500 bytes**
- Jumbo MTU: **9001 bytes**
  - Supported within same VPC, Direct Connect, VPN
- Helps reduce CPU overhead in large data transfers

Throughput Optimization Tips
| Strategy                            | Notes                                      |
|-------------------------------------|--------------------------------------------|
| Choose instance with high ENI limit | More bandwidth and connections             |
| Use NLB instead of ALB              | NLB is layer 4 and faster for raw traffic  |
| Use Gateway Load Balancer for appliances | Maintains performance and simplifies routing |
| Use S3 Transfer Acceleration        | Boosts S3 upload/download globally         |
| Enable HTTP keep-alive              | Avoids connection reestablishment latency  |

Cross-Region Performance
- Use **Global Accelerator**:
  - Routes users to optimal endpoints
  - Leverages AWS global backbone
- Use **S3 Transfer Acceleration**:
  - Speeds up cross-continent uploads

High Availability Design
- Use **multi-AZ deployments**
- Use **Route 53 health checks** and failover routing
- Use **NAT Gateway** across multiple AZs

🧠 Pro Tips for Exam
- ENA is default in modern EC2 → no config needed
- Use **monitoring tools** like **CloudWatch + VPC Flow Logs** to verify performance
- Use **Performance Insights** for RDS or **CloudWatch metrics** for EC2/EBS/ENI
- Understand **packet per second (PPS)** vs **bandwidth (Gbps)** needs

### 📖 Managing Global Network with AWS Cloud WAN
- **AWS Cloud WAN** is a managed wide-area networking (WAN) service that helps you build, manage, and monitor a **global network** using **AWS backbone** across multiple Regions and on-premises locations.
- Cloud WAN centralizes network control using **Core Network Policies**, replacing the need for complex manual configuration using Transit Gateways (TGWs), VPNs, and VPC peering.

Lifecycle Flow
1. Create a **Global Network** (one per Org)
2. Define a **Core Network** (name, ASN)
3. Define **Network Segments** (prod, dev, etc.)
4. Create a **Core Network Policy**
5. Attach **VPCs, TGWs, VPNs** to **CNEs**
6. Define **Route tables and propagation rules**
7. Monitor via **AWS Network Manager**

| Term                  | Description                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| **Core Network**      | The global network managed by Cloud WAN                                     |
| **Network Segments**  | Like VRFs – isolated routing domains (e.g., prod, dev, shared)              |
| **Core Network Edge (CNE)** | Regional attachment point for VPCs, VPNs, TGWs                        |
| **Policy Document**   | JSON-based config file that defines attachments, routing, segments          |
| **Attachment**        | Connection between VPC/TGW and Core Network                                 |

## Configure Network Integration wit Application Services
### 📖 Domain name system
- DNS (Domain Name System) translates **human-readable domain names** into **IP addresses** that computers use to identify each other over the internet or within a private network.
- Split-Horizon DNS: Use **same domain name** with **different IPs** in public and private zones. Prevents leaking internal IPs externally.

| Service            | Description                                                                   |
|-------------------|--------------------------------------------------------------------------------|
| **Route 53**       | AWS's highly available and scalable DNS and domain name registration service  |
| **Private Hosted Zones** | Used for **internal DNS resolution** within a VPC                       |
| **Public Hosted Zones**  | Used for **internet-facing domains**                                    |

| Feature                        | Details                                                                 |
|-------------------------------|-------------------------------------------------------------------------|
| **Routing Policies**           | Simple, Weighted, Latency-based, Failover, Geolocation, Multivalue     |
| **Health Checks**              | Monitors endpoints, supports automatic failover                        |
| **DNS Failover**               | Reroutes traffic when endpoint fails                                   |
| **Private Hosted Zone (PHZ)**  | Resolves DNS names **within VPCs only**                                |
| **Inbound/Outbound Endpoints** | For **hybrid cloud DNS resolution** (Route 53 Resolver)                |

**Route 53 Resolver**
| Component              | Description                                                                 |
|------------------------|-----------------------------------------------------------------------------|
| **Inbound Endpoint**   | On-prem → AWS DNS resolution                                                |
| **Outbound Endpoint**  | AWS → On-prem DNS resolution                                                |
| **Rules**              | Forward specific domains to custom DNS resolvers (e.g., `corp.example.com`) |

**Routing Policy Use Cases**
| Policy Type         | Use Case Example                                                |
|---------------------|------------------------------------------------------------------|
| **Simple**          | Single resource per domain                                       |
| **Weighted**        | Blue/Green deployments, A/B testing                              |
| **Latency**         | Global applications – route to region with lowest latency        |
| **Failover**        | DR setup – route to healthy resource                            |
| **Geolocation**     | Route based on user’s location                                   |
| **Multivalue**      | Acts like Round Robin + Health Checks                            |

**Integration Scenarios**
| Scenario                              | Solution                                                                 |
|---------------------------------------|--------------------------------------------------------------------------|
| On-prem to AWS DNS                    | Route 53 Resolver + Inbound Endpoint                                     |
| AWS to on-prem DNS                    | Route 53 Resolver + Outbound Endpoint + Forwarding rules                 |
| Multi-region resolution               | Route 53 with **latency** or **geolocation** routing policies            |
| Service discovery in VPC              | Use **Route 53 Private Hosted Zone** or **Cloud Map**                    |

📝 Exam sample
Your company has a hybrid architecture. You want DNS resolution for `internal.corp` from AWS resources and also resolve AWS private domains from on-prem. What should you configure?
- Set up Route 53 Resolver **inbound endpoint** for on-prem → AWS DNS
- Set up **outbound endpoint** with forwarding rule for `internal.corp`
- Use Private Hosted Zones for AWS-internal domains
  
### 📖 Route 53 hosted Zones, zone records, subdomain delegation, routing policies, health checks, private hosted zones

### 📖 Elastic Load Balancer, target groups, application load balancer, network load balancer, classic load balancer, gateway load balancer

### 📖 VPC Endpoint Services

### 📖 Amazon ECS


# Labs
- [Configure an Amazon EC2 Instance with Dual-Homed Network Connectivity] (https://app.pluralsight.com/hands-on/labs/2c732866-9017-4b5f-bc7b-ee8b6589ef32?ilx=true)

      
