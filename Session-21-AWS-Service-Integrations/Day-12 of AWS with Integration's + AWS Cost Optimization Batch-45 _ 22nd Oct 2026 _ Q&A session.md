## Key Outcomes

The session provided a comprehensive introduction to **AWS Lambda** and serverless computing, covering core concepts, pricing, use cases, and hands-on implementation. Students built a functional **image resizing pipeline** using Lambda, S3, SNS, and CloudWatch — all provisioned via **Terraform**. Cost optimization strategies using AWS billing tools were also explored, connecting Lambda's event-driven model to real-world cost savings. 

---

## Decisions Made

- **Lambda** chosen as the primary serverless compute service to teach, due to its event-driven model, cost efficiency, and 200-service integration capability. 
- **Python** selected as the primary runtime language for Lambda functions, identified as the most popular supported language. 
- **Terraform** used for infrastructure provisioning rather than manual console setup, reinforcing DevOps automation principles. 
- **Mumbai / US-East-1** regions used for deployments, consistent with prior class guidance. 
- Students directed to **destroy resources** after the practical to avoid unnecessary charges. 
- Students who had not completed the previous Terraform static website practical were advised they could not proceed with this dependent practical. 

---

## Core Concepts Taught

### What is AWS Lambda?

- **Serverless compute service** that runs code without provisioning or managing underlying infrastructure. 
- The term "serverless" means no physical server setup is required by the user; AWS manages backend servers transparently. 
- Launched in **2014** — the first major serverless computing service globally. 
- Analogy used: Lambda is like a **5-star hotel** — everything is ready-made, but you only book it for an occasion, not 24/7. 

### Key Lambda Facts

- **Pay-per-use:** Billing in **milliseconds** — charged only when code runs, never for idle time. 
- **Free tier:** Up to **1 million requests per month** at no cost. 
- **Auto-scaling:** Scales from **0 to 10,000+ concurrent executions** automatically, with no configuration required. 
- Supports **20+ programming languages**; Python is the most commonly used. 
- Integrates with **200 AWS services**. 
- Runs over **1 trillion executions per month** globally (used by Netflix, Airbnb, Coca-Cola, etc.). 
- Available in **30 regions** across **100 data centers**. 
- **15-minute execution limit** per invocation — not suitable for long-running heavy workloads. 

### When to Use Lambda vs. EC2

- **EC2** is appropriate for continuous, 24/7 workloads; you pay for the full uptime regardless of usage. 
- **Lambda** is optimal for **occasional, event-triggered tasks** — e.g., processing resumes once per day, resizing images on upload. 
- Key interview point: Lambda is for **cost optimization** when a server is only needed for seconds or minutes, not round-the-clock. 
- Migration from EC2 to Lambda is possible when the use case is identified as occasional; Python code logic remains largely the same, but Lambda-optimized configuration is required. 

### How Lambda is Triggered

- Lambda does not run on its own — it requires a **trigger** (event source). 
- Triggers can be configured from **200 AWS services**, including S3, CloudWatch, EventBridge, Alexa, CloudFront, and more. 
- Example trigger types from S3: **PUT, COPY, DELETE, permanent delete** — any S3 event can invoke Lambda. 
- Scheduling is also possible via **CloudWatch / EventBridge** (e.g., run at 8 AM daily). 
- Manual invocation via the AWS console is also available. 

---

## Cost Optimization Module

### Understanding AWS Billing

- Students were walked through the **AWS Billing console** (root user login required). 
- Instructor's own bill reviewed: EC2 charges of **$0.21**, total bill of **$18**, attributed to running EC2, VPC, and Kubernetes resources. 
- First step in cost optimization: **understand your bill** — identify which services are consuming cost. 

### AWS Cost Management Tools (6 Key Tools)

1. **AWS Cost Explorer** — Visualizes and analyzes cost by service and time period; supports forecasting. 
2. **AWS Budgets** — Set spending thresholds; alerts triggered when budget is approached or exceeded. 
    - Analogy: Like setting a ₹2 crore construction budget and getting alerted when spending hits ₹70 lakhs. 
3. **AWS Cost Anomaly Detection** — Detects unexpected spending spikes using ML pattern recognition; sends alerts via SNS/email. 
4. **AWS Compute Optimizer** — Analyzes resource utilization and recommends **right-sizing** (e.g., flags oversized EC2 instances). 
5. **Cost Optimization Hub** — Centralized dashboard aggregating recommendations across all accounts. 
6. **AWS Trusted Advisor** — Reviews architecture for unnecessary or wasteful resource usage; recommends decommissioning unused services. 

### Cost Optimization Principles

- **Discover waste:** Identify idle or oversized resources (e.g., a dedicated GPU that's never used). 
- **Right-sizing:** Match resource size to actual usage — if CPU/memory consumption is 60–80%, consider upsizing; if barely used, downsize. 
- **Pricing models:** Choose between **on-demand, savings plans, reserved instances, or spot instances** based on workload duration. 
- **Networking costs:** NAT Gateway data transfer is **heavily chargeable** — avoid unnecessary cross-region or private-to-private transfers through NAT. 
- **Storage optimization:** Databases, storage, and networking components can all be optimized. 
- **Governance:** Cost decisions should align with audit and governance requirements in enterprise environments. 

---

## Hands-On Project: Image Resizing Pipeline

### Architecture Overview

The pipeline demonstrates Lambda's event-driven model with the following flow: 

**User uploads image → S3 (non-resized bucket) → Lambda triggered → Image resized using Pillow (Python) → Output saved to S3 (resized bucket) → CloudWatch logs event → SNS sends email notification**

### Components Used

|             Service             |                       Role                        |
|---------------------------------|---------------------------------------------------|
| **S3 (Bucket 1 — non-resized)** | Receives original uploaded images                 |
| **S3 (Bucket 2 — resized)**     | Stores processed, reduced-size images             |
| **AWS Lambda (Python)**         | Executes image resizing logic on trigger          |
| **Pillow (Python library)**     | Python image processing library for resizing      |
| **SNS**                         | Sends email notification upon task completion     |
| **CloudWatch**                  | Captures logs, duration, memory usage, and errors |
| **Terraform**                   | Provisions all infrastructure as code             |
| **IAM**                         | Role and policy for Lambda execution permissions  |

### Why Two S3 Buckets?

- **Bucket 1** holds original (large) images and triggers the Lambda function. 
- **Bucket 2** stores the resized output — keeps processed files separate from originals. 
- Storage cost is the same whether files are in one or two buckets; the separation is architectural, not cost-driven. 

### Terraform Provisioning (9 Resources Created)

The `terraform apply` command automatically created: 

1. IAM Role for Lambda
2. IAM Policy attachment
3. Lambda function (Python runtime)
4. Lambda trigger permission
5. S3 Bucket 1 (non-resized)
6. S3 Bucket 2 (resized)
7. S3 event notification integration
8. SNS Topic
9. SNS Subscription

### Practical Steps Followed

1. **Clone repo** from GitHub using `git clone`. 
2. **Create** **`terraform.tfvars`** file with personal values: access key, secret key, name, email address. 
3. Run `terraform init` → backend configured locally. 
4. Run `terraform validate` → syntax check passed. 
5. Run `terraform plan` → preview of 9 resources to be created. 
6. Run `terraform apply` → type `yes` to confirm; all resources created. 
7. **Upload a PNG image** to the non-resized bucket via AWS console. 
8. **Verify output:** Resized image appears in the second bucket (396KB → 223KB confirmed). 
9. **Check CloudWatch logs** for execution details: duration, memory used, billing milliseconds. 
10. **Confirm SNS subscription** via email to receive notifications. 
11. Run `terraform destroy` to clean up all resources. 

### Observed Results

- Image successfully reduced from **396KB to 223KB** after Lambda execution. 
- CloudWatch logs showed: request ID, **bill duration**, memory consumed (~133MB used), initialization time (~569ms). 
- Lambda charges applied only for the execution duration — not for idle time. 

---

## GitHub Diagram Feature (Bonus Tip)

- Replace `github.com` with `gitdiagram.com` in any GitHub repo URL to auto-generate a **visual architecture diagram** of the codebase. 
- Also supports `explain me` and backend file exploration directly in the browser. 
- Works for any GitHub repository. 

---

## Interview Talking Points

### How to Describe This Project in an Interview

Instructor modeled the following narrative structure: 
> 
> *"In my organization, we had an occasional image processing requirement. Instead of keeping a 24/7 EC2 instance running, we migrated the workload to AWS Lambda. We automated the entire infrastructure using Terraform — provisioning two S3 buckets, a Lambda function, SNS topic, and IAM roles. When a user uploads an image to the source bucket, Lambda triggers automatically, resizes the image using Python's Pillow library, saves it to a destination bucket, and sends an email notification via SNS. This resulted in approximately 60–70% cost savings by eliminating idle server charges."*

### Key Interview Phrases

- "We used Lambda for **occasional, event-driven processing** rather than maintaining 24/7 infrastructure." 
- "Lambda **auto-scales from 0 to 10,000+ concurrent executions** without any configuration." 
- "Billing is in **milliseconds** — we only pay when the function runs." 
- "We integrated Lambda with **S3 triggers, SNS notifications, and CloudWatch logging**." 
- "This was a **cost optimization initiative** — we eliminated idle EC2 costs." 
- For cost tools: reference **Cost Explorer, Budgets, Anomaly Detection, Compute Optimizer, Cost Optimization Hub, Trusted Advisor**. 

---

## Troubleshooting & Issues Encountered

- **Capital letters in bucket names:** S3 bucket names must be **lowercase** — students using uppercase characters in their name variable received errors. 
- **403 IAM permission error:** Lambda function creation failed for some students due to insufficient IAM permissions on their access key user — resolved by attaching **AdministratorAccess** policy. 
- **Region restriction (US-East-1):** One student (Mandhir) received a 403 API error in US-East-1 and resolved it by switching regions. 
- **SNS subscription not confirmed:** Students who did not click **"Confirm Subscription"** in their email did not receive notifications — subscription remains in "pending confirmation" state until confirmed. 
- **Wrong email in** **[variables.tf](https://variables.tf)****:** Some students had incorrect email addresses in their Terraform variables file, causing notification failures. 
- **Terraform not set up:** Students who skipped the previous Terraform practical could not complete this dependent exercise. 
- **File format for Lambda trigger:** Only image files (PNG, JPG) trigger the resize function correctly; uploading Word or video files will cause Lambda to fail — logs in CloudWatch will show the error. 

---

## Pending Confirmation

- Recording of the previous **mock interview session** to be uploaded — instructor confirmed it would be available on the same link. 
- Students who did not complete the **static website Terraform practical** need to watch the recording and complete it before attempting this project. 
- Students experiencing **SNS pending confirmation** need to locate the AWS notification email (check spam) and click the confirmation link before re-uploading an image. 

---

## Action Items

- **All students:** Clone the provided GitHub repo, configure `terraform.tfvars` with personal credentials, and complete the image resizing pipeline practical independently. 
- **All students:** Run `terraform destroy` after completing the practical to avoid charges. 
- **All students:** Record a **2-minute spoken summary** of the project in the WhatsApp group to practice interview articulation. 
- **Students missing previous practical:** Watch the Terraform static website recording and complete it as a prerequisite. 
- **Abhinav / students with SNS issues:** Search inbox/spam for "AWS Notification," confirm the subscription link, then re-upload an image to test the full flow. 
- **Sunil:** Attach **AdministratorAccess** to the IAM user being used for Terraform; recreate access key if deleted. 
- **Rajesh:** Join the correct WhatsApp group (Batch 45) via the number shared in session. 

---

## Open Questions

- **Lambda for infra teams (Shankar):** Use case clarified — Lambda can automate backup/restore triggered by tickets or schedules, eliminating the need for a 24/7 server to run cron jobs. 
- **Migrating EC2 workloads to Lambda:** Feasible when the workload is occasional; Python code logic stays the same, but Lambda-specific configuration and testing are required. 
- **SMS notifications via SNS:** Possible but requires OTP verification and subscription approval; organizational-level restrictions apply. 
- **Non-billable AWS services:** Students directed to check `aws.amazon.com/free` and AWS documentation for the full free-tier list; IAM and S3 (within limits) are free, SNS charges ~₹1 per notification. 
- **Monthly AWS project costs:** Vary widely by project — can range from $1 to $10,000+/month; students advised to state their specific project cost confidently in interviews. 
- **Uploading large images via front-end:** Same upload mechanism applies; Lambda handles the processing regardless of how the file reaches S3. 
