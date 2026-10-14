## Key Outcomes

The session focused on designing and implementing an AWS RDS architecture using MySQL, covering the full workflow from architecture diagramming to live database creation, connectivity via a jump host (EC2), and running SQL queries. Students created a VPC-based architecture with public and private subnets, deployed an EC2 jump server, connected to a private MySQL RDS instance via endpoint, and executed core SQL commands. The session concluded with guidance on how to frame this hands-on work as professional database administration experience for resumes and interviews.

---

## Decisions Made

- **[draw.io](https://draw.io)** selected as the recommended tool for creating real-time cloud architecture diagrams, based on participant experience. 
- **MySQL 8.4** chosen as the RDS engine version for the session; version 8.x confirmed as the standard production answer. 
- **Free Tier** template selected for learning; **Sandbox** offered as an alternative for students without free tier access. 
- **Auto-generated password** used via AWS self-managed credential option; students instructed to copy credentials immediately as they cannot be recovered later. 
- **Automated connectivity** (not manual) selected when linking EC2 to RDS, to avoid manual subnet, routing, and NAT gateway configuration errors. 
- **Existing security group** should be selected (not "create new") during automated RDS-EC2 connectivity setup. 
- **20 GB storage** allocated for the EC2 jump server to support query execution and software installation. 
- **Ubuntu 24.04 LTS** (referred to as 26.04 in session) selected as the default OS for the EC2 instance. 
- **PPK key pair** created (named "RDS key") for third-party tool (PuTTY) connectivity. 

---

## Architecture Design

### VPC Architecture ([draw.io](https://draw.io))

- **VPC** as the outer boundary containing two subnets: 
    - **Public Subnet**: Hosts the EC2 jump server (Jump Host Server), connected to the internet via an **Internet Gateway (IGW)**
    - **Private Subnet**: Hosts the MySQL RDS instance — not directly internet-accessible
- **NAT Gateway**: Required in the public subnet to enable private subnet outbound connectivity; automatically configured when using AWS automated setup. 
- **Client/User** sits outside the VPC and connects via internet → IGW → EC2 → RDS (port **3306**). 
- Architecture analogy: public subnet = hall/dining area; private subnet = private bedroom — same concept of segmenting a house. 

### Why a Jump Host?

- RDS is in a private subnet and cannot be accessed directly from the internet. 
- The EC2 instance acts as a **mediator** — users SSH into the jump server first, then connect to RDS from there. 
- Named **Jump Host Server** / **Bastion Server** in architecture. 

### Production-Level Architecture Elements Discussed

- **Load Balancer** (Classic/ALB) in the public subnet distributes traffic to EC2/application servers. 
- **Auto Scaling Group** increases EC2 instances when traffic grows. 
- **RDS Standby** (secondary database) for disaster recovery — data synced/replicated in real time. 
- **S3 Bucket** stores application logs, database dumps, and audit data (not row/column data — that stays in RDS). 
- **CloudWatch** receives all RDS and EC2 logs for monitoring; basic monitoring enabled by default on AWS services. 
- **IAM** for access control; **CloudTrail** for auditing. 
- **Snapshot backups** stored separately; point-in-time recovery available. 

---

## RDS Configuration Walkthrough

### Database Creation Steps

1. Navigate to AWS Console → **RDS** → **Databases** → **Create Database** → **Full Configuration**. 
2. Engine: **MySQL 8.4**; Template: **Free Tier** (or Sandbox). 
3. Master username: **admin** (used by ~95% of companies). 
4. Password: **Auto-generated** by AWS; copy immediately from "View Credential Details" pop-up. 
5. Storage: **20 GB** (production standard cited as **512 GB** with auto-scaling enabled). 
6. **Connect to EC2 instance**: Select "Connect to EC2 Compute Resource" → choose the jump server instance → set to **Automated** setup. 
7. Public access: **Blocked** — only private connections allowed. 
8. Monitoring: **CloudWatch** integration enabled; logs sent automatically. 

### RDS Tiers Explained

|    Tier    |       Instances       |               Use Case                |
|------------|-----------------------|---------------------------------------|
| Free Tier  | 1 (read + write)      | Learning/testing                      |
| Dev/Test   | 2 (primary + standby) | Development with basic DR             |
| Production | 3+ (multi-AZ)         | High availability, negligible failure |

### Amazon Aurora vs Standard RDS

- **Aurora** = AWS enterprise-managed MySQL/PostgreSQL; 5× throughput vs standard MySQL. 
- Up to **128 TB** auto-scaling on SSD storage. 
- **6-way replication**, up to **15 read replicas**. 
- Automatic **failover detection** — hardware failures handled transparently; no manual intervention. 
- Multi-AZ: primary + multiple standbys; if one AZ fails, traffic shifts automatically. 
- Higher cost than standard RDS; not recommended unless enterprise features are needed. 

---

## EC2 Jump Server Setup

### Instance Configuration

- **Name**: Jump Host Server 
- **OS**: Ubuntu 24.04 LTS (64-bit) 
- **Key pair**: PPK format (RDS key) for PuTTY connectivity 
- **Storage**: 20 GB 
- **Traffic**: Allow SSH (port 22) inbound 

### Connection Commands (run in EC2 terminal)

```bash
# Step 1: Become root user
sudo -i

# Step 2: Update package list
sudo apt update

# Step 3: Upgrade installed packages
sudo apt upgrade -y

# Step 4: Install MySQL client
sudo apt install mysql-client

# Step 5: Connect to RDS
mysql -h <RDS_ENDPOINT> -u admin -p
# Do NOT add a space after -p; enter password at prompt
```
