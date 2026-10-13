## Key Outcomes

The session provided a comprehensive introduction to **AWS Relational Database Service (RDS)** and general database concepts, emphasizing the transition from manual VM-based installations to **Platform as a Service (PaaS)** . Students learned to provision **PostgreSQL** and **MySQL** instances using the AWS Console, focusing on security best practices such as keeping databases in **private subnets** and using **jump hosts** (bastion servers) for access .

## Database Fundamentals

- **Relational vs. Non-Relational**: Relational databases use rows, columns, and predefined schemas (SQL), while non-relational databases (NoSQL) handle unstructured data, often using JSON formats or key-value pairs .
- **Popular Engines**:
    - **MySQL**: Open-source, widely used for e-commerce and accounting systems .
    - **Oracle**: Enterprise-grade, expensive, and preferred for banking due to high security and support .
    - **MongoDB**: Ideal for evolving schemas and gaming sessions .
    - **Cassandra**: Designed for high-volume read/write operations and IoT data .
- **Time Series Data**: Specialized databases (e.g., **Prometheus**) are used for monitoring data that changes every second .

## AWS RDS Technical Details

- **PaaS Advantage**: RDS automates hardware provisioning, OS patching, backups, and failure detection, reducing the need for dedicated DBA teams .
- **Amazon Aurora**: An enterprise-level AWS database that is 5x faster than standard MySQL, supports up to 128 TB of storage, and replicates data across three availability zones .
- **Configuration Highlights**:
    - **Default Port**: MySQL uses port **3306** .
    - **Scaling**: Supports **vertical scaling** (increasing CPU/Memory) via a single click in the console .
    - **Connectivity**: Databases should be set to **Public Access: No** to prevent SQL injections and unauthorized internet access .
    - **Maintenance**: Backups and maintenance windows should be scheduled during non-business hours to minimize impact .

## Practical Implementation

- **Provisioning Process**: The instructor demonstrated creating databases using **Full Configuration** and **Free Tier** settings .
- **Authentication**: Support for both standard database passwords and **IAM database authentication** .
- **Endpoints**: RDS provides a **hostname/endpoint** rather than a direct IP address to connect applications to the database .

## Action Items

- **All Students**: Record a summary of what was learned in the session to build communication confidence for interviews .
- **Students with Terraform deployments**: Run `terraform destroy` to avoid unnecessary AWS billing .
- **Students with 403 errors**: Raise a support ticket with AWS to verify account status .
