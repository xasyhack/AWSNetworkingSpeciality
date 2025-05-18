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
- A **Hosted Zone** is a container for DNS records for a specific domain (e.g., `example.com`).
- You must **register a domain** or create a hosted zone manually.
- A **domain can have multiple hosted zones** (public and private).
- **You can’t use CNAME at zone apex** — use **ALIAS** instead
  
**Hosted zones**
| Type               | Purpose                                                         |
|--------------------|------------------------------------------------------------------|
| **Public Hosted Zone**  | Manages DNS for internet-facing domains                      |
| **Private Hosted Zone (PHZ)** | DNS for internal VPC communication                    |

**Private Hosted Zones (PHZ)**
- Ensure **VPCs are explicitly associated** with the PHZ, or they won’t be able to resolve the internal DNS records.
| Feature              | Description                                                   |
|----------------------|---------------------------------------------------------------|
| Scope                | DNS resolution **within one or more associated VPCs**         |
| Use case             | Internal services (e.g., `internal.example.com`)              |
| Security             | Not resolvable over the public internet                       |
| Cross-VPC support    | Can associate with multiple VPCs in the same or different accounts (via Resource Access Manager - RAM)  |
| Hybrid support       | Combine with **Route 53 Resolver Inbound Endpoints** to enable on-prem queries to PHZ  |

**DNS Resolver Integration (Hybrid)**
| Component             | Function                                                          |
|-----------------------|-------------------------------------------------------------------|
| **Inbound Endpoint**  | For **on-prem to AWS** DNS resolution (e.g., to PHZ)              |
| **Outbound Endpoint** | For **AWS to on-prem** resolution                                 |
| **Resolver Rules**    | Forward specific domains (e.g., `corp.local`) to custom resolvers |

**DNS Record Types**
- ALIAS records allow pointing root domain (`example.com`) to AWS services (e.g., ELB, CloudFront, S3) without violating DNS rules
| Record Type | Purpose                                        |
|-------------|------------------------------------------------|
| A           | IPv4 address of a domain                       |
| AAAA        | IPv6 address                                   |
| CNAME       | Alias to another domain name                   |
| MX          | Mail exchange server                           |
| NS          | Delegates a subdomain to another name server   |
| TXT         | Holds arbitrary text (SPF, DKIM, etc.)         |
| ALIAS       | AWS-specific: behaves like CNAME but at zone apex |

**Subdomain Delegation**
- **Goal**: Let another team or account manage a subdomain (e.g., `dev.example.com`)
1. Create a **hosted zone** for `dev.example.com`
2. Route 53 auto-generates **NS records** (name servers) for the subdomain
3. In the **parent zone (`example.com`)**, add an **NS record** for `dev.example.com` pointing to the subdomain's name servers

**Routing Policies**
| Policy Type         | Use Case                                                       |
|---------------------|----------------------------------------------------------------|
| **Simple**          | One record, no logic                                           |
| **Weighted**        | A/B testing, blue/green deployment                             |
| **Latency-based**   | Route users to region with lowest latency                      |
| **Failover**        | Route to primary unless health check fails                     |
| **Geolocation**     | Based on user’s geographic location                            |
| **Geoproximity (traffic flow)** | Route based on user’s location + bias setting                 |
| **Multivalue**      | Round robin with optional health checks                        |

**Health Checks**
| Feature               | Description                                                   |
|------------------------|--------------------------------------------------------------|
| Endpoint monitoring    | HTTP, HTTPS, TCP                                              |
| String matching        | Check for specific response in body                          |
| Alarm integration      | Integrates with CloudWatch Alarms                            |
| DNS failover support   | Marks record as unhealthy to trigger routing change          |
| **Calculated health check** | Combines multiple health checks into a single status          |

📝 Exam sample
You need to allow an on-prem data center to resolve internal AWS hostnames in a private VPC. What should you configure?
- Set up a **Route 53 Private Hosted Zone** for internal domains
- Deploy **Route 53 Resolver Inbound Endpoint** in the VPC
- Point on-prem DNS servers to the inbound endpoint IPs
  
### 📖 Elastic Load Balancer, target groups, application load balancer, network load balancer, classic load balancer, gateway load balancer
- Elastic Load Balancing automatically distributes incoming application traffic across multiple targets such as EC2 instances, containers, and IP addresses within one or more AZs.

| Load Balancer Type         | Layer      | Use Case                                  |
|----------------------------|------------|--------------------------------------------|
| **Application LB (ALB)**   | Layer 7    | HTTP/HTTPS-based, content-aware routing    |
| **Network LB (NLB)**       | Layer 4    | High performance, TCP/UDP load balancing   |
| **Gateway LB (GWLB)**      | Layer 3/7  | Deploy 3rd-party virtual appliances (e.g., firewalls) |
| **Classic LB (CLB)**       | Layer 4/7  | Legacy support, avoid in new architectures |

**Target Groups**
- Target Groups define the destinations for requests routed by ALBs, NLBs, and GWLBs.
- Each TG includes:
 - Protocol (HTTP, HTTPS, TCP, UDP, TLS, GENEVE)
 - Port
 - Health check config
| Target Type    | Supported by                 | Notes                                          |
|----------------|------------------------------|------------------------------------------------|
| Instance       | ALB, NLB                     | Targets are EC2 instances                      |
| IP             | ALB, NLB                     | Targets can be IP addresses (in or outside VPC)|
| Lambda         | ALB only                     | Direct ALB to Lambda functions (event trigger)|
| Appliance Mode | GWLB only                    | Targets 3rd-party virtual appliances           |

**Application Load Balancer (ALB)**
- **Layer 7** (HTTP/HTTPS)
- Content-based routing (host/path/header/method/query-string)
- Supports **WebSockets** and **HTTP/2**
- **Advanced features**:
  - Authentication via Cognito or OIDC
  - Redirects, fixed responses
  - **WAF Integration**
- IP address targeting (IPv4 only)
- **Zonal DNS names** (for multi-zone routing)
- Use Cases:
 - Microservices-based apps
 - Container-based apps on ECS/EKS
 - Host-based routing (`api.example.com`, `app.example.com`)
 - Path-based routing (`/api`, `/admin`)

**Network Load Balancer (NLB)**
- **Layer 4** (TCP, UDP, TLS)
- Handles **millions of requests per second**
- Static IP and **Elastic IP support**
- Supports TLS termination and SNI
- Cross-zone load balancing is optional (off by default)
- Preserves **source IP** (ideal for security tools)
- use cases:
 - Low latency & high throughput applications (e.g., gaming, IoT, real-time streaming)
 - Lift-and-shift of on-prem TCP workloads
 - When source IP is needed for security

**Gateway Load Balancer (GWLB)**
- **Layer 3/7 hybrid**
- Uses **GENEVE tunneling** to redirect traffic to security appliances (IDS/IPS, firewall)
- Provides scale-out, load balancing, and failover for virtual appliances
- Requires:
  - **GWLB Endpoint** in consumer VPC
  - **GWLB Target Group** in appliance VPC
- use cases:
 - Centralized network inspection
 - Transparent insertion of 3rd-party firewalls
 - Service chaining in inspection VPC

**Classic Load Balancer (CLB) - (Legacy)**
- **Layer 4 or 7**, supports HTTP, HTTPS, TCP, SSL
- No content-based routing
- No support for advanced routing or containers
- Cannot register IPs or Lambda
- Being phased out – **prefer ALB/NLB**

| Feature                    | ALB         | NLB         | GWLB        | CLB         |
|----------------------------|-------------|-------------|-------------|-------------|
| OSI Layer                  | 7           | 4           | 3 (GENEVE)  | 4/7         |
| Use Case                   | Web apps    | TCP/UDP     | Sec tools   | Legacy      |
| Path-based routing         | ✅          | ❌          | ❌          | ❌          |
| Host-based routing         | ✅          | ❌          | ❌          | ❌          |
| TLS termination            | ✅          | ✅          | ❌          | ✅          |
| Static IP support          | ❌          | ✅          | ✅          | ❌          |
| WAF integration            | ✅          | ❌          | ❌          | ❌          |
| Health Checks              | HTTP        | TCP/HTTP    | HTTP        | HTTP/TCP    |

🧠 Pro Tips for Exam
- ✅ ALB is the only one supporting **OIDC authentication**
- ✅ GWLB requires **GENEVE-capable appliance**
- ✅ ALB/NLB support **IP targets**, CLB does not
- ❌ You cannot attach **WAF** to NLB or GWLB
- ✅ NLB supports **TLS Listener + SNI**
- ✅ Use **NLB** for preserving client IP and ultra-low latency
- ✅ Use **GWLB** to integrate with 3rd-party security solutions

📝 Exam sample
You need to deploy a scalable inspection layer for east-west traffic using 3rd-party firewalls, while minimizing network architecture complexity.
**Answer:** Use **Gateway Load Balancer** with a **target group** of security appliances and **GWLB endpoints** in inspected VPCs.

### 📖 VPC Endpoint Services
- A **VPC endpoint** enables private connectivity between your VPC and supported AWS services or endpoint services powered by **PrivateLink**, without traversing the public internet or using NAT/GW/VPN.
- **PrivateLink** leverages **Interface Endpoints**
- A **VPC Endpoint Service** is a service **you create and expose** (usually behind a NLB) so that **other VPCs or AWS accounts can connect** to it using an Interface Endpoint.
- Built using **Network Load Balancer**
- Associated with **Private DNS (optional)**
- You **approve or auto-accept** endpoint connection requests
- Access Control:
 - Use **IAM policies** on endpoint
 - **Security Groups** attached to the ENI
 - NACLs
 - Resource-based policies on the service
- Use Cases:
- SaaS provider exposing services to customers privately via **PrivateLink**
- Internal services across multiple VPCs/accounts without Transit Gateway or VPC peering
- Reducing attack surface by avoiding public IPs
- Centralized security services (e.g., a logging or inspection service)
- Key Requirements:
 - NLB must **target instances or IPs** (cannot use Lambda)
 - NLB subnets must have **free private IPs**
 - All target instances must be **in a subnet with connectivity to the NLB**
 - VPC Endpoint Service must be **available in the same region** as consumer endpoint
 - Only **TCP/TLS protocols** are supported

**Architecture Components**
| Component             | Description |
|-----------------------|-------------|
| **Service VPC**       | Where the service/NLB is hosted |
| **Consumer VPC**      | Where the Interface Endpoint resides |
| **NLB**               | Must be **internet-facing or internal**, must use TCP or TLS |
| **Interface Endpoint**| ENI in consumer VPC connecting to the service |
| **Private DNS**       | Enables endpoint to be accessed via domain instead of VPCE DNS |

 🧠 Pro Tips for Exam
 - ❌ VPC Peering allows full traffic; PrivateLink is for specific services
 - ✅ Interface Endpoints scale horizontally, use ENIs across AZs
 - ✅ Interface Endpoints support cross-account, cross-VPC traffic
 - ✅ NLB in service VPC must support TCP/TLS
 - ✅ You must explicitly allow other accounts or orgs
 - ❌ PrivateLink does not support UDP or ICMP
 - ✅ Use Private DNS to allow DNS resolution inside the consumer VPC

📝 Exam sample
A SaaS provider needs to expose a billing microservice in one account to customers in multiple AWS accounts with low latency and no internet access. Use **PrivateLink** by:
- Creating a **VPC Endpoint Service** in the SaaS provider account
- Backed by an internal **NLB**
- Customers connect using **Interface Endpoints**

### 📖 Amazon ECS
- Amazon ECS is a **fully managed container orchestration service** used to deploy, manage, and scale containerized applications.
- Use **`awsvpc` mode** for **fine-grained security** (ENIs, SGs, IAM).
- Tasks in `awsvpc` mode get **their own security group** and **VPC subnet placement**
- Use **task IAM roles** to grant least privilege access
- Can use **Service Connect** or **App Mesh** for secure service-to-service communication
- You can attach a load balancer to automatically register/deregister task IPs
- Target groups are used in **dynamic port mapping**

| Launch Type     | Description |
|------------------|-------------|
| **Fargate**      | Serverless compute engine; no infrastructure to manage |
| **EC2**          | Run tasks on your own EC2 instances in an ECS cluster |
| **External**     | Manage workloads on external infrastructure (on-prem or other cloud) |
| **Fargate with EKS** | Not ECS but worth knowing for contrast |

| Network Mode   | Description |
|----------------|-------------|
| **bridge**     | Docker’s default, uses NAT inside the EC2 host |
| **host**       | Container shares the host's network stack (no NAT) |
| **awsvpc**     | Each task gets an ENI (Elastic Network Interface) in the VPC |
| **none**       | No external connectivity |

**Key ECS Components**
| Component     | Description |
|---------------|-------------|
| **Task**      | A running container definition |
| **Task Definition** | Blueprint describing container images, CPU/mem, IAM role, port mappings |
| **Service**   | Maintains desired # of task replicas |
| **Cluster**   | Logical grouping of tasks or services |
| **Scheduler** | Places tasks on available infrastructure |

**Service Discovery in ECS**
- ECS integrates with **AWS Cloud Map**
- Register ECS services in **Route 53 Private Hosted Zone**
- ECS handles service health and DNS registration/deregistration

**ECS Security Best Practices**
- Use **IAM Task Roles**
- Place services in **private subnets** + use **NAT Gateway** if needed
- Enable **container-level logging** to CloudWatch
- Isolate workloads with **SGs and VPCs**

**Best Practices & Design Patterns**
- Use **Fargate + awsvpc** for max isolation and least management
- Use **Auto Scaling** for services (based on CPU/memory/custom metrics)
- Use **App Mesh** or **Service Connect** for internal communication
- Use **PrivateLink** or VPC Endpoints for accessing AWS APIs securely

 🧠 Pro Tips for Exam
- ✅ Use **`awsvpc` mode** if each task needs **own IP address** and **SG**
- ✅ ECS supports **PrivateLink**, ALB/NLB, service discovery
- ❌ ECS does **not support IPv6** in Fargate (as of now)
- ✅ ECS + ALB requires container **health checks**
- ✅ ECS tasks/services can span **multi-AZ** in the same region
- ❌ ECS does **not natively support cross-region task placement**

### 📖 CloudFront securing with TLS/SSL, private viewer access, field-level encryption, invalidating and expiring files, optimizing CloudFront distribution
**TLS/SSL for CloudFront**
- CloudFront supports HTTPS using SSL/TLS certificates.
- Certificate options:
  - **AWS Certificate Manager (ACM)** – must be in `us-east-1` for CloudFront.
  - **Custom Certificate** in IAM or ACM.
- **Viewer Protocol Policy**:
  - `Redirect HTTP to HTTPS`
  - `HTTPS Only`
- **Origin Protocol Policy**:
  - `Match Viewer`
  - `HTTPS Only`
- **SNI (Server Name Indication)**:
  - Supports multiple secure websites (TLS) on one distribution.
 
**Private Content Access (Signed URLs / Cookies)**
- Restrict access to content on S3 or custom origins.
- Methods:
  - **Signed URLs** – limit access to specific objects.
  - **Signed Cookies** – limit access to multiple objects or entire paths.
- Requirements:
  - **CloudFront Key Pair**
  - **Trusted Signer** (IAM user with the key pair)

**Field-Level Encryption (FLE)**
- Encrypt sensitive data (like PII) **at the field level** before it reaches your origin.
- Uses **asymmetric encryption (RSA)**.
- Steps:
  - Create **FLE Profile** (public key + fields to encrypt).
  - Create **FLE Config** and attach to CloudFront.
- Data is encrypted at the edge and decrypted by the origin.
- Works alongside HTTPS for full-stack encryption.

**Invalidation and Cache Expiry**
- **Invalidate** cached content manually before TTL expiry.
- Tools:
  - AWS Console
  - AWS CLI:
    ```bash
    aws cloudfront create-invalidation \
      --distribution-id <DIST_ID> \
      --paths "/index.html" "/images/*"
    ```
- Use sparingly to avoid cost; prefer versioned file names for cache busting.

 🧠 Pro Tips for Exam
- ACM certs **must be in us-east-1** for CloudFront.
- Signed cookies/URLs are used with **S3** and **custom origins**.
- FLE is for **extra security** on form data (e.g., credit cards).
- Use **versioned URLs** to avoid excessive invalidation charges.

### 📖 Customizing requests using Lambda@Edge and CloudFront Functions
**CloudFront Functions (CFF)**
- Lightweight **JavaScript-based functions** that run at CloudFront edge locations.
- Designed for **high-volume, low-latency** request processing.
- Use Cases:
 - URL rewrites/redirects
 - Header manipulation
 - Request validation (e.g., user-agent check, geoblocking)
 - A/B testing
 - Simple authentication tokens
- Execution Phase:
 - **Viewer Request**
 - **Viewer Response**
- Characteristics:
 - Extremely low latency
 - Cost-effective for short, simple logic
 - No IAM role needed (executes with CloudFront service permissions)
 - Cannot access origin (no fetch or AWS SDK)

**Lambda@Edge (L@E)**
- AWS Lambda functions **replicated globally** and run in CloudFront edge locations.
- Powered by full Lambda service (Node.js, Python).
- Use Cases：
 - **Dynamic origin selection**
 - **Origin failover handling**
 - Inserting signed cookies/headers
 - Device-based rendering
 - Advanced authentication (OAuth, JWT validation)
 - Field-Level Encryption integration
- Execution Phases:
 - **Viewer Request**
 - **Viewer Response**
 - **Origin Request**
 - **Origin Response**
- Characteristics:
 - More powerful: full Lambda environment (e.g., access to AWS SDK)
 - Higher latency than CloudFront Functions
 - Requires IAM execution role
 - Logs via CloudWatch

| Feature                  | CloudFront Functions         | Lambda@Edge                   |
|--------------------------|------------------------------|-------------------------------|
| Language                 | JavaScript                   | Node.js / Python              |
| Latency                 | Very Low                     | Higher                        |
| Execution Time Limit     | 1 ms                         | 5 sec (sync), 30 sec (async)  |
| Execution Phases         | Viewer Req / Viewer Resp     | All 4 phases                  |
| Access AWS Services      | ❌ No                        | ✅ Yes (via AWS SDK)         |
| Logging                  | Limited (Metrics only)       | Full CloudWatch logs          |
| Use Case Type            | Lightweight logic            | Heavy custom logic            |

🧠 Exam Tips
- CloudFront Functions **cannot call AWS services** (like DynamoDB/S3).
- For **dynamic origin selection**, you **must use Lambda@Edge** (Origin Request phase).
- If you want to **inject security headers**, both CFF and L@E can be used (Viewer Response phase).
- **Lambda@Edge** supports **all 4 event triggers**, while CFF supports only **Viewer Request/Response**.
- **Lower latency or cost-sensitive applications** → prefer CloudFront Functions.
- CloudFront Functions are **faster and cheaper**, but limited to JavaScript and simple use cases.
- **L@E requires deployment from us-east-1**.

### 📖 AWS Global Accelerator
- AWS Global Accelerator (AGA) is a **global traffic management service** that improves **availability and performance** for global applications using **Anycast IPs** and the **AWS global network backbone**.
- Use Cases：
 - **Global applications** needing consistent IP addresses
 - **Disaster recovery** with active-active or active-passive failover
 - **Improved latency** for users worldwide
 - **Blue/green or canary deployments** using traffic dials
 - **Gaming, VoIP, financial services** where TCP/UDP latency matters
- How It Works：
1. Global Accelerator assigns **two static Anycast IPs**.
2. Client connects to **nearest AWS edge location**.
3. AGA uses AWS backbone to route to **optimal AWS region and endpoint**.
4. If endpoint/region fails → **automatic failover** to healthy region.
   
| Feature                      | Description                                                                 |
|-----------------------------|-----------------------------------------------------------------------------|
| **Anycast IPs**             | Two static IPv4 addresses (for high availability + fault tolerance)        |
| **Global Edge Network**     | Routes traffic through nearest AWS edge location to AWS Regional endpoint  |
| **Health Checks**           | Automatically detects unhealthy endpoints and reroutes traffic             |
| **Traffic Dials**           | Control % of traffic going to each region                                  |
| **Endpoint Types**          | ALB, NLB, EC2, Elastic IPs, AWS Lambda (via ALB), IP-based endpoints       |
| **IP Preservation**         | Supports client IP preservation at endpoint (with NLB or ALB)              |
| **Failover**                | Fast, automatic regional failover within seconds                           |
| **Routing Control**         | Integrates with Route 53 Application Recovery Controller (ARC)             |
| **Support for IPv6**        | Yes (as of 2023, dual-stack support added)                                 |

Comparison: Global Accelerator vs CloudFront vs Route 53
| Feature                  | Global Accelerator                      | CloudFront                         | Route 53                          |
|--------------------------|-----------------------------------------|------------------------------------|-----------------------------------|
| **Protocol**             | TCP, UDP                                | HTTP/HTTPS                         | DNS only                          |
| **Use Case**             | Global app acceleration & HA            | Web content caching, CDN           | DNS-based routing                 |
| **Latency Improvement**  | ✅ Yes (network-level routing)           | ✅ Yes (edge caching)              | ❌ No (DNS latency not improved)  |
| **IP Type**              | Static Anycast IPs                      | Dynamic per distribution           | No IP, just DNS                   |
| **TLS Termination**      | At ALB/NLB (not AGA)                    | At CloudFront edge                 | N/A                               |
| **Failover Speed**       | Seconds                                 | Minutes (depends on cache TTL)     | TTL-based (slow unless using health checks) |

🧠 Exam Tips
- AGA provides **static Anycast IPs** – perfect for **multi-region failover** and **latency-based routing**.
- **Route 53 is DNS-based**, while **AGA is IP-based with near real-time failover**.
- Use **traffic dials** in AGA to shift traffic gradually (e.g., 70% to region A, 30% to region B).
- For **TCP/UDP protocols (non-HTTP)** like gaming or VoIP, AGA is preferred over CloudFront.
- **CloudFront** is for **HTTP content delivery**, **not** raw network acceleration.
- AGA can **improve cross-region failover and performance** by routing via AWS backbone.
- AGA **does not cache content** — it's not a CDN.

📝 Exam sample
Your company runs a multi-region application across us-east-1 and eu-west-1. You want a consistent IP address, low latency for global users, and automatic failover with minimal downtime. *What should you use?
- **AWS Global Accelerator** – provides a global Anycast IP and automatic health-based routing/failover.

### 📖 Lambda within a VPC
- By default, AWS Lambda **functions run in an isolated environment** with **access to the public internet**. To allow access to **resources inside a private VPC**, you can **attach a VPC to a Lambda function**.
- Execution Flow：
1. Lambda is invoked
2. ENI is created in selected VPC subnets (cold start)
3. Lambda uses ENI to access VPC resources (e.g., RDS)
4. If no NAT or VPC Endpoint: **no internet access**
- Use Cases：
 - Accessing **private RDS/MySQL/PostgreSQL**
 - Accessing **resources in private subnets**
 - Using **Secrets Manager** securely inside a VPC
 - Controlled **egress traffic** using NAT or security group
- Sample Architecture：
 - Lambda (in VPC) → Private Subnet (ENI) → NAT Gateway → Internet
 - Lambda (in VPC) → Private Subnet → RDS/MySQL (private subnet)

| Component             | Details                                                                 |
|----------------------|-------------------------------------------------------------------------|
| **Subnets**          | Lambda must be attached to at least **2 private subnets** in different AZs |
| **Security Groups**  | Lambda uses a SG to control outbound/inbound traffic                     |
| **ENI (Elastic Network Interface)** | Created in each subnet to allow Lambda to connect into VPC         |
| **VPC Access**       | Enables access to **RDS, EC2, ElastiCache**, etc.                        |
| **Internet Access**  | Lambda **loses public internet access** unless:                         |
|                      | - **NAT Gateway + Route Table** in private subnet                        |
|                      | - **VPC Endpoint (for AWS APIs)**                                        |
| **Performance Impact** | Cold start latency increases with VPC-attached Lambda                   |

**Best Practices**
| Area                 | Recommendation                                           |
|----------------------|----------------------------------------------------------|
| **Subnets**          | Use **private subnets** with **NAT Gateway** if needed    |
| **Cold Start**       | Use **Lambda SnapStart** (Java only) or keep warm via scheduled events |
| **Minimize Latency** | Use **VPC Lambda** only if you need VPC access           |
| **Avoid Public Subnets** | To prevent ENI exhaustion and increase security            |
| **Monitor ENIs**     | Use **CloudWatch metrics** for Lambda and VPC      

🧠 Exam Tips
✅ Lambda in VPC **loses internet access by default**  
✅ Use **NAT Gateway** or **VPC Endpoints** to restore access  
✅ Attaching Lambda to VPC creates **ENIs**, which causes **cold start delay**  
✅ Place Lambda in **private subnets**, not public  
✅ Required for access to **private RDS, ElastiCache, Redshift (without public endpoint)**  
✅ Avoid using **public subnets** with IGW for Lambda — unnecessary and insecure

📝 Exam sample
You need a Lambda function to connect to an Amazon RDS database deployed in a private subnet. Which of the following is required?
- Attach Lambda to the same VPC and private subnets as RDS  

### 📖 Running Kubernetes in AWS
- Kubernetes (K8s) is an open-source container orchestration platform. AWS provides two main options to run Kubernetes:

| Option                | Description                                             |
|----------------------|---------------------------------------------------------|
| **Amazon EKS**       | Managed Kubernetes control plane by AWS                 |
| **Self-managed K8s** | User installs and manages Kubernetes on EC2 manually    |

**Amazon EKS (Elastic Kubernetes Service)**
| Feature                 | Details                                                                 |
|--------------------------|------------------------------------------------------------------------|
| **Control Plane**        | Fully managed by AWS, highly available across 3 AZs                     |
| **Worker Nodes**         | EC2 instances or Fargate (serverless)                                  |
| **Networking**           | Uses **VPC CNI plugin** to assign **VPC IPs to pods**                   |
| **IAM Integration**      | Uses **IAM Roles for Service Accounts (IRSA)**                         |
| **EKS Add-ons**          | Managed add-ons (CoreDNS, kube-proxy, VPC CNI)                         |
| **Cluster Autoscaler**   | Automatically adjusts EC2 worker node count                            |
| **Fargate Profiles**     | Run pods without managing EC2, with per-pod billing                    |

**Networking in EKS**
| Concept                  | Description                                                                 |
|--------------------------|-----------------------------------------------------------------------------|
| **Amazon VPC CNI Plugin**| Assigns **VPC IPs directly to pods**; integrates with ENIs                 |
| **Security Groups for Pods** | Supported using **security group for pod (SGP)** feature                  |
| **K8s Service Types**    | - `ClusterIP`: Internal-only service                                        |
|                          | - `NodePort`: Maps pod ports to a node’s IP and port                      |
|                          | - `LoadBalancer`: Creates an AWS ELB (ALB/NLB) for external access         |
| **Ingress Controller**   | Use **ALB Ingress Controller** for HTTP routing to services                |
| **IP Address Management**| Assigns IPs per pod; watch out for IP exhaustion in large clusters         |
| **Egress Control**       | NAT Gateway required for private subnet pods accessing the internet        |

**Key Design Considerations**
| Topic                    | Best Practice / Tip                                                       |
|--------------------------|---------------------------------------------------------------------------|
| **Networking Mode**      | Use **Amazon VPC CNI** (native pod-to-pod routing with VPC IPs)           |
| **Scaling**              | Use **Cluster Autoscaler** or **Karpenter**                              |
| **Security**             | Use **IAM roles for service accounts** + **SG for Pods**                 |
| **Logging**              | Enable **CloudWatch Container Insights**, audit logs                     |
| **Egress**               | Place nodes in private subnets + NAT Gateway or use VPC Endpoints        |

🧠 Exam Tips
✅ Understand **how the VPC CNI plugin assigns IPs** to pods (each pod = one VPC IP)  
✅ **EKS assigns ENIs** in subnets for pods; this may **exhaust IPs** quickly  
✅ **ALB Ingress Controller** is commonly used for routing external traffic  
✅ **Security groups for pods** allow granular control (SGP)  
✅ Use **Fargate** to eliminate EC2 node management  
✅ For **public clusters**, ensure **control plane endpoint is exposed**, for **private** clusters, configure **interface endpoints**

📝 Exam sample
You are deploying a high-scale Kubernetes workload on EKS. Each pod needs an individual IP address and must be reachable from other VPC services. What CNI plugin should you use?
- The **AWS VPC CNI** allows pods to get VPC IPs and integrate with other AWS services directly.

### 📖 Amazon WorkSpaces
- Amazon WorkSpaces is a fully managed, secure Desktop-as-a-Service (DaaS) that allows provisioning Windows and Linux virtual desktops to users.

**Architecture Overview**
| Component               | Description                                                                 |
|------------------------|-----------------------------------------------------------------------------|
| **WorkSpace**           | The virtual desktop instance running Windows or Linux                       |
| **Directory Service**   | Required for authentication (Simple AD, AD Connector, or AWS Managed AD)   |
| **VPC**                 | Each WorkSpace is launched into a subnet within a VPC                      |
| **WAM (App Manager)**   | Manages packaging and deployment of desktop applications                   |
| **Connection Gateway**  | Manages secure connection between WorkSpaces clients and desktops          |

**Network & Connectivity**
| Topic                         | Details                                                                 |
|------------------------------|-------------------------------------------------------------------------|
| **Internet Access**           | Requires NAT Gateway for outbound internet if launched in private subnets |
| **Subnets**                   | Can be in **private or public subnets**; private subnets need NAT        |
| **WorkSpaces Gateway**        | Used for secure communication from client to the virtual desktop         |
| **Required Ports**            | TCP: `443` (HTTPS), `4172` (PCoIP), `4195` (WSP)                         |
| **DNS**                       | VPC DNS or custom DNS with Directory Integration                        |
| **Access via Clients**        | Web browser, iOS, Android, Windows, macOS supported                     |

**Use Cases**
| Use Case                       | Benefit                                                              |
|--------------------------------|----------------------------------------------------------------------|
| **Remote Employees**           | Secure access from anywhere without provisioning hardware           |
| **Contractors/Temp Workers**   | Quickly spin up/down desktops for short-term staff                  |
| **Secure Desktop Requirements**| Data stays within AWS; no local downloads or transfers              |
| **BYOD Environments**          | Employees access desktops using personal devices                    |

🧠 Exam Tips
✅ **Always requires a Directory Service** (Simple AD, AD Connector, or AWS Managed Microsoft AD)  
✅ **No inbound ports required** – outbound connection is initiated from WorkSpaces  
✅ **WorkSpaces launched in private subnets** need NAT Gateway to access the internet  
✅ Use **S3 VPC Endpoint** if WorkSpaces need to access S3 without NAT  
✅ Ensure **security groups and NACLs** allow required outbound ports  
✅ CloudWatch is used for **monitoring session connectivity and usage**

📝 Exam sample
You are launching Amazon WorkSpaces into a VPC with only private subnets. You want WorkSpaces to access the internet for software updates. What must you configure?
- ** Private subnets need a **NAT Gateway** to allow outbound internet access.

### 📖 Amazon AppStream 2.0
- Amazon AppStream 2.0 is a fully managed application streaming service that lets users access desktop apps securely from anywhere, without needing to rewrite them.
Scenarios:
- Secure access to internal desktop apps  
- No local installation required  
- Scaling app delivery globally  

| Feature                     | Description                                                                                   |
|----------------------------|-----------------------------------------------------------------------------------------------|
| **Service Type**           | Desktop-as-a-Service (DaaS) for application/desktop streaming                                |
| **Protocol**               | NICE DCV (optimized for low latency, high-performance streaming)                             |
| **Use Cases**              | Secure remote work, application delivery for developers/designers, software trials           |
| **Deployment Type**        | Fully managed — no infrastructure to provision manually                                      |
| **Access Method**          | HTML5 browser (no client installation required)                                               |
| **Streaming Instances**    | Run apps on AWS-managed fleets of EC2 instances                                               |
| **Auto Scaling**           | Supports fleets with automatic scaling policies                                               |
| **Storage Options**        | AppStream can access S3, EFS, OneDrive, Google Drive, and Home Folders                       |
| **Authentication**         | AWS SSO, SAML 2.0 federation, Active Directory (Microsoft AD)                                |

**Networking Considerations**
| Topic                     | Details                                                                 |
|--------------------------|-------------------------------------------------------------------------|
| **VPC Integration**       | Instances must be launched in **private subnets** with NAT/IGW access    |
| **Security Groups**       | Control access to/from streaming instances                              |
| **Streaming Gateway**     | Communication flows via AWS-managed streaming gateway using **HTTPS**  |
| **Internet Access**       | Requires NAT Gateway if in private subnet                               |
| **Domain Join**           | Supports domain join via AWS Managed Microsoft AD or self-managed AD    |

**Basic Setup Flow**
1. **Create an image** using Image Builder
2. **Create a Fleet** (Always-On or On-Demand)
3. **Create a Stack**
4. **Associate Fleet with Stack**
5. **Enable user access** (native or federated)
6. **Configure VPC, subnet, SGs**
7. **Distribute URL to users**

🧠 Exam Tips
✅ AppStream 2.0 runs entirely inside your **VPC** – configure subnets, route tables, and security groups.  
✅ Streaming instances do **not have public IPs** – use **NAT Gateway** or **VPC Endpoints** for internet access.  
✅ Streams over **HTTPS (TLS)** via AWS-managed **Streaming Gateway** – no inbound rules required.  
✅ Two fleet types:
- ✅ **Always-On** – fast start, higher cost  
- ✅ **On-Demand** – delayed start, cost-effective  
✅ Supports native **Windows desktop applications** – no rewrite required.  
✅ Can be **domain-joined** using AWS Managed Microsoft AD or self-managed AD.  
✅ Assign **IAM roles** to streaming fleets for access to AWS services like S3, DynamoDB.  
✅ Supports **persistent storage** via:
- ✅ Amazon S3 (Web folders)  
- ✅ OneDrive  
- ✅ Google Drive  
- ✅ Amazon FSx
✅ Use **Image Builder** to create custom images with pre-installed applications.  
✅ User authentication options:
- ✅ SAML 2.0  
- ✅ AWS SSO  
- ✅ AppStream 2.0 user pool  
✅ Enable **CloudWatch Logs** for session tracking and troubleshooting.  
✅ Common use case: Secure app delivery to remote users **without VPN**.  

### 📖 AWS App Mesh
**AWS App Mesh** is a **service mesh** that provides application-level networking for **microservices** to monitor and control communications across services running on **ECS**, **EKS**, **Fargate**, or **EC2**.
- App Mesh uses **Envoy** proxy as a sidecar to provide observability, traffic control, and secure communication.
- Supports **mTLS** (mutual TLS) for encrypted service-to-service communication
- Integrates with **ACM** for certificate management
Use cases:
- Fine-grained traffic control (canary, A/B testing, blue/green deployment)
- Zero-downtime deployments
- Secure and observable microservice communication

🧠 Exam Tips
✅ App Mesh provides **application-layer (L7)** networking, unlike traditional L4 networking in VPC.  
✅ Uses **sidecar pattern** via **Envoy proxy** injected alongside application containers.  
✅ Supports both ECS and EKS, but needs Envoy integration and configuration.  
✅ Common scenario: replacing traditional NGINX proxying or custom SDK retries with App Mesh.  
✅ Helps you **avoid tight coupling between services** – no hardcoded IPs or DNS logic.  
✅ Choose App Mesh when **central control of microservices routing and observability** is needed.  
✅ For **service discovery**, App Mesh integrates with **AWS Cloud Map**.  

### 📖 Securing API Gateway
- Create, publish, maintain, monitor, and secure **APIs at any scale**
- Build RESTful, HTTP, and WebSocket APIs to act as **“front doors”** for applications to access backend services
Securiry
- IAM authorization  
- Lambda authorizers (custom logic)  
- Amazon Cognito User Pools  
- Resource policies (restrict IPs, VPCs, AWS accounts)  
- API Keys (for throttling and usage plans)
- Private API
 - Require an **Interface VPC Endpoint** (powered by AWS PrivateLink)
 - Use **Resource Policies** to control who can call the API

| API Type         | Use Case                                                    |
|------------------|-------------------------------------------------------------|
| REST API         | High control, supports usage plans, caching, throttling     |
| HTTP API         | Lightweight, lower latency and cost, JWT/OIDC auth          |
| WebSocket API    | Real-time bi-directional communication (chat, games, etc.)  |

🧠 Exam Tips
✅ Know when to use **HTTP APIs vs REST APIs**  
✅ Use **VPC Link** when your API needs to reach **private ALB/NLB endpoints**  
✅ For **private APIs**, you must create a **VPC Endpoint (Interface)**  
✅ You can **throttle** and apply **usage plans** using API Keys  
✅ Combine API Gateway + Lambda for **serverless** RESTful APIs  
✅ **Edge-optimized APIs** use CloudFront for global low latency  
✅ Use **IAM auth** for service-to-service internal access  
✅ **Resource policies** are essential for **restricting access** (IP, VPC, accounts)  
✅ **Enable logging** for troubleshooting and **CloudWatch metrics** for API health 

### 📖 Enabling Enahnced Networking on Amazon EC2
| Technology                   | Supported Instance Types              | Max Throughput       | Description                                         | Driver Required     |
|-----------------------------|-------------------------------------|----------------------|-----------------------------------------------------|---------------------|
| Elastic Network Adapter (ENA)| Latest generation (e.g., C5, M5, R5)| Up to 100 Gbps       | Most common, recommended adapter for enhanced networking | ENA driver          |
| Intel 82599 Virtual Function (VF) | Older generation (e.g., C3, I3, R3) | Up to 10 Gbps        | Older adapter, lower max throughput                  | Intel VF driver     |
| Elastic Fabric Adapter (EFA) | HPC optimized (e.g., P3dn, C5n)     | Up to 100 Gbps, low latency | For HPC and machine learning workloads using SRD protocol | EFA driver          |

### 📖 Placement groups
- A logical grouping of EC2 instances within a single Availability Zone designed to meet specific workload needs.
- Placement groups influence the network topology and instance placement to optimize network performance and latency.

| Type            | Description                                                                                  | Use Case                                                |
|-----------------|----------------------------------------------------------------------------------------------|---------------------------------------------------------|
| Cluster         | Packs instances close together within a single AZ for low-latency, high-bandwidth networking | HPC, big data, low-latency workloads                     |
| Spread          | Distributes instances across distinct underlying hardware to reduce correlated failure risk   | Critical applications requiring high availability       |
| Partition       | Divides instances into logical partitions within an AZ to isolate faults                      | Large distributed and replicated workloads (e.g., HDFS) |

- **Cluster Placement Group**  
  - Instances physically close, in one AZ.  
  - Provides **low network latency** and **high throughput**.  
  - Supports enhanced networking (ENA) and high-performance networking.  
  - Limited to a single AZ, so not fault tolerant to AZ failure.

- **Spread Placement Group**  
  - Spreads instances across distinct hardware racks.  
  - Limits impact of hardware failure to a single instance.  
  - Maximum 7 running instances per AZ per group.  
  - Suitable for critical, small sets of instances requiring high availability.

- **Partition Placement Group**  
  - Instances divided into logical partitions (1 to 7).  
  - Each partition’s instances are isolated on different racks.  
  - Allows scaling beyond limits of spread groups while isolating failure domains.  
  - Ideal for distributed storage systems like Hadoop, Cassandra.

# Labs
- [Configure an Amazon EC2 Instance with Dual-Homed Network Connectivity] (https://app.pluralsight.com/hands-on/labs/2c732866-9017-4b5f-bc7b-ee8b6589ef32?ilx=true)

      
