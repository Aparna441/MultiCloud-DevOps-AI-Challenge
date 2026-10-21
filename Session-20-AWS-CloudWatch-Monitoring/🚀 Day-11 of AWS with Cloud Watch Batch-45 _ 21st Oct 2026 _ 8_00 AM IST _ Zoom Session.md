## Key Outcomes

The session introduced **Amazon CloudWatch** as AWS's fully managed monitoring service, covering its core purpose, architecture, and hands-on configuration. Students learned why centralized log monitoring is essential, how to configure CloudWatch alarms with **SNS (Simple Notification Service)** for email alerting, how to create billing alarms, and how to build dashboards for multi-server visibility. The session concluded with a Q&A covering resume preparation, CloudWatch vs. CloudTrail distinctions, and billing management strategies.

---

## Decisions Made

- All hands-on practice will be performed in the **Mumbai region** only; creating resources in multiple regions is discouraged to avoid confusion and unexpected billing. 
- **Enhanced/detailed CloudWatch monitoring** must be manually enabled per service (EC2, RDS, etc.) if granular metrics are needed — basic monitoring is on by default but provides less detail. 
- The standard **threshold for CPU alerts** used across most companies is **80%**; alerts should trigger only after the issue persists for an average of **5–15 minutes** to avoid false positives from brief spikes. 
- **Auto-scaling actions** triggered by CloudWatch alarms (e.g., reboot/terminate EC2) exist as options but should **not** be used without manual verification and approval in real-world scenarios. 
- **Friday's session** will be a reverse Knowledge Transfer (KT): students prepare and present topics related to AWS to the group. 

---

## Core Concepts Covered

### Why Monitoring Is Required

- If a server goes down and is terminated, its **local logs are deleted** — without a centralized store, root cause analysis becomes impossible. 
- Managing **100+ servers** individually via SSH to check logs is not feasible; a centralized system is mandatory. 
- **Attackers** who compromise an environment often delete local logs to remove their footprint — centralized logging captures activity before deletion occurs. 
- Storing logs **locally for extended periods** (e.g., 1 year per client SLA) requires very large machines, increases cost, and can slow down the server itself. 
- **SLA compliance**: monitoring enables teams to maintain agreed service uptime thresholds (e.g., 80–90% CPU alert → action taken before SLA breach). 

### How CloudWatch Works

- CloudWatch **collects logs and metrics** from all AWS services (200+) into a centralized location. 
- Data flow: AWS services generate logs → logs stream to CloudWatch → CloudWatch creates **graphs, dashboards, and panels** → thresholds trigger **alarms** → alarms notify via **SNS**. 
- Four core functions of CloudWatch: **Collect → Monitor → Act → Analyze**. 
- CloudWatch is **100% managed by AWS**, eliminating the overhead of maintaining a separate monitoring server (e.g., open-source tools like ELK, Zabbix, Splunk, Grafana, AppDynamics can go down themselves). 
- Real-world example shared: a company moved from self-hosted observability servers to CloudWatch after their own monitoring server crashed, removing the "uptime overhead" of managing the monitoring tool itself. 

### Metrics vs. Logs

- **Metrics**: numeric measurements (CPU %, memory %, network throughput) — visualized as graphs. 
- **Logs**: detailed event records — application activity, errors, messages, and events happening inside a service. 

### CloudWatch vs. CloudTrail

- **CloudWatch**: used for monitoring resources and applications — dashboards, alarms, metrics. 
- **CloudTrail**: auditing and governance service that **records all API activity** — tracks who made changes, for security and compliance purposes. 

---

## Hands-On Demonstrations

### Enabling Enhanced Monitoring on EC2

- Navigate to **EC2 → Launch Instance → Advanced Details → Detailed CloudWatch Monitoring** → enable → extra charges apply. 
- Basic monitoring provides standard metrics; enhanced monitoring provides higher-frequency data collection (as granular as **1-second intervals** vs. standard 30-second or 5-minute intervals). 

### Enabling Monitoring on RDS

- RDS offers **Standard mode** (7-day free metric retention) and **Advanced mode** (up to 24 months, additional charges). 
- Enhanced monitoring for RDS collects data every **8 seconds** by default, configurable down to 1 second. 

### Viewing Metrics from EC2 Instance

- Inside an EC2 instance: **Monitoring tab** → panels auto-generated for CPU, network in/out, disk, credit usage. 
- Click the **three-dot menu → "View in Metrics"** to navigate directly into CloudWatch metrics for that instance. 
- Initial data may take **3–5 minutes** to appear after instance launch. 

### Creating a CPU Alarm

1. From the metrics graph, click the **bell icon** → enters CloudWatch Alarm creation. 
2. Set **threshold**: e.g., CPU utilization ≥ **80%** — a red threshold line appears on the graph. 
3. Set **period**: 5-minute average (standard); alert triggers only if the condition persists for that duration. 
4. Configure **SNS topic**: create a new topic (e.g., `Batch45-CPU-80`) with an **email endpoint**. 
5. After topic creation, AWS sends a **confirmation email** — the subscription must be confirmed before alerts are received. 
6. Once confirmed, the SNS subscription status changes from **"Pending"** to **"Confirmed"** in the SNS console. 

### How Many Alerts Fire? (Class Discussion)

- A brief spike above 80% that immediately drops = **no alert** (does not sustain for the averaging period). 
- A sustained breach above 80% = alerts fire based on the configured period; if the issue persists, alerts continue every 5–15 minutes. 
- Best practice: **3 alerts** for a sustained breach spanning multiple periods; spikes that resolve quickly generate 0 alerts. 

### Creating a Billing Alarm

- Navigate to **CloudWatch → Metrics → Billing → Estimated Charges**. 
- Set currency to **INR or USD** depending on account configuration; set threshold (e.g., ₹250 or $1). 
- Create a new SNS topic with email endpoint → confirm subscription → alarm created. 
- Billing data updates approximately every **6 hours** by default. 
- **SNS charges**: approximately **₹1 per email** notification sent. 

### Dashboards

- From EC2 monitoring tab: **three-dot menu → "Add to Dashboard"** → create or select a dashboard (e.g., `Batch45-Amrut-Dashboard`). 
- All panels (CPU, network, disk) from a machine are added to the single dashboard view. 
- For **multiple servers on one dashboard**: each server appears as a **different color line** on the same panel — no need for separate dashboards per server. 
- For **100 machines**: one dashboard, one alarm per metric type (e.g., one CPU alarm, one memory alarm) — alarms can reference multiple instance IDs. 
- Dashboards can be scoped **per project** (prod vs. dev vs. QA). 

### SNS (Simple Notification Service)

- Fully managed AWS messaging service using a **publisher-subscriber (PubSub)** model. 
- A **topic** is the message channel; **subscriptions** are the endpoints (email, SMS, Teams, etc.) that receive notifications. 
- Subscribers must **opt in** (confirm subscription) — analogous to joining a WhatsApp/email group. 
- Equivalent to **GCP Pub/Sub** in Google Cloud. 
- **NOC (Network Operations Center)** teams are the typical recipients of monitoring alerts — L1 teams monitoring 24/7 for incoming alerts. 

---

## Billing & Cost Management

- Students checked their AWS bills live; findings included:
    - Most students: **$0–$0.09/month**
    - One student (Gopal): **~₹270**
    - One student (Manoj): **~$13**
    - One student (Nithish): **$24** (left Load Balancer + RDS running for 1–2 days) 
- **Key chargeable services**: EC2 compute time, RDS, Load Balancer, ELB — all billed per hour of use. 
- **Non-chargeable items**: Security Groups (free regardless of quantity). 
- **How to read bills**: AWS Billing Dashboard → Bills → filter by month → view cost by service and region. 
- **Free Tier**: some services have free usage limits (e.g., AWS Glue: 40 requests/day free). 

### If Bill Is Unexpectedly High

- Go to **AWS Support → Create a billing support case** → explain you are a student learning AWS, request bill reversal. 
- AWS may automatically reduce or zero out the bill; allow **2–3 days** for processing. 
- Students with free-tier/POC accounts should check all regions for forgotten resources and delete them. 
- For accounts with free credits (e.g., AWS Education Builder): contact AWS support to confirm whether charges are deducted from credits. 

---

## Pending Confirmation

- Whether **INR billing alarms** can be set directly or only USD alarms are available in certain account configurations — observed inconsistency during demo. 
- Nithish's **$24 bill**: needs to verify with AWS Support whether it will be covered by free credits or requires payment. 
- Shubhendu's account: confirm whether **POC account** or main account, and whether to upgrade for additional learning capabilities. 

---

## Open Questions

- **How to add multiple VMs to a single alarm**: use **tags** (dev/QA/prod) or add individual **instance IDs** directly in the alarm configuration's add/edit option. 
- **Can CloudWatch automatically trigger an alert when CPU > 80%?** Yes — CloudWatch triggers the alarm → SNS delivers the notification → team takes manual action (or Lambda can automate further). 
- **How to test if the alerting setup is working**: create a low-threshold test alarm (e.g., 20% CPU) or artificially generate load to push CPU above the configured threshold. 
- **Custom/imported dashboard panels (like Grafana)**: CloudWatch supports customization but has fewer import options than Grafana, which was purpose-built for panel imports. 
- **Mono-repo to multiple Azure DevOps repositories**: must clone locally, then `git init` + `git add` + `git commit` + push each folder individually to its own remote repository. 

---

## Action Items

- **All students**: Delete any resources created during today's session (EC2 instances, alarms, SNS topics) to avoid unnecessary charges; recreate as needed for practice. 
- **All students**: Check all AWS regions for forgotten running resources and delete them. 
- **Students with high bills**: Raise an AWS Support billing case requesting reversal, citing student/learning status. 
- **Nithish**: Contact AWS Support to clarify whether the $24 October bill is covered by free credits. 
- **All students**: Prepare a topic related to AWS for **Friday's reverse KT session** — include a short practical demo if possible; submit before 8:00 AM Monday. 
- **Vikas**: Conduct upcoming sessions on **AWS Lambda** (next class), **Prometheus + Grafana** (manual install and configuration), and **cost optimization tips and tricks**. 
- **Vikas**: Deliver a dedicated session on **resume points and networking** (committed during session). 

---

## Upcoming Sessions

|  Session   |                            Topic                             |
|------------|--------------------------------------------------------------|
| Next class | AWS Lambda — automations and integrations with CloudWatch    |
| Upcoming   | Prometheus + Grafana — manual installation and configuration |
| Upcoming   | Cost optimization tips and tricks                            |
| Upcoming   | Resume points and networking discussion                      |
| Friday     | Reverse KT — students present AWS topics to the group        |
