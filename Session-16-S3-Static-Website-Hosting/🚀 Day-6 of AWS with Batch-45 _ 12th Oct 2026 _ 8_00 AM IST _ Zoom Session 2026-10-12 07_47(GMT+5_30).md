## Key Outcomes

- **S3 Storage Classes** were reviewed, focusing on the frequency of data access (hot vs. cold) to optimize costs .
- **Terraform** was introduced as the Infrastructure as Code (IaC) tool to automate the deployment of a static website on AWS S3 with CloudFront .
- **CloudFront and OAC** (Origin Access Control) were implemented to provide HTTPS security and low-latency content delivery via edge locations .
- **Automation Workflow** was demonstrated using `terraform init`, `plan`, and `apply` to provision resources without manual console configuration .

## S3 Storage Class Analysis

- **Standard**: Used for frequently accessed data, such as active website content or recent log files .
- **Intelligent Tiering**: Automatically monitors access patterns to move data between tiers to save costs .
- **One Zone-IA**: Reduces costs by storing data in a single availability zone, though with lower redundancy .
- **Glacier**: Used for archival data accessed quarterly or annually .
- **Deep Archive**: The cheapest option for data accessed very infrequently (e.g., once every few years) .
- **Lifecycle Policies**: Used to automate the transition of data between these classes (e.g., Standard $\rightarrow$Glacier $\rightarrow$Deep Archive) based on the age of the object .

## Technical Implementation & Automation

- **Infrastructure as Code (IaC)**: The team shifted from manual S3 bucket creation to using Terraform to ensure scalable and repeatable deployments .
- **CloudFront Integration**: Implemented to serve content from **edge locations**, reducing latency and providing a secure HTTPS endpoint .
- **Origin Access Control (OAC)**: Configured to restrict direct public access to the S3 bucket, forcing all traffic through CloudFront for better security .
- **CLI Tools**: Installed and configured **AWS CLI** and **Terraform binary** to manage resources via the command line .

## Troubleshooting & Interview Insights

- **Bucket Naming**: S3 bucket names must be globally unique because they are DNS-compliant .
- **Deletion Limits**: Buckets containing more than 100,000 objects cannot be deleted via the AWS Console UI and require the **AWS CLI** (`aws s3 rm`) .
- **Error 409**: Occurs during Terraform apply when a resource name (like a CDN distribution) already exists globally .
- **Error 403**: Often indicates an account verification issue or insufficient IAM permissions .
- **Region Constraints**: Once a bucket is created in a specific region, the region itself cannot be changed; data must be replicated to a new bucket in a different region .

## Action Items

- **Students**: Create a custom architecture diagram for the static website deployment and present it in the next class .
- **Students**: Practice recording themselves explaining technical concepts for 1 minute daily to improve communication skills .
- **Students**: Review networking basics (LAN, MAN, WAN) and database concepts before the next session .
