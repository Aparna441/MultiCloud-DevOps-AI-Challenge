## Key Outcomes

Day 2 of the AWS training covered foundational cloud concepts (elasticity, scaling, high availability, CDN/edge locations) and moved into hands-on EC2 practice. Students created both Linux and Windows virtual machines, connected to them via browser console and RDP respectively, and learned instance lifecycle management including termination behavior. The session established Mumbai as the primary production region for learning purposes and clarified key differences between POC accounts and standard AWS accounts.

---

## Decisions Made

- **Mumbai selected as the primary AWS region** for all class practicals; region selection should be based on latency ping results to data centers, not the instructor's physical location 
- **Root account to be used** for POC accounts since IAM user creation is restricted in the new POC account type 
- **Amazon Linux 2023** selected as the default OS for Linux VMs; **Windows Server 2025 Base** selected for Windows VMs (Data Center Edition used in real enterprise environments) 
- **GP3 SSD** confirmed as the standard EBS volume type used in real-time production environments 
- **Key pair (PEM format, RSA algorithm)** required when connecting to Windows VMs via external RDP tools; not needed when using the AWS browser console 
- Every VM must be **terminated after each practice session** to avoid unnecessary billing 

---

## Core Concepts Covered

### Elasticity

- **Elasticity** = ability to increase or decrease compute resources; all AWS services starting with "E" (EC2, EBS, ECR, ECS, EFS, EKS) are elastic services 
- Elasticity is a **concept**; auto-scaling is the **feature** that implements it 

### Scaling Types

- **Vertical scaling**: same machine gets a larger size (e.g., upgrading a laptop's RAM); scale-down requires restart, cannot be done automatically 
- **Horizontal scaling**: additional separate resources added to handle load; auto scale-down is possible by terminating excess instances 
- Horizontal scaling uses **identical-sized replicas** created via AMI + Launch Templates 

### Auto-Scaling

- Resources automatically increase or decrease based on configurable conditions: **CPU, memory, or traffic** 
- Real-world example: Flipkart running 100 servers normally scales up by 10% when traffic increases by 10% 
- Configuration options include minimum, desired, and maximum instance counts with trigger thresholds 

### High Availability (HA)

- Running the same application stack across **two different regions** (e.g., Mumbai + UAE) so that if one data center goes down, the other serves traffic 
- Cost is **double** since full hardware resources are duplicated 
- **Database sync layer required** between HA zones; web/middleware servers do not need sync 
- HA is part of horizontal scaling conceptually — more data centers added 

### Disaster Recovery (DR)

- DR is **separate from HA**: kept in a third zone, **passive** (not actively serving traffic) 
- DR is activated only when both primary and HA regions go down 
- **Active-Active**: both instances serving traffic simultaneously; **Active-Passive**: one serves traffic, one is on standby 

### CDN / Edge Locations

- **CDN (Content Delivery Network)** = delivers content from edge locations near the end user rather than the origin data center 
- AWS CDN service is **CloudFront**; 900+ edge locations globally — no need to memorize count 
- Analogy: like Blinkit's dark stores near your neighborhood delivering goods in 10 minutes instead of from a central warehouse 
- CDN is for **front-end content delivery** (caching/distribution), not load balancing 
- Edge locations ≠ full data centers; they serve cached content to reduce latency 

---

## AWS Account Types: POC vs. Standard

- New AWS accounts are now **POC (Proof of Concept) accounts** with limited access 
- POC accounts: DigiLocker verification mandatory, 200 free credits provided, regions limited (only one region visible/usable) 
- POC account has a **[settings.aws.amazon.com](https://settings.aws.amazon.com)** portal (new) with Projects, Teams, Billing, Support tabs; IAM users created via Teams section 
- **Do not mention POC account in interviews** — state that you worked on a paid/production account 
- Standard console access: **[console.aws.amazon.com/console](https://console.aws.amazon.com/console)** 
- Students on POC accounts should use the root account for all practicals 

---

## AMI (Amazon Machine Image)

- AMI = **customized operating system image** provided by Amazon, optimized for AWS hardware 
- Includes pre-installed software for: faster boot times, monitoring agents, Active Directory integration, application optimization 
- Each AMI has a **unique AMI ID**; ID changes if the region changes 
- AMI ID changes per region — same image in Mumbai vs. UAE will have different IDs 
- **Always select verified AMIs** (green tick) in production and interviews; avoid unverified community AMIs 
- 5,163+ AMIs available in the marketplace including third-party and community images 
- Custom AMIs can be created and shared (used as "golden images" for replication) 
- AMI is the basis for horizontal scaling replicas — template contains the AMI, which is used to spin up identical instances 

---

## EC2 Instance Creation Walkthrough

### Linux VM (Amazon Linux 2023)

Key configuration steps covered: 

- **Name**: descriptive label for the instance
- **AMI**: Amazon Linux 2023 (free tier eligible, kernel version 6)
- **Instance type**: t3.micro (2 vCPU, 1 GB RAM) — free tier
- **Key pair**: skipped for browser-based console connection
- **Network**: left as default (networking covered in a separate dedicated class)
- **Storage (EBS)**: 30 GB root volume (C drive equivalent); additional EBS volumes can be added as D drive equivalent via "Add New Volume" 
- **Traffic**: HTTP and HTTPS allowed

### Windows VM (Windows Server 2025 Base)

- Select **Windows Server 2025 Base** AMI (Data Center Edition used in real enterprise) 
- Instance type t3.micro has only 1 GB RAM — slow for Windows but free tier; 2 GB RAM minimum recommended 
- **Key pair required** (PEM format, RSA algorithm) to decrypt the administrator password for RDP login 
- Minimum **30 GB storage** for Windows 

---

## Connecting to Instances

### Linux VM — Browser Console

1. Select instance → click **Connect**
2. Choose **EC2 Instance Connect** option
3. Click **Connect** — lands directly in terminal 
- Total clicks: ~4 from VM creation to connected terminal 

### Windows VM — RDP (6-Step Process)

1. Install/open RDP client (pre-installed on Windows; Mac users download **Windows App** from Microsoft's official website) 
2. Download the **RDP shortcut file** from the Connect tab 
3. Open RDP client and run the shortcut file 
4. **Retrieve password**: upload the downloaded PEM key file → click **Decrypt** to generate the password 
5. Enter **username** (`Administrator` by default) and the decrypted password 
6. Accept the certificate prompt (first-time connection) → logged in 

**Important notes:**

- Windows Home Edition **does not support RDP** — Windows Professional required 
- Public IP = External IP = same thing; used as the hostname in RDP 
- If public IP is not generated, check **Network Settings → Auto-assign Public IP** is enabled 
- Mac users: install **Windows App** from Microsoft's official site (not App Store) 

---

## Instance Lifecycle & Termination Behavior

- **Terminate = Delete** in AWS terminology — both are identical 
- After termination, instance remains **visible for 10–15 minutes intentionally** — AWS keeps it so accidental deletions can be identified and configuration info retrieved 
- Information retrievable post-termination: instance name, CPU/memory specs, key pair used, network configuration 
- **VM itself cannot be recovered** after termination — only metadata/configuration info is accessible 
- Data can be recovered if **snapshots/backups** were taken before termination 
- Resources are fully released after the grace period — no billing continues 
- Reboot option available from right-click menu on instance without needing to log in 

---

## EBS (Elastic Block Storage)

- EBS = **virtual external hard drive** attached to an EC2 instance 
- Root volume (C drive) = minimum 8 GB, class uses 30 GB 
- Additional EBS volumes added as separate drives (D drive equivalent) via "Add New Volume" 
- Volume types: **GP3 SSD** is the standard in real-time use; magnetic (HDD) types are being deprecated 
- In Linux terminology, attaching EBS = **mounting** a volume to a mount point (directory) 
- EBS described as a "virtual raw hard drive" — accurate technical characterization 

---

## Region Selection Logic

- Use **latency ping tool** (link shared in Zoom chat) to ping all AWS data centers and select the one with lowest response time 
- Share the ping link with clients and have them test from their location over 24 hours; use the average result 
- **Never select a region purely based on your own location** — base it on where end users are 
- Avoid placing HA/DR in the same country to protect against country-level outages (e.g., AWS India contract termination scenario) 
- CDN edge locations handle geographic distribution; full data centers only needed where primary user base is concentrated 

---

## Real-World Windows Admin Context (Gangadhar's Experience)

Gangadhar (10 years Windows Admin) shared how Windows Server is used in production: 

- **Splunk** installed on Windows Server for log monitoring and alerting
- **Windows patching** managed via **BigFix** (third-party patching tool) — not built-in Windows Update
- Patching workflow: identify unpatched servers → raise change management ticket → coordinate with application team for downtime window → inject KB articles → reboot multiple times → confirm services are up 
- **Disk space management**: C drive space monitored and cleaned before patching
- Database servers: DB team informed to bring down and restart instances during maintenance 
- AWS **CloudWatch** handles automatic monitoring of Windows servers in the cloud 
- VDI environments use the same RDP/remote connectivity principles; only the client software differs (e.g., Citrix vs. standard RDP) 

---

## IAM, Roles & Policies (Brief Coverage)

- **Policy**: set of permissions applicable broadly (e.g., all users can start/stop EC2 but not delete) 
- **Role**: permissions scoped to a department or function (e.g., IT team vs. marketing team gets different access) 
- Analogy: company ID card = policy (everyone gets one); department access card = role (specific to your team) 
- Best practice: use **IAM users** rather than root for daily work; root acceptable in POC accounts where IAM is restricted 

---

## Pending Confirmation

- Students with **account verification issues** (KYC/DigiLocker not completing): raise a support ticket with AWS if issue persists beyond 2 days 
- Students on POC accounts **not seeing region selector**: expected behavior — limited to one assigned region, no impact on VM creation practicals 
- Public IP not appearing on instances: verify **Auto-assign Public IP** is enabled in network settings during instance creation 

---

## Action Items

- **All students**: Create and terminate at least one Linux and one Windows EC2 instance daily for practice 
- **All students**: Connect to Linux VM via EC2 Instance Connect and to Windows VM via RDP using the 6-step process 
- **Patel**: Write a LinkedIn article with step-by-step screenshots for connecting to Windows VM from Mac using the Windows App 
- **Students with verification issues**: Raise AWS support ticket if account access not resolved within 2 days 
- **New students / those missing Day 1**: Watch the YouTube playlist (Batch 45) for Linux and foundational content before next class 
- **All students**: Review recordings for any missed content; recording link available in the same Zoom join link after ~10–15 minutes post-session 

---

## Upcoming Session (Day 3)

- **Linux configuration** deep dive
- Create **Apache / Nginx web server** on EC2
- Connect using **MobaXterm** or other third-party SSH tools
- **Snapshots and backups** of EBS volumes
- Networking fundamentals scheduled for **Day 7** 
