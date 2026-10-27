## Key Outcomes

The session provided a comprehensive walkthrough of Git and GitHub concepts within a DevOps context, using a clock-building scenario to simulate real-world team development. Key topics covered included repository creation, committing and pushing code, branching strategies, pull requests, merge conflicts, and advanced Git commands. Participants also explored environment-based branching (dev, QA, production) and received guidance on interview-ready answers for common Git questions.

---

## Decisions Made

- **DevOps role in code**: DevOps engineers are responsible for **code management and automation**, not writing code; developers write code and push it via Git. 
- **Language choice**: **Python** selected for the clock scenario due to its ready-made libraries (`time`, `datetime`), platform independence, open-source nature, and ease of use. 
- **Branch naming**: Default branch should be called **`main`** for new projects; older projects (3+ years) may still use **`master`** — both are functionally identical, the name change was introduced when **Microsoft acquired GitHub**. 
- **Code ownership**: **Code owners** (technical leads/managers) are responsible for approving pull requests; DevOps engineers manage code infrastructure but do not own code review responsibility. 
- **Branch protection**: Branch protection rules in GitHub settings can restrict who can merge into `main`, enforcing review gates before any code lands in production. 
- **JDK installation**: Participants instructed to install **JDK 21** (not JRE) on local machines ahead of the next Jenkins-focused session. 

---

## Core Concepts Covered

### Why Git and GitHub in DevOps

- DevOps spans development **and** operations — the "Dev" in DevOps requires managing code, even if engineers don't write it. 
- Developers write code locally, push to GitHub via Git; **code management and CI/CD automation** fall to the DevOps team. 
- Every commit is tracked with a **commit ID** tied to the author, enabling accountability (e.g., identifying which developer introduced a bug). 

### Scenario: Building a Clock Application

- A client requirement to build a **clock** (hours, minutes, AM/PM) was used as the teaching scenario. 
- The requirement was divided into **three modules/tickets** assigned to three developers of varying experience:
    - **Nilesh** (9 years, Team Lead) → Hour module (~2 hours to complete) 
    - **Rita** (5 years, Mid-level) → Minute module (~1–2 days) 
    - **Mamta** (6 months, Junior) → AM/PM feature (~3–5 days) 
- Each developer works in isolation; upon completion, code is pushed to a **centralized GitHub repository** so it can be combined. 

### GitHub Copilot

- Shortcut to open Copilot in VS Code: **Ctrl + Shift + I**. 
- Copilot is an **AI code assistant** — useful for generating boilerplate code from prompts. 
- Demonstrated live: a prompt ("I'm a Python developer, write a code to create a clock using libraries") generated a working clock script. 
- Running Python code: `python <filename>.py` compiles and runs the script. 

---

## Git Workflow — Step-by-Step (First-Time Setup)

The following commands were demonstrated live for initializing and pushing a new repository: 

1. **`git init`** — Initializes an empty local Git repository (one-time activity)
2. **`git add *`** — Stages all files for commit
3. **`git commit -m "message"`** — Commits staged files with a descriptive message; creates a trackable commit ID
4. **`git branch -M main`** — Renames local branch from `master` to `main` to match GitHub's default
5. **`git remote add origin <URL>`** — Links local repo to the remote GitHub repository (`origin` is a conventional alias for the remote URL)
6. **`git push -u origin main`** — Pushes code to GitHub; `-u` sets **upstream**, meaning future pushes track this branch automatically 

### Everyday Commands (After Initial Setup)

Mnemonic shared by a participant: **ACP — Add, Commit, Push** 

- `git add <file or *>` — Stage changed files
- `git commit -m "second message"` — Commit with context on what changed
- `git push` — Push to the already-configured remote; no need to re-add origin or reset branch 

---

## Branching Strategy

### Why Branches Exist

- Branches isolate individual developer work so the **main branch is never directly disrupted** during active development. 
- Best practice: **one branch per feature/ticket**, named with the Jira ticket number and feature name. 
    - Example: `feature/JIRA-007-alarm` for Dinesh's alarm feature ticket

### Branch Lifecycle

1. Developer creates a **feature branch** from the main/dev branch
2. Writes and commits code within that branch
3. Raises a **Pull Request (PR)** to merge into the target branch
4. Reviewer approves → code is merged
5. Feature branch is **deleted** after merge (no ongoing utility) 

### Environment-Based Branching (Real-World)

For teams with 3+ years of experience, a three-tier branch model is standard: 

- **Dev branch**: All developer feature branches merge here first; developer self-tests in the dev environment
- **QA branch**: Stable, versioned code promoted here after dev validation; QA team tests in QA environment
- **Production branch**: Final, QA-approved code deployed to production via Jenkins pipeline
> 
> Interview answer guidance: If asked about branching strategy, describe this three-branch model and explain that developers can only push to the **dev branch** directly; promotion to QA and production is controlled and version-tagged. 

---

## Pull Requests (PRs)

### What Is a PR?

- A **Pull Request** is a formal request to merge code from a feature branch into a target branch (e.g., `main` or `dev`). 
- Abbreviated as **PR**; raising a PR is distinct from `git pull` (which fetches remote code to local). 

### PR Workflow

1. Developer completes work on feature branch and raises a PR 
2. **Reviewer** (code owner / TL / manager) is assigned 
3. Reviewer leaves **comments** — requesting changes, flagging missing documentation, or approving 
4. Developer addresses comments, updates code, re-pushes
5. Once all checks pass (green tick), the reviewer clicks **Merge Pull Request → Confirm Merge** 
6. Code lands in the target branch; feature branch can then be deleted 

### PR Options in GitHub UI

- **Reviewer**: Who reviews the code 
- **Assignee**: Who is responsible for the PR
- **Label**: Tag the PR type — `enhancement` for new features, `bug` for fixes, etc. 
- **Project / Milestone**: Organizational metadata
- **Branch protection rules** (in Settings): Enforce required reviews, status checks, and conversation resolution before merging 

### Real-World PR Volume

- Kubernetes' GitHub repository was cited as an example with **1,400+ open PRs** — demonstrating that large projects require strict prioritization and ordering. 
- **Hotfixes** take priority over regular PRs regardless of submission order. 

---

## Merge Conflicts — Causes and Resolution

### Scenario Analysis (Dinesh vs. Ankur)

Three conflict scenarios were walked through: 

|                        Scenario                        |                  Outcome                  |
|--------------------------------------------------------|-------------------------------------------|
| Two developers edit **different files**                | No conflict — both merge cleanly          |
| Two developers edit the **same file, different lines** | No conflict — Git resolves automatically  |
| Two developers edit the **same file, same line**       | **Conflict** — manual resolution required |

### Resolution Process

- Manager checks **which PR was raised first** and which requirement is independent 
- The independent/first PR is merged first
- The second developer must **pull the latest merged code**, apply their changes on top, and raise a new PR 
- Developers should **`git pull`** **daily** to keep their local branch current and minimize conflict surface area 

---

## Advanced Git Commands (Conceptual Overview)

These are used rarely by DevOps engineers but appear in interviews: 

|         Command         |                             Purpose                             |
|-------------------------|-----------------------------------------------------------------|
| `git clone`             | Copies an entire remote repository to local machine             |
| `git checkout`          | Switches between branches                                       |
| `git diff`              | Shows line-by-line differences between file versions            |
| `git reset <commit-id>` | Rolls back to a previous commit state                           |
| `git stash`             | Temporarily shelves uncommitted changes without committing      |
| `git rebase`            | Replays commits one-by-one on top of another branch's tip       |
| `git squash`            | Collapses multiple commits into a single commit before merging  |

### Squash — When and Why

- Clients or managers may request squashing when a developer has made **10+ small commits** over several days, making history hard to audit 
- Squash combines all commits into one clean entry in the target branch's history 

### Rebase vs. Squash vs. Fast-Forward Merge

- **Rebase**: Commits are replayed individually on top of the latest base — preserves granular history 
- **Squash on merge**: All commits collapsed into one before merging — cleaner history 
- **Fast-forward merge**: No merge commit created; branch pointer simply advances to the latest commit — used when there are no diverging changes 

---

## Key Interview Questions Highlighted

|                      Question                       |                                                       Answer                                                       |
|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| How do you set your Git username?                   | `git config --global user.name "Name"`                                                                             |
| What is the difference between `main` and `master`? | No functional difference; `master` was the original default, `main` introduced after Microsoft acquired GitHub     |
| What is a PR?                                       | A request to merge a feature branch into the main branch; raised after development is complete                     |
| `git pull` vs. `git fetch`?                         | `git fetch` retrieves remote changes without merging; `git pull` fetches **and** merges into the current branch    |
| What is upstream (`-u`)?                            | Sets the default remote tracking branch so future `git push` commands don't need to specify the remote and branch  |
| What is `origin`?                                   | A conventional alias for the remote repository URL; set via `git remote add origin <URL>`                          |
| If your project is 3+ years old, which branch name? | Likely `master`; new projects use `main`                                                                           |
| Who approves PRs?                                   | Code owners — typically technical leads or managers with domain knowledge                                          |

---

## Git Staging Area — Visual Clarification

Three-zone model explained: 

1. **Workspace (Working Directory)**: Local files being edited — changes exist here first
2. **Staging Area (Index)**: Files added via `git add`; changes are staged but not yet committed
3. **Local Repository**: Changes finalized via `git commit`; tracked with commit ID
4. **Remote Repository (GitHub)**: Code pushed via `git push`; visible to the whole team
- `git reset` moves code **back** from local repository to staging or workspace 
- `git diff` shows differences **between** any two of these zones 

---

## Pending Confirmation / Action Items

- **All participants**: Install **JDK 21** on local machines before the next class (link shared in chat); verify with `java -version` in terminal 
- **All participants**: Practice the full Git push workflow (init → add → commit → push) at least once more; those who encountered errors should retry without creating a README file on GitHub first 
- **All participants**: Post a LinkedIn update about today's learning to build profile visibility 
- **Next session**: Jenkins installation, Java environment setup, and introduction to CI/CD pipeline concepts 

---

## Open Questions

- **API Gateway (AWS)**: Shubham asked about API Gateway coverage — confirmed it is **not in the current syllabus** but will be touched on in supplementary content 
- **`.NET`** **CI/CD**: A participant asked whether CI/CD concepts apply to .NET — confirmed: **CI/CD concepts are language-agnostic**; the pipeline process is identical regardless of whether the codebase is Java, Python, Go, or .NET 
- **Staging environment purpose**: Discussed as a **pre-production replica** used to validate code under production-like conditions before final release; alpha/beta releases map to this layer 
- **AWS billing concern**: One participant reported unexpected charges (~₹1,500); advised to disable **auto-pay** in PhonePe and set a budget alarm; encouraged to continue practicing and revert resources to avoid further charges 
