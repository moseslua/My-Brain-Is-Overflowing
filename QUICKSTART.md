# ML Research + Engineering Crew — Quick Start Guide

> From zero to fully operational AI research & engineering system in 15 minutes.

---

## Prerequisites

### Required Software

| Tool | Purpose | Install Command |
|------|---------|-----------------|
| **Obsidian** | Note-taking app | [Download](https://obsidian.md) |
| **Claude Code** | AI agent runtime | `npm install -g @anthropics/claude-code` |
| **Git** | Version control | `apt install git` / `brew install git` |
| **Python 3.10+** | ML/DS tooling | `apt install python3` / `brew install python@3.11` |

### Optional (But Recommended)

| Tool | Purpose |
|------|---------|
| **Weights & Biases** | Experiment tracking | `pip install wandb` |
| **Docker** | Containerization | [Docker Desktop](https://docker.com) |
| **kubectl** | Kubernetes management | `brew install kubectl` |

---

## Installation Steps

### Step 1: Create Your Vault

```bash
# Create a directory for your vault
mkdir -p ~/vaults/research-vault
cd ~/vaults/research-vault

# Initialize git (optional but recommended)
git init
```

### Step 2: Install the Crew

```bash
# Clone this repo INTO your vault
cd ~/vaults/research-vault
git clone https://github.com/moseslua/My-Brain-Is-Full-Crew.git

# Run the installer
cd My-Brain-Is-Full-Crew
bash scripts/launchme.sh
```

The installer will:
- ✅ Copy 21 agent files to `.claude/agents/`
- ✅ Copy 22 skills to `.claude/skills/`
- ✅ Copy references to `.claude/references/`
- ✅ Copy CLAUDE.md (dispatcher rules)
- ✅ Create Meta/ directory for logs and state
- ✅ Set up folder structure

### Step 3: Open in Obsidian

1. Open Obsidian
2. Click "Open folder as vault"
3. Select `~/vaults/research-vault`
4. Install recommended plugins (Templater, Dataview)

### Step 4: Configure Claude Code

```bash
# Navigate to your vault
cd ~/vaults/research-vault

# Start Claude Code
claude

# Claude will automatically load agents from .claude/agents/
# You'll see: "Loaded 21 agents"
```

### Step 5: Initialize Your Vault

In Claude Code, type:

```
initialize the vault
```

The `/onboarding` skill will start and ask you:
- Name and role
- Research interests
- Active projects
- Team size
- Integrations (Gmail, Calendar, WandB, etc.)

---

## Daily Usage Workflow

### Morning Standup (2 minutes)

```bash
# In Claude Code:
> "What's my research agenda today?"

Response:
- 3 papers queued for review
- 2 experiments running (exp-042 at 80%, exp-043 queued)
- 1 deadline approaching (ICLR in 5 days)
- Yesterday's progress summary
```

### Reading Papers (5 minutes)

```bash
# Add a paper:
> "Read arxiv 2501.12345"

# Or:
> "Add paper https://arxiv.org/abs/2501.12345"

# Or with PDF:
> "Process this PDF" [paste PDF]

Response:
- Summary created in 03-Resources/Papers/2025/
- Key findings extracted
- Connections to your existing work identified
- Added to reading queue with priority score
- 3 related papers suggested
```

### Running Experiments (Ongoing)

```bash
# Log an experiment:
> "Log experiment"

System prompts:
- Project name? [project-alpha]
- Config? [paste or describe]
- Results? [metrics]
- Observations? [notes]

Response:
- Experiment logged as exp-2025-04-06-001
- Compared to previous best: +2.1% improvement
- SoTA gap: still 15.8 pp behind
- Suggested next experiments generated
```

### Checking Competition (1 minute)

```bash
# Check benchmark standings:
> "What's SoTA on MMLU?"
> "Are we competitive on HumanEval?"
> "What did OpenAI release this week?"

Response:
- Current SoTA vs your best
- Gap analysis
- Recent competitor releases
- Strategic recommendations
```

### Generating Ideas (10 minutes)

```bash
# Generate research ideas:
> "Generate ideas from my MoE papers"
> "Combine speculative decoding with my attention work"
> "What gaps exist in efficient transformers?"

Response:
- 5 novel ideas with confidence scores
- Implementation sketches
- Resource estimates
- Risk assessments
- Prioritized by impact/feasibility
```

### System Design (When Building)

```bash
# Architecture review:
> "Design system for serving 7B model with <100ms latency"

Response:
- 2-3 architecture options
- Trade-off analysis
- Cost estimates
- Technology recommendations
- Architecture Decision Record created
```

### Data Pipeline (When Processing)

```bash
# Design data pipeline:
> "Design pipeline for processing 10TB of training data"

Response:
- Pipeline architecture
- Technology stack
- Cost estimates
- Data quality checks
- Implementation code
```

### Security Review (Before Production)

```bash
# Security audit:
> "Security audit my model serving API"

Response:
- Threat model
- Vulnerabilities found
- Risk ratings
- Remediation steps
- Compliance checklist
```

### Writing Papers (When Publishing)

```bash
# Draft paper sections:
> "Draft methods section for my MoE paper"
> "Write related work citing my vault papers"
> "Format for NeurIPS submission"

Response:
- Section draft in academic style
- Proper citations from vault
- Comparison to SoTA
- Figures suggestions
```

---

## Key Commands Reference

### Research Commands

| Command | Purpose | Agent/Skill |
|---------|---------|-------------|
| `"Read arxiv 2501.12345"` | Ingest paper | `/paper-ingest` |
| `"What's SoTA on MMLU?"` | Check benchmarks | `/sota-check` |
| `"Log experiment"` | Track experiment | `/experiment-log` |
| `"Generate ideas"` | Brainstorm | `/idea-generation` |
| `"Draft abstract"` | Write paper | `paper-writer` |

### Engineering Commands

| Command | Purpose | Agent/Skill |
|---------|---------|-------------|
| `"Design system for X"` | Architecture | `/architecture-review` |
| `"Review this code"` | Code review | `code-reviewer` |
| `"Deploy model"` | MLOps | `mlops-engineer` |
| `"Security audit"` | Security | `/security-audit` |
| `"Optimize performance"` | Performance | `/performance-optimization` |

### Data Science Commands

| Command | Purpose | Agent/Skill |
|---------|---------|-------------|
| `"Analyze dataset"` | EDA | `/exploratory-analysis` |
| `"Design data pipeline"` | Data engineering | `/data-pipeline-design` |
| `"Statistical test"` | Statistics | `data-scientist` |
| `"Feature engineering"` | Features | `data-engineer` |

### Productivity Commands

| Command | Purpose | Agent/Skill |
|---------|---------|-------------|
| `"Save this note"` | Capture | `scribe` |
| `"Find my notes on X"` | Search | `seeker` |
| `"Triage inbox"` | Organize | `/inbox-triage` |
| `"Weekly agenda"` | Planning | `/weekly-agenda` |

---

## Vault Structure (After Setup)

```
~/vaults/research-vault/
├── .claude/                      # Agent & skill files
│   ├── agents/                   # 21 agent definitions
│   │   ├── architect.md
│   │   ├── principal-investigator.md
│   │   ├── literature-curator.md
│   │   ├── experiment-runner.md
│   │   ├── tech-lead.md
│   │   ├── mlops-engineer.md
│   │   └── ... (21 total)
│   ├── skills/                   # 22 skill definitions
│   │   ├── onboarding/
│   │   ├── paper-ingest/
│   │   ├── sota-check/
│   │   ├── architecture-review/
│   │   └── ... (22 total)
│   ├── references/               # Shared documentation
│   └── hooks/                    # Automation hooks
│
├── CLAUDE.md                     # Dispatcher routing rules
│
├── 00-Inbox/                     # Capture everything first
├── 01-Projects/                  # Active research projects
│   └── project-alpha/
│       ├── experiments/
│       ├── papers/
│       ├── code/
│       └── _index.md
├── 02-Areas/                     # Research domains
├── 03-Resources/
│   ├── Papers/                   # Organized by year
│   ├── Code-Snippets/
│   └── Datasets/
├── 08-Experiments/               # Experiment tracking
├── 09-SoTA-Tracking/             # Benchmark monitoring
├── 10-Systems/                   # System architectures
├── 11-Pipelines/                 # Data pipelines
├── MOC/                          # Maps of Content
├── Templates/                    # Note templates
└── Meta/                         # System files
    ├── user-profile.md
    ├── experiment-index.db
    ├── agent-logs/
    └── states/
```

---

## Integration Setup

### Weights & Biases (Experiment Tracking)

```bash
# Install
pip install wandb

# Login
wandb login

# The system will auto-log experiments
```

### GitHub (Code Repository)

```bash
# Set up in vault root
cd ~/vaults/research-vault
git remote add origin https://github.com/YOUR_USERNAME/research-vault.git

# The system tracks code in 01-Projects/*/code/
```

### Google Cloud (Compute)

```bash
# Install gcloud
brew install google-cloud-sdk

# Authenticate
gcloud auth login
gcloud config set project YOUR_PROJECT

# The mlops-engineer can provision resources
```

### AWS (Alternative Compute)

```bash
# Install AWS CLI
pip install awscli

# Configure
aws configure

# The system supports multi-cloud
```

---

## Updating the System

```bash
cd ~/vaults/research-vault/My-Brain-Is-Full-Crew

# Pull latest changes
git pull origin main

# Run updater
bash scripts/updateme.sh
```

This will:
- Update agent definitions
- Update skills
- Update references
- Preserve your custom agents
- Preserve your vault notes

---

## Troubleshooting

### Problem: Claude Code doesn't see agents

**Solution:**
```bash
# Check .claude directory exists
ls -la ~/vaults/research-vault/.claude/agents/

# If missing, re-run installer
cd ~/vaults/research-vault/My-Brain-Is-Full-Crew
bash scripts/launchme.sh

# Restart Claude Code
exit
claude
```

### Problem: Skills not working

**Solution:**
```bash
# Check skills directory
ls ~/vaults/research-vault/.claude/skills/

# Verify CLAUDE.md exists
cat ~/vaults/research-vault/CLAUDE.md | head -20

# Re-install if needed
bash scripts/launchme.sh
```

### Problem: Obsidian plugins not working

**Solution:**
```bash
# In Obsidian:
# 1. Settings → Community Plugins
# 2. Enable "Templater" and "Dataview"
# 3. Restart Obsidian
```

---

## Advanced Configuration

### Custom Agents

Create your own agents:

```bash
# In Claude Code:
> "Create a new agent"

# The system will ask:
# - Agent name?
# - Purpose?
# - Triggers?
# - Tools needed?

# Saves to: .claude/agents/your-agent.md
```

### Environment Variables

Create `.env` in vault root:

```bash
# API Keys (replace with your actual keys)
OPENAI_API_KEY=sk-your_key_here
ANTHROPIC_API_KEY=sk-ant-your_key_here
WANDB_API_KEY=your_wandb_key_here

# Cloud
GOOGLE_APPLICATION_CREDENTIALS=/path/to/key.json
AWS_ACCESS_KEY_ID=...
AWS_SECRET_ACCESS_KEY=...

# Database
DATABASE_URL=postgresql://...

# Slack (for notifications)
SLACK_WEBHOOK_URL=...
```

### Custom Skills

Create new skills in `.claude/skills/your-skill/SKILL.md`:

```markdown
---
name: your-skill
description: >
  What your skill does.
  Triggers: "trigger 1", "trigger 2".
---

# Your Skill Name

## Capabilities
1. Thing it can do
2. Another thing

## Usage
How to use it...
```

---

## Next Steps

1. **Complete onboarding**: Run `initialize the vault` in Claude Code
2. **Add your first paper**: `read arxiv 2501.12345`
3. **Log an experiment**: `log experiment`
4. **Explore**: Try different agents and skills

---

## Getting Help

### In-System Help

```bash
# List all agents
> "List my agents"

# List all skills
> "What skills do I have?"

# Get agent help
> "What can principal-investigator do?"

# Get skill help
> "How do I use paper-ingest?"
```

### Documentation

- `CLAUDE.md` — Routing rules and agent index
- `references/agents.md` — Agent descriptions
- `references/agents-registry.md` — Agent registry
- Each agent file has detailed instructions

### Community

- Discord: [link in README]
- Issues: GitHub Issues
- Discussions: GitHub Discussions

---

**You're now ready to run a world-class AI research & engineering operation!** 🚀

Start with: `initialize the vault`
