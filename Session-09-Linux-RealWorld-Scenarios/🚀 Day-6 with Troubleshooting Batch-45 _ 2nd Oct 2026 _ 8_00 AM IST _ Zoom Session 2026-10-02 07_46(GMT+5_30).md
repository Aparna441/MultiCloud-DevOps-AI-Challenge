## Concept

- **Linux Troubleshooting** is the systematic process of identifying, diagnosing, and resolving issues on Linux servers in real-time production environments, covering logs, performance, disk, services, permissions, hardware, and network problems. 
- A **DevOps engineer** must be capable of independent troubleshooting — even when L1/L2 support is unavailable — because unresolved issues escalate to senior engineers who are expected to fix them. 
- **System logs** are the primary source of truth when any issue occurs; the thumb rule is: *if something goes wrong, check the logs first*. 
- **Shell scripting** allows repetitive diagnostic commands to be automated into a single executable file, reducing manual effort and enabling scheduled execution via **cron jobs**. 
- **Service management** in Linux is handled via `systemctl`, which controls the lifecycle (start, stop, restart, status) of any installed service such as NGINX, SSH, Docker, Kubernetes, Ansible, etc. 
- **Graceful vs. Forceful process termination**: `kill -15` (SIGTERM) waits for the process to finish cleanly before stopping; `kill -9` (SIGKILL) forces immediate termination regardless of state. 
- **Swap memory** is virtual memory allocated from disk space, used when RAM is insufficient or to offload inactive memory pages — analogous to Windows' page file / hibernate feature. 

---

## What I Understood

### Google Cloud VM Creation and Configuration

- The session was conducted live on YouTube and used **Google Cloud (GCP)** as the hands-on environment; students were instructed to navigate to `console.cloud.google.com` and bookmark it as their cloud homepage. 
- A new VM named **"Day 6"** was created using the **Create Instance** workflow in Compute Engine > VM Instances. 
- **Real-time production server configuration** discussed: up to **16 cores, 16 GB RAM, 512 GB SSD** — though for critical workloads with thousands of users, 32 GB RAM is appropriate; large-scale storage is offloaded to third-party object storage rather than stored on VMs. 
- **Operating system choice**: Ubuntu **24.04 LTS** was selected over 26.04 because production servers predominantly still run on 22.04 or 24.04; a planned upgrade to 26.04 is a valid interview answer ("upgrade planned in next 3 months, discussion ongoing with client"). 
- **Auto-deletion feature**: Under Machine Configuration > VM Provisioning, a time limit can be set so the VM is automatically deleted or stopped after a defined period — useful for learning environments to avoid unexpected billing. 
- **CLI VM creation**: The `gcloud compute instances create` command was demonstrated in **Cloud Shell**, with parameters for name, zone, machine type, and project. Remembering four key lines of this command is sufficient for interview purposes. 
- **Equivalent instance type names across clouds** for 16 core / 16 GB RAM: 
    - **AWS**: `C6i.4xlarge` (C-series)
    - **GCP**: `N2-standard-16`
    - **Azure**: `Standard_F16s_v2`
- **IP types on a VM**: Internal IP = private IP used within the same VPC; External IP = public IP accessible over the internet. 
- **SSH connectivity options** in GCP: browser-based SSH, custom port, SSH key (public/private), Cloud Shell command, or third-party SSH client — all use port 22. 

---

### Log Checking — Scenario 1

- All system logs are stored under **`/var/log/`**; navigate there with `cd /var/log` and list contents with `ls`. 
- Key log files and their purposes: 
    - **auth.log** — authentication-related events (login attempts, sudo usage)
    - **kern.log** — kernel/OS-level messages, hardware-OS interface events
    - **dpkg.log** — package installation and removal history (package manager)
    - **dmesg** — boot-time messages, hardware detection during startup
    - **syslog / messages** — general system messages
    - **secure** — secure access logs
    - **fail.log** — failed login attempts
- **Commands for reading logs**: 
    - `more <filename>` — reads file page by page (slow, sequential)
    - `tail -10 <filename>` — shows last 10 lines only
    - `tail -f <filename>` — **follows** the file in real-time as new logs are written (`-f` = follow); used when a developer is running something and you need to monitor live output
    - `cat <filename> | grep error` — filters for specific keywords like `error`, `warning`, `failed`, `closed`, `exception`
- Application logs (e.g., Jenkins, NGINX) may also appear under `/var/log/<appname>/` — same commands apply, same concept. 
- **Logging tools** used in real-time environments: **Splunk** (most common), **Kibana**, **Dynatrace**, **Datadog** — these provide centralized, searchable log dashboards without manual CLI access. 

---

### Performance Troubleshooting — Scenario 2

- **Key commands for performance analysis**: 
    - `top` — real-time view of CPU usage, memory usage, load average, running tasks
    - `htop` — enhanced GUI-style version of top (must be installed: `apt install htop`); shows color-coded CPU/memory bars
    - `free -h` — memory usage in human-readable format
    - `df -h` — disk space usage per partition in human-readable format
    - `du` — disk usage of specific directories
    - `ps -ef` or `ps -aux` — list all running processes with details
    - `uptime` — system uptime and load average
    - `who` / `w` — who is currently logged in, how many users
- **Monitoring Shell Script** was built live, combining these commands: 
    - Created with `vi` editor; started with `#!/bin/bash` (shebang line)
    - Used `echo` statements as section headers for readability
    - Commands included: `free -h`, `df -h`, `du`, `uptime`
    - Saved and given execute permission with `chmod +x <filename>` (by default Linux does NOT grant execute permission for security reasons) 
    - Executed with `./scriptname.sh`
- **Production-level script enhancements** (generated with ChatGPT prompt): 
    - Added hostname and date/time for context
    - Colorized output — e.g., if disk usage exceeds **80% threshold**, display warning in red
    - Conditional logic: `if disk_usage > threshold → show warning`
    - Could be extended to **send email alerts** via SMTP configuration
- **Scheduling with cron jobs**: The monitoring script can be scheduled to run at regular intervals using `crontab`, enabling automated recurring health checks. 
- **Historical performance data**: For trend analysis (CPU/memory over weeks or months), data must be fed into a **continuous monitoring system** (e.g., Grafana, Datadog) rather than relying on real-time commands alone. 

---

### Disk Troubleshooting — Scenario 3

- **Windows disk tools**: Disk Defragmentation (built-in), `chkdsk` (Check Disk for errors), Disk Management (`compmgmt.msc`) for viewing partitions. 
- **Linux disk commands**: 
    - `df -h` — view partition sizes, used/free space; the root (`/`) partition is equivalent to Windows' C: drive
    - `lsblk` — list block devices; identify unconnected or unattempted disks
    - `smartctl` — tool from the `smartmontools` package; checks SSD/HDD health, temperature, error counts, and persistent device data 
- **Disk fragmentation** in Linux: Linux filesystems (ext4, etc.) handle fragmentation differently from Windows; Windows defragmentation compresses and reorders blocks; Linux rarely needs manual defragmentation. 
- **Block vs. Disk**: A disk contains blocks; blocks are the smallest units where data is physically stored; file systems manage how blocks are allocated. 
- **Partitions**: A single physical disk can be divided into multiple partitions (e.g., EFI, recovery, main OS partition); each partition appears as a separate mountpoint in Linux. 

---

### Service Troubleshooting — Scenario 4 (NGINX Example)

- **NGINX** was installed live: `apt-get update` → `apt install nginx` 
- Verified it was running by accessing the VM's external IP in a browser — default NGINX welcome page confirmed the service was active. 
- **Service management commands** (applicable to any service — NGINX, SSH, Docker, Kubernetes, Ansible, Tomcat, Apache, Java apps): 
    - `systemctl status nginx` — check if service is running, stopped, or failed; also shows the log path
    - `systemctl start nginx` — start the service
    - `systemctl stop nginx` — stop the service
    - `systemctl restart nginx` — restart
    - `systemctl enable nginx` — enable service to start on boot
    - `journalctl -u <service>` — view all logs for a specific service unit
- **Finding application logs**: When you run `systemctl status <service>`, the output shows the binary path and log location — this is the "smart answer" for finding service-specific logs in an interview. 
- **NGINX log location**: `/var/log/nginx/` — contains `access.log` and `error.log`; monitored in real-time with `tail -f access.log`. 
- **HTTP status codes** briefly discussed: 
    - `409` — Conflict (single server overloaded with too many requests)
    - `404` — Service/resource not found
    - `Connection refused` — service may have been killed (OOM kill, manual kill, crash)
- **Scaling solutions for overloaded servers**: Horizontal scaling (add more VMs) + **load balancing** (distribute traffic across VMs using AWS Elastic Load Balancer, GCP Load Balancer, or NGINX configured as a load balancer) + **High Availability (HA)** setup. 

---

### Permission Issues — Scenario 5

- **File permissions** in Linux are not automatically granted — by default, even the file owner cannot execute a newly created file (security design). 
- **CHMOD** (Change Mode) to grant permissions: 
    - `chmod +x <file>` — add execute permission
    - `chmod 744 <file>` — owner has full permissions (rwx), group and others have read-only
    - `chmod 777 <file>` — full permissions for everyone (use with caution)
- **CHOWN** (Change Ownership) — used to change file/directory ownership when permission issues relate to the wrong owner. 
- Checking permissions: `ls -l` — shows permission string, owner, group; executable files appear highlighted in green in many terminals. 

---

### Process Killing — Scenario 6

- Use `ps -ef` or `top` to find the **PID (Process ID)** of the problematic process. 
- **Kill signals**: 
    - `kill -15 <PID>` or plain `kill <PID>` — **graceful termination** (SIGTERM): waits for the process to complete its current task before stopping; preferred in production
    - `kill -9 <PID>` — **forceful termination** (SIGKILL): immediately kills the process regardless of state; use only when graceful kill fails or when the process is consuming resources abnormally without justification
    - `kill -11` — also mentioned as a signal option
- **Interview language tip**: Use the word **"graceful"** (not just "normal") when describing `kill -15` to sound professional. 
- **Thread dumps**: For Java-based applications consuming high CPU/memory, take a thread dump and hand it to the developer for analysis — DevOps engineers typically cannot fix application-level thread issues directly. 
- **When NOT to kill**: In production, never kill a process without approval or analysis; first check logs, analyze historical data, determine if the high usage is expected (e.g., end-of-day batch reports, monthly data extraction jobs). 

---

### Hardware Troubleshooting — Scenario 7

- **Common hardware issues in Linux**: 
    - **Kernel panic** — most common hardware-related error; often caused by OS/firmware mismatch after an update, or running an incompatible OS on old hardware
    - Disconnected cables or failed disk mounts
    - Fan issues, temperature problems
    - Driver/firmware mismatch (common in legacy systems)
- **Diagnostic commands**: 
    - `dmesg` — kernel ring buffer; shows hardware detection messages, boot errors, device connection/disconnection events
    - `lsblk` — list block devices; identifies unconnected or failed disks
    - `lscpu` — CPU information; can reveal multi-CPU configuration issues
    - `smartctl` — disk health and temperature (requires `smartmontools`)
- **Key principle**: Even in cloud environments, understanding hardware troubleshooting is essential for interviews — cloud abstracts hardware but interviewers still ask about it. 

---

### Network Troubleshooting — Brief Coverage

- **Telnet**: Used to verify port connectivity — `telnet <hostname> <port>` checks if a specific port on a destination is open and reachable. 
- **SSH service**: If SSH is not running, remote connections fail; check with `systemctl status sshd`; SSH configuration (allow/disallow users, keys) is managed in `/etc/ssh/sshd_config`. 
- **Wireshark / tcpdump**: Network packet capture tools — `tcpdump` captures packets via CLI, Wireshark analyzes them visually; used to diagnose network latency, packet drops, and timeout issues. 
- **SSH disconnection causes**: Most commonly network latency or packet drops (timeout); unlike Zoom which auto-reconnects, SSH shell sessions break and must be manually reconnected. 
- **Swap memory** (network context): Not network-related, but swap is virtual memory from disk — analogous to Windows hibernate/page file; useful when RAM is insufficient for active application data. 

---

### Weekend Challenge & Interview Prep

- **Bandit Game** (OverTheWire): A Linux command-line game with ~33–34 levels; students were challenged to complete as many levels as possible over the weekend and explain their solutions to the class. 
    - Level 0: SSH into the server using provided credentials (`bandit0` user, `bandit0` password) — login command was provided in Zoom chat
    - Completing and explaining levels builds real troubleshooting muscle memory
- **Resume points derived from this session**: 
    - Linux server configuration and troubleshooting in real-time with DevOps best practices
    - Installed and configured Ubuntu Server 24.04 on Google Cloud (console and CLI)
    - Performed user management, group permissions, secure access (public/private key, CHMOD)
    - Managed services: NGINX, SSH, and others
    - Built health check scripts (CPU, memory, disk, network, hardware monitoring)
    - Performed process analysis and termination; troubleshot disk and service issues
- **AI Ops preview**: A future class will automate all manual troubleshooting steps using AI Ops tools — the manual approach taught today is the foundation. 

---

## What I Didn't Fully Get

- **`journalctl -u <service>`** was mentioned briefly but not deeply demonstrated — this command streams the full log history of a specific systemd service and is extremely useful for debugging service failures. You can add `-f` to follow it live, or `--since "1 hour ago"` to filter by time.
- **`kill -11`** **(SIGSEGV)** was mentioned but not explained clearly — this is a segmentation fault signal, typically sent when a process accesses invalid memory. It is not commonly used to manually kill processes; it usually appears as an error signal from the OS itself.
- **OpenTelemetry** was briefly discussed at the end in the context of an SRE interview question — it is an observability framework that combines **metrics, logs, and traces** into a unified standard. Tools like Grafana, Prometheus, and Jaeger can consume OpenTelemetry data. It is particularly relevant for SRE/production support roles. 
- **VPC connectivity between two VMs** was raised as a question but deferred to Day 19 — the concept is that two VMs in different VPCs need **VPC peering** or a shared network configuration to communicate; this will be covered practically later. 
- **Horizontal vs. Vertical scaling use cases** were touched on but not fully resolved — vertical scaling (increasing CPU/RAM on the same VM) requires stopping the VM in GCP/AWS; VMware supports **hot-plugging** (adding RAM without stopping), but GCP/AWS use cold-plug only. 
- **`systemctl`** **vs.** **`journalctl`**: `systemctl` manages service state (start/stop/enable); `journalctl` reads logs from the systemd journal. Both are part of the `systemd` ecosystem and complement each other. 
- **Disk fragmentation in Linux** was glossed over — Linux filesystems like ext4 are designed to minimize fragmentation; tools like `e4defrag` exist but are rarely needed. The `smartctl` tool is more relevant for disk health monitoring.

---

## Might Show Up on the Exam

- **Log file location**: `/var/log/` is the standard directory for all system and application logs on Linux 
- **`tail -f`** **vs** **`tail -n`**: `-f` follows live updates; `-n` (or `-<number>`) shows last N lines — know the difference 
- **`chmod +x`** **is required** before executing any shell script — Linux does not grant execute permission by default 
- **Shebang line**: Every shell script must start with `#!/bin/bash` to specify the interpreter 
- **`systemctl status <service>`** reveals both service state AND the log path — useful in troubleshooting interviews 
- **`kill -9`** **= forceful (SIGKILL),** **`kill -15`** **= graceful (SIGTERM)** — use "graceful" terminology in interviews 
- **Swap memory** = virtual memory from disk; used when RAM is full or to offload cold pages — equivalent to Windows page file 
- **Instance type naming conventions** across clouds: AWS uses `C6i.4xlarge` style; GCP uses `N2-standard-16`; Azure uses `Standard_F16s_v2` 
- **`df -h`** = disk space by partition; **`free -h`** = RAM/swap usage; **`top`****/****`htop`** = live CPU/process view — all standard performance commands 
- **Telnet** checks port connectivity (`telnet <host> <port>`); SSH is for secure login — they serve different purposes 
- **Auto-delete VM setting** in GCP: Machine Configuration > VM Provisioning > Set time limit — VM must be stopped before this setting can be edited 
- **Single project rule**: Always create VMs within one GCP project; creating multiple projects leads to billing confusion and resource fragmentation 
- **NGINX** can act as: web server, load balancer, reverse proxy, streaming server, or messaging server — know which role it plays in your use case 
- **80% disk threshold** is the standard warning threshold for production monitoring scripts; alerts should trigger at or above this level 
