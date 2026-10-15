## Key Outcomes

The session provided a comprehensive, hands-on introduction to Amazon VPC, covering its architecture, core components, and underlying networking concepts. Participants created VPCs, subnets, Internet Gateways, NAT Gateways, and VPC peering connections live in the AWS console. Key networking fundamentals — OSI model layers, TCP/UDP, IPv4/IPv6, proxy types, and CIDR notation — were discussed as foundational context before diving into AWS-specific implementation. 

---

## Decisions Made

- **Production environment protection:** Mumbai region designated as production; all practice and experimentation must be done in **N. Virginia** to avoid accidental disruption. 
- **VPC components are chargeable; VPCs themselves are not:** The default VPC incurs no cost, but components like **Elastic IPs** and **NAT Gateways** do — participants advised to use the default VPC for learning where possible. 
- **CIDR /16 chosen as the standard VPC size** for real-world projects, yielding 65,536 available IP addresses (2^16); subnets further divided using /20 (4,096 IPs) as a practical partition size. 
- **Manual vs. automated VPC creation:** Both approaches demonstrated — step-by-step manual creation and the "VPC and more" wizard that auto-generates subnets, route tables, NAT gateways, and endpoints in one click. 
- **VPC peering requires non-overlapping CIDRs:** A failed peering attempt between two VPCs with the same CIDR range illustrated that overlapping IP spaces prevent peering; migration or IP reassignment is the only resolution. 
- **Transitive routing is not supported in VPC peering:** If VPC-A peers with VPC-B and VPC-B peers with VPC-C, VPC-A cannot reach VPC-C through VPC-B; direct peering or Transit Gateway is required. 
- **One-directional peering acceptance:** A peering connection accepted by one side does not automatically allow reverse communication — a separate peering must be created for bidirectional access. 

---

## Core Concepts Covered

### OSI Model & Networking Fundamentals

- **Seven OSI layers** reviewed with practical relevance: Application (Layer 7) and Transport (Layer 4) are most critical for DevOps practitioners. 
    - **Layer 7 (Application):** Handles request/response cycles — e.g., a browser requesting a website.
    - **Layer 6 (Presentation):** Encryption/decryption, TLS/SSL.
    - **Layer 5 (Session):** Establishes time-bound connections (e.g., a Zoom session).
    - **Layer 4 (Transport):** TCP/UDP data transfer.
    - **Layer 3 (Network):** Large file transfers broken into packets.
    - **Layer 2 (Data Link):** Ethernet/LAN connections between routers.
    - **Layer 1 (Physical):** Physical cable/hardware connections. 

### IPv4 vs. IPv6

- **IPv4:** 32-bit, written in decimal (e.g., `192.168.1.16`), range 0–255 per octet; address space is nearly exhausted. 
- **IPv6:** 128-bit, written in hexadecimal; created to solve IPv4 exhaustion — analogous to mobile number series expanding from 9→8→7→6 as each series fills up. 
- Most laptops carry **both IPv4 and IPv6** simultaneously, confirmed by checking Wi-Fi network settings. 
- IPv6 in production is **very rare** currently; most company servers still run IPv4. 

### TCP vs. UDP

- **TCP (Transmission Control Protocol):**
    - Connection-oriented; uses **three-way handshake** (SYN → SYN-ACK → ACK). 
    - Guarantees delivery with acknowledgements; gracefully shuts down after transmission.
    - Used for dynamic websites, persistent connections, and larger DNS queries. 
- **UDP (User Datagram Protocol):**
    - Stateless; **send-and-forget** — no acknowledgement packets.
    - Used for video streaming, heavy media transfers, and standard DNS queries where speed matters more than guaranteed delivery. 

### Proxy, Peer-to-Peer, Broadcast, Unicast

- **Forward Proxy:** Sits on the client side; used to restrict outbound access (e.g., blocking YouTube in a corporate network). 
- **Reverse Proxy:** Sits on the server side; controls and filters incoming traffic — **NGINX** is a common reverse proxy implementation. 
- **Peer-to-Peer:** Direct one-to-one connection between two hosts without intermediary. 
- **Broadcast:** One message sent to all recipients simultaneously. 
- **Unicast:** One-to-one targeted communication. 

### CIDR Notation & Subnet Math

- **Formula:** Available IPs = 2^(32 − prefix length)
    - `/16` → 2^16 = **65,536 IPs**
    - `/20` → 2^12 = **4,096 IPs**
    - `/24` → 2^8 = **256 IPs** 
- CIDR website `cidr.xyz` demonstrated live to visualize IP counts per prefix. 
- **IP address classes** (private ranges):
    - Class A: `10.x.x.x` — default CIDR /8
    - Class B: `172.16.x.x` — default CIDR /16
    - Class C: `192.168.x.x` — default CIDR /24 

---

## VPC Architecture & Components

### VPC (Virtual Private Cloud)

- A **private, isolated network** within AWS where you define IP ranges, security rules, and resource placement. 
- Analogy used: purchasing a plot of land and deciding how to partition it into public areas (hall, garden, parking) and private areas (bedrooms). 
- Default VPC exists in every AWS account and region; up to **5 VPCs** per region; multiple VPCs supported for different projects or environments. 

### Subnets

- **Partitions** of the VPC's IP space into smaller logical units. 
- **Public Subnet:** Resources accessible from the internet (e.g., EC2 application servers).
- **Private Subnet:** Resources with no direct internet access (e.g., RDS databases). 
- AWS allows **one public subnet per Availability Zone**; selecting 2 AZs yields 2 public subnets automatically. 
- Subnet size example: a /16 VPC split into /20 subnets gives ~4,096 IPs per subnet, with the remainder available for additional partitions. 

### Internet Gateway (IGW)

- Enables **internet connectivity** for the VPC; required for any public-facing resources. 
- Created independently and then **attached to a VPC** via Actions → Attach to VPC. 
- **One IGW per VPC** — a single IGW cannot be shared across multiple VPCs. 
- **Egress-Only Internet Gateway:** Used exclusively for **IPv6 outbound** connections; does not allow inbound IPv6 traffic. 

### NAT Gateway

- Allows resources in **private subnets to initiate outbound connections** to the internet (e.g., downloading OS patches for RDS) without exposing them to inbound internet traffic. 
- Must be placed in the **public subnet**; private subnet route tables point to it for internet-bound traffic. 
- Two types: 
    - **Public NAT Gateway:** Private subnet → internet.
    - **Private NAT Gateway:** Private subnet → other VPCs or on-premises networks.
- **Zonal vs. Regional scope:** Zonal NAT Gateway serves a single AZ; Regional serves the entire region — selection based on architecture requirements. 

### Route Tables

- Define **which traffic goes where** within the VPC — equivalent to **IP tables in Linux**. 
- Each subnet is associated with a route table that specifies allowed paths (e.g., private subnet → NAT Gateway → internet). 
- Linux IP tables historically used for: (1) firewall/traffic filtering, and (2) manual traffic redirection between VMs during maintenance — AWS route tables serve the analogous routing function. 

### VPC Endpoints

- Enable **private connectivity between VPC resources and AWS services** (e.g., S3) without traversing the public internet or requiring a NAT Gateway. 
- Use case: RDS backups exported directly to S3 privately via an **S3 Gateway Endpoint**. 
- Reduces cost and latency compared to routing through NAT Gateway for AWS service communication. 
- **Chargeable** component — treat all VPC components as billable. 

### DNS Settings in VPC

- **DNS Hostnames:** Assigns human-readable hostnames to VPC resources in addition to IP addresses. 
- **DNS Resolution:** Maps hostnames to IP addresses in DNS records, enabling name-based access to resources. 
- Both settings must be enabled together; disabling resolution means hostnames exist but cannot be resolved to IPs. 

---

## VPC Peering

### How It Works

- Connects **two VPCs** so resources can communicate using private IP addresses — works across accounts and regions. 
- **Requester** sends a peering request; **Acceptor** must explicitly accept it — pending until accepted. 
- File transfer between peered VPCs uses **SCP (Secure Copy Protocol)** over SSH for private IP communication. 

### Key Constraints

- **Overlapping CIDR blocks prevent peering** — if both VPCs use the same IP range (e.g., `10.0.0.0/16`), the peering request fails; resolution requires migrating resources to a new IP range. 
- **No transitive routing:** VPC-A ↔ VPC-B and VPC-B ↔ VPC-C does not allow VPC-A ↔ VPC-C; each pair needs its own peering connection. 
- **Peering is unidirectional by acceptance:** OM→Default peering being active does not mean Default→OM traffic is automatically allowed without a separate accepted connection. 

### Cross-Account Peering (Live Demo)

- Vikas created a peering request from his account targeting a participant's (Sunil's) VPC in a different account and region. 
- First attempt failed due to **CIDR overlap**; second attempt with a non-overlapping CIDR succeeded and was accepted by Sunil. 

---

## Database Access via Jump Host

- **RDS lives in the private subnet** — not directly accessible from the internet or developer laptops. 
- Standard access pattern: developer → **Jump Host (Bastion)** → RDS endpoint using tools like **MySQL Workbench** or **phpMyAdmin**. 
- For non-technical stakeholders needing read-only access: create a **read-only database user** and provide credentials via the jump host or a Windows Jump Host with GUI tools installed. 
- Alternative for fully private setups: **Windows Jump Host** with database GUI software installed, accessible via VDI in client environments. 
- Exposing RDS publicly is technically possible with strict security group restrictions (source IP whitelisting), but is **not a best practice** for production. 

---

## Cleanup Procedure

Participants instructed to delete resources in the correct dependency order to avoid errors: 

1. Delete **NAT Gateway** first (Actions → Delete NAT Gateway).
2. Wait for NAT Gateway deletion to complete (~30 seconds).
3. Delete **VPC Peering Connections** (Actions → Delete Peering).
4. Delete the **VPC** (Actions → Delete VPC).
5. Check for and release any **Elastic IPs** — these are chargeable even when unattached. 
- Default VPC: **do not delete**; no charges incurred for the VPC itself.

---

## Interview Questions Highlighted

|    Topic     |                      Question / Concept                       |
|--------------|---------------------------------------------------------------|
| VPC          | What is a VPC? Define in one line.                            |
| IGW          | What is an Internet Gateway and when do you need one?         |
| NAT Gateway  | What does NAT Gateway enable for private subnets?             |
| CIDR         | How do you calculate available IPs from a CIDR prefix?        |
| IPv4 vs IPv6 | Differences and why IPv6 was introduced?                      |
| TCP vs UDP   | Three-way handshake, use cases for each?                      |
| Proxy        | Forward proxy vs. reverse proxy — use cases?                  |
| VPC Peering  | Does it work across accounts and regions? Transitive routing? |
| VPC Endpoint | When would you use a VPC S3 endpoint instead of NAT Gateway?  |
| Route Table  | Linux equivalent? (IP tables)                                 |
| Subnets      | Definition and purpose of public vs. private subnets?         |

---

## Pending Confirmation

- **Monday vs. Tuesday next class:** Participants debated; final decision leaned toward **Monday**, but confirmation pending from batch coordinator. 
- **Vault/secrets management:** Will be covered in the **Kubernetes secrets session**, not this module. 
- **Route 53 / DNS practical session:** Requested by participants; Vikas indicated domain registration via GoDaddy would be needed as a prerequisite. 
- **VPC Lattice and Transit Gateway:** Overview requested; Vikas acknowledged the topic but deferred detailed coverage to a future session. 
- **Session 11 Q&A format:** Participant (Shubham) requested a structured question-and-answer document from the session; Vikas confirmed the AI-generated notes follow discussion content and committed to maintaining the existing format. 

---

## Action Items

- **All participants:** Switch AWS practice region to **N. Virginia** (not Mumbai) before any hands-on work. 
- **All participants:** Delete any VPC components created during the session (NAT Gateway → Peering → VPC → Elastic IPs) to avoid charges. 
- **All participants:** Read the VPC definitions and CIDR formulas shared via WhatsApp; spend time on the linked resources before the next class. 
- **All participants:** Record a **1–2 minute self-introduction video** and share in the WhatsApp group — daily practice required to build communication skills for interviews. 
- **Interested participants (Pune-based or remote):** Apply to the **Happiest Minds AWS DevOps Engineer** opening (8+ years experience, AWS CDK with TypeScript required) — send CV to Vikas for forwarding. 
- **Vikas:** Share full job description details in the **Batch 45 WhatsApp group**. 
- **Shubham:** Submit the auto-scaling + load balancer integration point as a structured note/PR to the shared repository for Vikas to verify and publish. 
