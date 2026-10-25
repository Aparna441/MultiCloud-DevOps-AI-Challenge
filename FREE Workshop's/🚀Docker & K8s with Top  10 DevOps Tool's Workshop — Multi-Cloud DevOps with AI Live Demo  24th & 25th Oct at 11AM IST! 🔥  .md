## Key Outcomes

A 4+ hour live interactive session covering Docker fundamentals and a Kubernetes introduction was conducted by Vikas (14 years IT experience, currently at PWC) for approximately 143+ participants. Participants created GCP accounts, provisioned VMs, installed Docker, ran containers, and created a 3-node Kubernetes cluster on Google Cloud. The session concluded with a course enrollment pitch for a 2.5-month Multi-Cloud + DevOps with AI program.

---

## Announcements

- Session divided into two parts: **Part 1 — Docker** (~60 min), **Part 2 — Kubernetes/container orchestration** (~60 min); total ~2 hours. 
- Recording would be provided to participants who posted a LinkedIn feedback post and tagged the instructor; delivery target was by 6 PM same day. 
- A **WhatsApp group** was created for screenshots, troubleshooting, and course communication. 
- Next batch starts **tomorrow (Monday) at 8:00 AM IST** — Day 1 of DevOps (Git/GitHub/Jenkins). 
- Instructor confirmed: **no weekend batches**; missed sessions covered by recordings delivered within 2 hours of class end. 

---

## Topics Covered

### Docker Architecture & Core Concepts

- **Docker definition:** Open-source platform to build, ship, and run containers; a containerizing tool that moves applications across environments. 
- **Three architectural components:**
    - **Client** — where Docker commands are executed (terminal) 
    - **Docker Host / Daemon** — background service running on the VM/node that manages Docker operations 
    - **Registry (Docker Hub)** — centralized image storage; images pushed here and pulled when needed 
- **Workflow demonstrated live** using `docker run hello-world`: 
    1. Client sends command to daemon
    2. Daemon checks locally for image; not found → pulls from Docker Hub
    3. Daemon creates container and streams output to client
- **Dockerfile** = recipe/blueprint of an application (OS, dependencies, run instructions, location) 
- **Docker image** = packaged artifact built from Dockerfile (`docker build -t <name> .`) 
- **Container** = running instance of an image (`docker run <image-name>`) 
- **Docker Hub** = default public registry; alternatives include **ECR** (AWS Elastic Container Registry), **GCR** (Google Container Registry), **ACR** (Azure Container Registry), **Nexus** 
- Images can also be archived to S3 using `docker save` → tar file → `aws s3 cp` 

### Key Docker Commands Covered

|               Command               |                     Purpose                     |
|-------------------------------------|-------------------------------------------------|
| `docker pull <image>`               | Download image only; no container created       |
| `docker run <image>`                | Pull (if needed) + create and start container   |
| `docker build -t <name> .`          | Build image from Dockerfile                     |
| `docker images` / `docker image ls` | List local images                               |
| `docker ps`                         | List running containers                         |
| `docker ps -a`                      | List all containers including exited            |
| `docker commit`                     | Create image from a modified container          |
| `docker save`                       | Archive image to tar file                       |
| `apt-get update`                    | Update packages (best practice before install)  |
| `sudo apt install docker.io -y`     | Install Docker on Ubuntu                        |
| `docker -v`                         | Check installed Docker version (29.1.3 shown)   |

- **Best practice emphasized:** Always run `apt-get update` before `apt install docker.io` to ensure packages are current — a key interview differentiator. 
- **Container lifecycle states:** Running → Exited (task complete) → Stopped; `stop`, `start`, `restart` commands available. 
- **`pull`** **vs** **`run`** **distinction:** `pull` only downloads the image; `run` downloads if needed AND creates a container. 
- **`build`** **vs** **`push`****:** `build` creates the image from Dockerfile; `push` uploads it to a registry. 

### Practical: Docker on Google Cloud VM

- VM created on **GCP Compute Engine** (Ubuntu 24.04 LTS, 35GB storage) named "25 October Docker Day 1". 
- **Three ways to create a VM** discussed: UI/Console, CLI (gcloud/CloudShell), Terraform/REST API. 
- **Equivalent code** feature in GCP generates CLI commands automatically from UI configuration — also available in AWS. 
- Connected to VM via **SSH through browser** (port 22, automatic key transfer). 
- Docker installed, version confirmed as **29.1.3**. 
- `docker run hello-world` executed; container ran in **527 milliseconds** — cited as a potential interview question. 

### Kubernetes Introduction

- **Why Kubernetes:** When containers scale to hundreds/thousands, manual management via Docker commands becomes impossible; a single VM failure takes down all containers on it. 
- **Kubernetes = container orchestration tool** — automates management, scaling, and high availability of containers. 
- **Cluster** = group of nodes (VMs with Docker/Kubernetes components installed); typically 3–5+ nodes. 
- **Node vs VM distinction:** A VM becomes a "node" when Kubernetes components are installed on it. 
- **High availability:** With a 5-node cluster, one node going down does not affect the application. 
- **Docker vs Kubernetes comparison:**
    - Docker: build images, ship containers, run containers (1–20 containers manageable) 
    - Kubernetes: orchestrate, automate, manage at scale (100s–1000s of containers) 

### Kubernetes Practical: GCP GKE Cluster

- **GKE (Google Kubernetes Engine)** used; cluster created in ~3 clicks: 
    1. Click Create → Configure
    2. Set node pool (3 nodes, smaller VM size, 30GB disk)
    3. Select region (US-Central), click Create
- Cluster creation took ~3 minutes; **3 VMs automatically visible** in Compute Engine VM Instances. 
- **Operating system for Kubernetes nodes:** **COS (Container Optimized OS)** — not Ubuntu, not Windows; purpose-built for containers. 
- **Connect to cluster command (GCP):** `gcloud container clusters get-credentials <cluster-name>` → generates kubeconfig entry. 
- **Check nodes:** `kubectl get nodes` — showed 3 worker nodes. 
- **Deploy Jenkins on Kubernetes:** `kubectl run jenkins --image=jenkins/jenkins` → pod created in ~59 seconds. 
- **Expose Jenkins externally:** Via Kubernetes Service (Load Balancer type, port 8080) — required because Kubernetes pods are internal by default. 
- **`kubectl describe pod jenkins`** used to inspect pod details and IP. 
- **Kubeconfig** stores cluster connectivity information (endpoint, credentials, context); used to switch between clusters. 

### End-to-End DevOps Workflow Explained

1. Developer writes code → compiles, builds, runs unit tests → **artifact** created 
2. Artifact copied into **Dockerfile**; DevOps engineer writes YAML manifest 
3. `docker build` → **image** created 
4. Image pushed to registry (Docker Hub / ECR / GCR / Nexus) 
5. YAML manifest applied to **Kubernetes cluster** via `kubectl` 
6. Application runs as **pods** in cluster 
- DevOps engineer's scope: everything except writing application code — infrastructure, containerization, CI/CD automation. 

---

## Q&A Highlights

- **Q: What is LTS?** → A: Long-Term Support — stable version; always use LTS in production. 
- **Q: What is** **`apt-get update`** **doing?** → A: Updates package metadata from Ubuntu sources; minor version upgrades; equivalent to mobile software updates (patching activity). 
- **Q: Difference between VM and node?** → A: A VM becomes a node when software (e.g., Docker, Kubernetes components) is installed on it. 
- **Q: Can you create an image from a running container?** → A: Yes, using `docker commit` command. 
- **Q: Why Load Balancer service type in Kubernetes?** → A: To expose the application to the internet so users from different locations (US, UK, etc.) can access it. 
- **Q: Which OS for Kubernetes cluster?** → A: COS (Container Optimized OS) — interview answer; Windows is possible but costly and not standard. 
- **Q: Pull vs Run?** → A: `pull` downloads image only; `run` pulls if needed and creates a container. 
- **Q: How to connect to Kubernetes cluster in production?** → A: Update kubeconfig using cloud-specific command (e.g., `gcloud container clusters get-credentials` for GCP, `aws eks update-kubeconfig` for AWS); concept is the same across clouds, commands differ. 
- **Q: Where to save Docker images cheaply instead of Docker Hub?** → A: Save to S3 using `docker save` → tar → `aws s3 cp`. 

---

## Career & Job Search Advice

### Resume & Profile Strategy

- Use phrase **"In my company, we use Docker for..."** rather than referencing YouTube or courses — sounds genuine. 
- Narrow technology profiles (e.g., Pega-only) are harder to place; combine with cloud (e.g., "Pega running on AWS EC2/Kubernetes") to broaden appeal. 
- For 90-day notice periods: tell HRs you are on bench and can join in 30 days — bench managers often release faster. 
- Create **multiple profiles on Naukri** with different technology emphasis (e.g., Azure DevOps on one, AWS DevOps on another) to increase call volume. 

### Job Portal Filters

- Platforms mentioned: **Naukri, LinkedIn, Indeed, Glassdoor, Internshala, TopHire, Flexi Jobs**. 
- LinkedIn filters recommended: **Under 10 applicants**, **Past 24 hours**, **Easy Apply** — targets fresh postings before competition grows. 
- **Walk-in interviews** recommended for same-day offers; physically attending shows commitment and proximity. 
- For jobless candidates: relocate to nearest metro city (Bangalore, Pune) and attend walk-ins rather than waiting at home. 

### Package Expectations Discussed

- Cloud/DevOps engineer roles on Naukri showing **₹77K–₹1.25 LPA range** (Bangalore market example shown). 
- Instructor's view: candidates with genuine skills do receive calls and offers at these levels. 
- For experienced professionals switching to DevOps: minimum expected package should match years of experience (e.g., 14-year professional → minimum ₹14 LPA). 
- Confidence building: practice speaking technical concepts aloud; record WhatsApp voice messages explaining Docker architecture. 

---

## Course Enrollment Details

### Program Structure (45th Batch — ongoing)

- **Duration:** 2.5 months, **55 live sessions** (Monday–Friday, 8:00–9:15 AM IST) 
- **55 Q&A sessions** + **8 mock interviews** + resume building included 
- **Recording access:** Delivered within 2 hours of session end; valid for 1,000 days 
- **Previous batch (44) recordings** included free of cost for new enrollees 
- **Internship certificate:** 6 months; available to enrolled students 

### Curriculum Roadmap

- **Days 1–10:** Git, GitHub, Jenkins (CI/CD fundamentals) 
- **Days 11+:** Docker, Kubernetes, Ansible, Terraform 
- **Projects (10 total):**
    1. CI/CD pipeline project (Jenkins + Docker + GitHub) 
    2. Microservice-based application on Kubernetes (Java/Python/Go/.NET) 
    3. Terraform project on AWS 
    4. Docker CI/CD project 
    5. DevSecOps project 
    6. Azure DevOps project (Boards, Pipelines, Repos, Artifacts) 
    7. Multi-Cloud project (AWS + GCP + Azure, 20 services) 
8–10. AI/MLOps/AIOps, MCP Server, Agentic AI projects 
- **Additional topics:** Python & Shell scripting automation (5 practicals), resume building, extra Azure sessions, Generative AI/Copilot integration 

### Pricing (Session Offer)

- Special offer during session: **₹29,000** (promo code applied, ~20% discount) 
- Offer closed at end of session; standard price reverted to **₹34,000 + GST (~₹39,000)** 
- Payment via UPI (instructor's number: 9993445100) or website; EMI option available (₹15K upfront + ₹15K later) 
- Confirmed enrollees during session: Praveen, Avishek (Debroy), Avishek Kumar, Amit Kumar, Srinivasan, and others 

### Instructor Background

- **14 years IT experience**; currently at **PwC (PricewaterhouseCoopers)** 
- ~12 AWS/GCP certifications at architect level; AI/MLOps certifications 
- Speaker at **CNCF 2024**, Microsoft events; judge at hackathons 
- Offices in **Bangalore (BTM, Vijayanagar)** and **Indore**; remote-first 
- Community: **CloudDevOpsHub** — 70,000+ WhatsApp group members 

---

## Action Items

- **All participants:** Post LinkedIn feedback tagging instructor (`@Vikas Satnawat` + `@CloudDevOpsHub`) with session photo to receive recording by 6 PM. 
- **All participants:** Join WhatsApp group (link shared in Zoom chat) for notes, recordings, and support. 
- **GCP account pending participants (Ankush, Akash, Gopal):** Make ₹500–₹1,000 prepayment to Google to complete account verification. 
- **Payment error participants (Dharmveer, others):** Change payment method or enable international transactions on bank account. 
- **Enrolled students:** Send payment screenshot to instructor's WhatsApp (9993445100); register on LMS website to receive batch access. 
- **All participants:** Run `docker run hello-world` and `kubectl get nodes` on own GCP VM/cluster and send screenshots to WhatsApp group. 
- **Vikas:** Deliver session recording to LinkedIn posters by 6 PM; add enrolled students to batch WhatsApp group and LMS. 

## Next Session

- **Tomorrow (Monday), 8:00–9:15 AM IST** — DevOps Day 1: Git, GitHub, Jenkins fundamentals. 
