## Key Outcomes

The session covered GitHub repository management, continuous integration (CI) concepts, and hands-on Jenkins installation on cloud VMs. Students explored GitHub features including branch protection rules, webhooks, and POLLSCM, then connected these concepts to a CI pipeline workflow using Jenkins. A job opportunity for a Senior SRE role (50–65 LPA, 100% remote) was also announced at the start of the session.

---

## Decisions Made

- **Jenkins** selected as the primary CI/CD tool for upcoming sessions due to its open-source nature, 2,300+ plugins, cross-platform UI consistency, and strong community support 
- **Next full week** dedicated entirely to Jenkins — pipeline setup, job configuration, CI/CD implementation 
- **Tomorrow's session** designated as Linux revision + Reverse KT (students must bring 2–3 new commands or concepts to share) 
- **Friday** scheduled for AWS revision + Reverse KT 
- Students advised to use **GCP** as the primary cloud platform to start with, with AWS as an alternative 
- Students instructed to commit notes and learning content to personal GitHub repos daily to build green contribution activity 

---

## Job Opportunity Announced

- **Role:** Senior SRE (Site Reliability Engineer) 
- **Budget:** 50–65 LPA, **100% remote** 
- **Client:** Undisclosed at this stage; name to be revealed after first interview round 
- **Minimum experience required:** ~10 years genuine experience (background verification will be conducted) 
- **Key skills required:**
    - Monitoring management: **Grafana, Prometheus, Loki, OpenTelemetry** 
    - **AWS:** CloudWatch, IAM, VPC, EKS, ECS 
    - **Terraform**, CICD ownership, production environment experience 
    - Observability/monitoring setup — at least 1–2 tools sufficient 
- **Apply by:** 28 October, 4 PM — send resume directly to the provided Skills Capital email 
- Eligible candidates (Batch 44, 45, 46) should share email ID with Vikas after applying 
- Candidates with 6 years of experience confirmed **not eligible** — 10 years minimum strictly required 

---

## GitHub Features Covered

### Repository Types & Visibility

- **Public repos** are accessible by anyone; **private repos** restrict access 
- Visibility can be toggled via Settings → Change Repository Visibility; MFA verification required to make private 
- Demo performed live: repo made private → collaborator (Nitesh) added → repo made public again 

### Collaborators & Access Control

- Adding collaborators: Settings → Collaborators → Add People → send invitation via email 
- Collaborator receives email invite; access granted upon acceptance 
- Teams can also be created and users added in bulk; AD integration also possible 
- **Level of access** for collaborators is configurable based on rules — not all collaborators get full settings access 

### Repository Settings & Features

- **Features toggleable:** Issues, Wiki, Projects, Sponsorship, Social Preview — enabling/disabling changes what tabs appear in the repo UI 
- **Issues tab:** Functions as an internal Jira — bug tracking, enhancement requests, support tickets, labels, milestones, assignees 
- **Pull Requests:** Mechanism to merge feature branch changes into main/master branch after approval 
- **Wiki:** Documentation similar to Confluence; supports multiple pages but has more limitations than dedicated tools 
- **Projects tab:** Workspace/project-related information and reporting 
- **Security tab:** Code scanning, vulnerability policies, [security.md](https://security.md) file, advisory management — third-party tools like SonarQube preferred in practice 
- **Insights tab:** Activity metrics — PR counts, open/closed issues, top contributors, merge stats, commit history 
- **Webhooks:** Connect GitHub to external CI/CD tools (e.g., Jenkins) to trigger pipelines on code push 
- **Transfer Ownership / Archive / Delete:** Available in Danger Zone settings; deletion is irreversible 

### Branch Protection Rules

- Accessible via Settings → Branches → Add Classic Rules 
- **Key rules demonstrated:**
    - **Required pull request before merging** — code cannot go to main without PR approval 
    - **Required status checks** — automated checks must pass before merge 
    - **Branch must be up-to-date** before merging — prevents merge conflicts 
    - **Required conversation resolution** — all review comments must be resolved 
    - **Required signed commits** — verified signatures enforced 
    - **Required deployment to succeed** before merging — build/test must pass first 
    - **Lock branch** — makes branch read-only 
    - **Allow force push** — enables `git push -f` to forcefully override; rarely recommended 
    - **Allow deletion** — controls whether branch can be deleted 
- In real companies, **not everyone can push directly to main** — branch protection rules enforce this 
- Rule sets are a one-time setup activity per project 

### Git Commands Referenced

- `git revert <commit-id>` — reverts to a previous version 
- `git push -f` (force push) — forcefully pushes even when errors occur; use with caution 
- `git reset --hard` — resets everything, ignores errors; workaround for stubborn revert failures 
- `git pull` before pushing — recommended to avoid push rejection errors 
- `.gitignore` — used on local machine to exclude files (credentials, API keys, build folders, binary dependencies) from being tracked or pushed to GitHub 

### GitHub Agents / Actions

- GitHub has built-in **agents/runners** for CI automation but they are **not free beyond a limit** — companies rarely use them due to heavy charges 
- **Jenkins** (open-source, free) used instead for build/compile/test automation 

### GitHub Contribution Activity

- Green squares on GitHub profile = commit activity — visible to recruiters and background verifiers 
- **Recommendation:** Minimum **10–15 commits per month** — students advised to fork Vikas's notes repo and make daily edits to build green activity 

---

## Continuous Integration (CI) — Concepts

### Definition

CI is the practice of automatically triggering **build, compilation, and unit testing** every time a developer pushes code to a shared repository (GitHub), with success/failure feedback sent directly to the developer via email. 

### How GitHub Connects to Jenkins

Two primary integration mechanisms: 

- **Webhooks** (~20–30% of companies) — GitHub notifies Jenkins on code push to trigger pipeline
- **POLLSCM** (~70% of companies) — Jenkins periodically polls the GitHub repository for changes; preferred in most real-world setups 

### CI vs. Agile

- In **Agile**, integration was the boundary — testing was separate
- In **DevOps CI**, unit testing is also automated within the pipeline itself — no manual build engineer needed 

### Traditional vs. CI Approach

- **Traditional:** A dedicated person (e.g., "Arthav") manually ran builds at fixed times (e.g., midnight) — developers waited 12–24 hours for feedback 
- **CI:** As soon as code is pushed, build + test runs automatically within minutes 

### Benefits of CI

- **Improved quality** — code tested 10–20 times per day automatically 
- **Increased productivity** — no waiting for manual builds; feedback in 5–15 minutes 
- **Reduced risk** — automated unit tests catch errors consistently; human error eliminated from build process 

### Popular CI Tools

Jenkins, GitLab CI, GitHub Actions, Azure Pipelines, Bamboo, TeamCity, Travis CI, AWS CodePipeline, Bitbucket Pipelines, CodeMagic 

- **Jenkins** used by ~50%+ of companies in job descriptions 
- Concepts (pipelines, steps, stages) are **the same across all tools** — Jenkins chosen for its ubiquity 

---

## Jenkins — Why It's Popular

Key reasons (interview-ready answer): 

- **Open-source and free** — no licensing cost; preferred for open-source project requirements
- **2,300+ community plugins** — integrates with virtually any tool (Git, Maven, Gradle, Selenium, SonarQube, Docker, Kubernetes, Jira, etc.) 
- **Framework, not just a tool** — fully customizable; custom plugins can be written 
- **Cross-platform** — runs on Windows, Linux, macOS with identical UI 
- **Lightweight and reliable** — fast, trustworthy for production use
- **Strong community support** — thousands of contributors maintaining plugins
- **Scripted builds** — supports both UI-based and code-based pipeline definitions 
- Can function as a **cron server replacement** 

---

## Jenkins Installation — Hands-On

### Prerequisites

- Ubuntu VM (24.04 LTS recommended) with **30 GB disk** 
- Minimum hardware per official docs: sufficient CPU/RAM for team size; Java 21+ required 
- VM created on GCP (Compute Engine → VM Instances → Create Instance) 

### Installation Steps (Official Jenkins Docs)

1. `sudo apt update` — update packages 
2. Install Java: `sudo apt install openjdk-21-jre` — Jenkins is Java-based 
3. Add Jenkins GPG key to keyring 
4. Add Jenkins repository to `/etc/apt/sources.list` so Ubuntu knows where to download Jenkins 
5. `sudo apt update` again 
6. `sudo apt install jenkins` — installs Jenkins (~54 MB) 
7. Verify: `jenkins --version` 

### Accessing Jenkins UI

- Default port: **8080** 
- Navigate to `http://<VM-public-IP>:8080` 
- First-time setup requires **initial admin password** from: `cat /var/lib/jenkins/secrets/initialAdminPassword` 
- Select **"Install Suggested Plugins"** (~80 plugins installed automatically) 
- Set admin credentials → Jenkins dashboard accessible 

### Common Issues & Fixes

|                     Issue                      |                                                Fix                                                 |
|------------------------------------------------|----------------------------------------------------------------------------------------------------|
| UI not loading                                 | Ensure `http://` not `https://`; check firewall/port 8080                                          |
| Port 8080 blocked                              | GCP: Firewall → Edit HTTP rule → Allow all ports; AWS: Security Group → Add inbound rule TCP 8080  |
| Jenkins service not running                    | Run `sudo systemctl start jenkins`; check with `systemctl status jenkins`                          |
| Permission denied on cat command               | Run `sudo -i` to become root user first                                                            |
| VM stopped → Jenkins stopped                   | Manually restart with `sudo systemctl start jenkins` after VM restart                              |
| Organization GCP account firewall restrictions | Use individual account instead                                                                     |

### Verifying Port Listening (Troubleshooting Tip)

- Use `ss` (networking command) to check if port 8080 is listening on the server before investigating firewall — best practice before jumping to VM settings 

### VM Management

- Stop VM when not in use to avoid charges; Jenkins will stop with VM 
- Delete VM after session; recreate fresh for next class using saved Cloud Shell commands 
- GCP Cloud Shell command saved for quick VM recreation each day 

---

## Pending Confirmation

- Client name for the Senior SRE job opportunity — to be disclosed after first interview round 
- Mock interview schedule — confirmed for upcoming session (Wednesday mentioned as prep day) 

---

## Action Items

- **All eligible students (10+ years exp):** Apply to Senior SRE role at Skills Capital email by **28 October, 4 PM**; share email ID with Vikas after applying 
- **All students:** Install Jenkins on a cloud VM and explore the dashboard before next class 
- **All students:** Create/maintain a GitHub repo and make daily commits to build green contribution activity; minimum 10–15 commits/month 
- **All students:** Prepare 2–3 Linux commands or new concepts for **tomorrow's Reverse KT session** 
- **Students without GCP account:** Watch GCP account creation video in the LMS (available in course materials) and create account; 90-day free trial available 
- **Vikas:** Send WhatsApp message with job details, classroom link, and session reminder 
- **Students on LinkedIn leaderboard:** Update profile name and LinkedIn URL in the leaderboard form; post daily on LinkedIn, sync every Friday 

---

## Open Questions

- How to design multi-branch deployment pipelines in GitHub (feature → UAT → main → production) with branch protection rules preventing direct publishing from feature branches — discussed conceptually; full implementation to be covered in Jenkins sessions 
- Kubernetes architecture deep-dive (API server flow when applying deployment YAML, multi-region deployments using node affinity/selectors) — raised by a student post-interview; flagged for upcoming Kubernetes class 
- DevSecOps vs. DevOps pipeline differences — briefly addressed: security tools added on top of existing DevOps pipeline; transition happens after DevOps is stable 
