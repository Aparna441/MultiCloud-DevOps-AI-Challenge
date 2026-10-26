## Key Outcomes

This session marked the first day of the DevOps module, transitioning from prior Linux and AWS training into Git and GitHub. Students installed Git Bash, created GitHub accounts, and successfully pushed code from a local machine to a remote GitHub repository using core Git commands. The session covered version control theory, the distinction between Git and GitHub, and practical workflows including cloning, forking, staging, committing, and pushing code. 

---

## Decisions Made

- **Git Bash** is the primary tool for all Git operations; **Git GUI** was demonstrated but deemed unnecessary since it only works on Windows and is not used in real-world workflows. 
- **GitHub Desktop** (~700 MB) was explicitly ruled out for use — it slows down code management and adds unnecessary complexity. 
- All practical labs must be performed on **personal laptops only**, not office laptops, due to potential security and vulnerability concerns (risk of exposing company code). 
- **Command-line interface (CLI)** is the standard for all Git operations going forward; GUI-based approaches will not be taught. 
- **GitLab** was noted as more popular and better than GitHub Actions for CI/CD; GitHub Actions and GitLab will be covered in later sessions. 

---

## Core Concepts Covered

### Version Control

- **Version control** maintains different snapshots of files/code over time, enabling tracking, rollback, and collaboration. 
- Demonstrated with a Google Sheets example: each edit is timestamped, and any prior version can be restored with one click. 
- Key benefits explained:
    - **Tracking**: Know who changed what and when. 
    - **Collaboration**: Multiple developers can work in parallel from different locations. 
    - **Centralized management**: Code pushed to a central repo is safe even if a developer's laptop fails or they go on leave. 
    - **Comparison**: Diff between versions (who added/deleted what) is visible per commit. 
    - **Revert**: A single `git revert` command restores a previous version (e.g., client rejects v2 and wants v1 back). 

### Git vs. GitHub

|    Aspect    |                Git                 |            GitHub             |
|--------------|------------------------------------|-------------------------------|
| Type         | Open-source software               | Cloud-hosted web service      |
| Installation | Local machine (developer's laptop) | No install; account-based     |
| Interface    | CLI (Git Bash)                     | GUI (browser)                 |
| Purpose      | Local version tracking             | Centralized remote repository |
| Ownership    | Open source                        | Purchased by **Microsoft**    |

- Git is used by an estimated **97% of developers/companies** worldwide. 
- GitHub alternatives include **GitLab** and **Bitbucket** — same Git concepts apply, only the UI/tool changes. 
- Git and GitHub communicate via **authentication/authorization mechanisms** such as SSH keys. 

### What is a Commit?

- A **commit** is the act of finalizing and saving code changes as a tracked snapshot. 
- Each commit receives a unique **hexadecimal commit ID** (tracking number) that records what changed, who changed it, and when. 
- Demonstrated on the VLC open-source GitHub repo: green `+` lines = additions; red `-` lines = deletions, all tracked per commit ID. 

### Staging Area

- The **staging area** is a waiting zone between the working directory and the remote GitHub repo — a buffer before pushing. 
- Analogy used: like an airplane on the runway preparing for takeoff — positioned and ready before launch. 
- Files must be explicitly **added** to the staging area with `git add` before they can be committed; Git won't track files it hasn't been told about. 
- The staging area allows developers to **reset wrong files or code** before the final push. 
- Cannot be bypassed — it is a mandatory part of Git's architecture. 

### Clone vs. Fork

- **Clone**: Downloads a full, fresh copy of a repository from GitHub to a local machine. Does **not** require a GitHub account. 
- **Fork**: Copies a repository **cloud-to-cloud** (GitHub account to GitHub account); requires a GitHub account. The forked copy appears under the user's own profile. 
- If the **parent (upstream) repo changes**, the forked child repo receives a notification/option to merge those changes — it does not sync automatically without user action. 
- Demonstrated live: a student (Mithun) forked the instructor's "Multicloud + DevOps with AI Challenge" repo, which already had **295 forks**. 

### README and Markdown Files

- The **[README.md](https://readme.md)** is the first file in a repository; it describes the project — how to run it, what it does, licensing, platform info, contribution guidelines. 
- **Markdown (.md)** files render formatted text (colors, headings, lists) in the GitHub UI; the raw file contains plain markdown syntax. 
- VLC's GitHub repo was used as a live example: README contains open-source license info, platform info, and contribution guidelines. 

---

## Tools Installed & Setup

### Git Installation

- **Windows**: Download ~63 MB installer from [git-scm.com](https://git-scm.com) (64-bit for Intel; ARM 64-bit for ARM processors). 
    - During setup: enable "Add Git Bash to Windows profile"; optionally add desktop icon; **disable daily auto-updates** to avoid constant update prompts. 
- **Mac**: Run `brew install git` in terminal. 
- **Linux**: Run `apt-get install git`. 
- After installation, two tools appear: **Git Bash** (primary — use this) and Git GUI (not recommended). 

### GitHub Account Creation

- Navigate to **[github.com](https://github.com)** → Sign Up → Continue with Google → choose a Google account. 
- Choose a **username** that reflects your real name (e.g., `mithun-ariya`); avoid unprofessional names as companies may request your GitHub profile. 
- Usernames **cannot be renamed** after creation. 
- Students were encouraged to update their profile with a photo and bio. 

### Visual Studio Code

- **VS Code** (~237 MB) is the recommended code editor for opening cloned repositories locally. 
- After installation, right-clicking a folder shows "Open with Code." 
- **Browser shortcut**: Replace `.com` with `.dev` in a GitHub repo URL to open it directly in a browser-based VS Code editor — useful for quick edits without local setup, but not recommended for daily use. 

---

## Git Commands Practiced

Seven core commands were covered and executed live: 

|              Command              |                                                Purpose                                                 |
|-----------------------------------|--------------------------------------------------------------------------------------------------------|
| `echo "# repo-name" >> README.md` | Creates a README.md https://readme.md file with initial content using Linux redirect                   |
| `git init`                        | Initializes Git in the local directory; creates a hidden `.git` folder for tracking                    |
| `git status`                      | Shows tracked/untracked files; untracked files appear in **red**, staged files in **green**            |
| `git add *` (or filename)         | Adds all files (wildcard `*`) or a specific file to the staging area                                   |
| `git commit -m "message"`         | Commits staged changes with a descriptive message; generates a commit ID                               |
| `git branch -M main`              | Renames/switches the current branch to `main` (GitHub uses `main`; local Git may default to `master`)  |
| `git remote add origin <URL>`     | Sets the remote GitHub repository address (the "destination address" for pushes)                       |
| `git push -u origin main`         | Pushes committed code from local machine to the remote GitHub repo                                     |

- **`git clone <URL>`**: Downloads an entire repository to the local machine. 
- **`git config --global user.name`** **/** **`user.email`**: Sets global identity so GitHub knows who is pushing code — required on first use. 
- **`git config --global --list`**: Verifies that global username and email are configured. 

---

## Common Errors & Resolutions

- **"Author identity unknown" on commit**: Global username/email not configured. Fix: run `git config --global user.name "YourName"` and `git config --global user.email "your@email.com"`, then retry. 
- **403 error on push**: Local Git not authenticated with GitHub. Fix: run the `git remote add origin` command, then push again; a browser pop-up will appear for sign-in verification. 
- **"Remote origin already exists"**: The remote was set previously (possibly incorrectly). Fix: delete the folder, start fresh, and re-run all commands from `git init`. 
- **"Source reference does not match / failed to push"**: Branch name mismatch (local is `master`, remote expects `main`). Fix: run `git branch -M main` before pushing. 
- **Wrong credentials cached (Windows)**: Go to Control Panel → User Accounts → Credential Manager → Windows Credentials → delete the old GitHub entry, then re-authenticate. 
- **Office laptop login conflicts**: If multiple GitHub accounts are cached, switch to the correct account or use a personal laptop exclusively for these labs. 

---

## Live Demonstrations

- **Version history in Google Sheets**: Showed how different dates of edits are tracked and restorable — used as an analogy for Git version control. 
- **VLC GitHub repo**: Explored a real open-source project — 761 contributors, code written 58.9% in C, commit history with per-line diff tracking, and contributor activity. 
- **Full push workflow**: Instructor created a new folder (`26 October`), opened Git Bash, created a README and a Java "Hello World" file, ran all 7 commands, and successfully pushed to GitHub. 
- **Fork demo**: Student Mithun live-forked the challenge repo; the copied repo appeared instantly in his GitHub account. 
- **Student success**: Multiple students shared their GitHub repo URLs confirming successful first code push (e.g., "Batch 45, 26 October, 1st code"). 
- **`.dev`** **browser editor**: Demonstrated opening a GitHub repo in a browser-based VS Code by changing `.com` to `.dev` in the URL. 
- **GitHub portfolio example**: Student Mohd Zubair had already built a GitHub portfolio; instructor highlighted this as a good practice and asked him to present the 5 commands used in the next class. 

---

## Pending Confirmation

- Students who encountered errors (403, branch mismatch, credential conflicts) need to **retry the full push workflow** after applying the relevant fix before the next class. 
- Students without a GitHub account or with incomplete setup need to complete account creation and first push independently. 
- **Transit Gateway** (AWS topic raised by a student) — acknowledged but not covered; will be included in the AWS revision session. 
- **Load Balancer differences** (Classic vs. ALB vs. NLB) — noted as an interview-relevant topic; deferred to a later session. 

---

## Action Items

- **All students**: Complete the first code push to GitHub (if not done in class) — retry with error fixes applied. 
- **All students**: Update GitHub profile with a real photo, name, and bio. 
- **Mohd Zubair**: Prepare a 5-minute walkthrough of the 5 Git commands used to build his portfolio — to present next class. 
- **All students**: Post a one-line session recap in the WhatsApp group summarizing what was learned (e.g., commands run, what was pushed). 
- **All students**: Do NOT use office laptops for Git/GitHub practicals — use personal laptops only. 
- **Instructor**: Cover branching in depth in the next session (branch creation, switching, merging, upstream sync for forks). 
- **Instructor**: Cover GitLab and GitHub Actions in upcoming sessions. 
- **All students**: Review all interview Q&A already uploaded to the **LMS system** for AWS and Linux revision ahead of the mock interview. 
