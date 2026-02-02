# Operational Tech Stack Manual
## A Guide for Non-Developers Building Real Systems with AI

---

## Table of Contents

1. [The Mental Model](#1-the-mental-model)
2. [Understanding the Claude Ecosystem](#2-understanding-the-claude-ecosystem)
3. [Configuration Files: What Lives Where](#3-configuration-files-what-lives-where)
4. [MCP Servers: Configuration by Claude Version](#4-mcp-servers-configuration-by-claude-version)
5. [Repository and Filesystem Structure](#5-repository-and-filesystem-structure)
6. [GitHub and GitHub Desktop](#6-github-and-github-desktop)
7. [The Complete Workflow](#7-the-complete-workflow)
8. [Day-to-Day Practices](#8-day-to-day-practices)
9. [Sharing Claude Context and Learnings](#9-sharing-claude-context-and-learnings)
10. [The Bigger Picture](#10-the-bigger-picture)

---

## 1. The Mental Model

### What You're Actually Building

You're not "learning to code" — you're building **operational infrastructure**. Think of it like this:

- A restaurant needs a kitchen, ordering system, and inventory tracking
- Your company needs workflows, data pipelines, and automation
- You're building that operational kitchen, just digitally

The tools have changed. Before, you needed a developer to build these systems. Now, AI lets you build them yourself — but you still need the *habits* developers have for keeping track of what you built and working together without breaking things.

### The Core Concepts

**Version Control** = Time Travel for Your Work
- Every time you save a "commit," you create a restore point
- If something breaks, you can go back to when it worked
- You can see exactly what changed and when

**Repositories** = Tracked Folders
- A repository (repo) is just a folder where every change is recorded
- GitHub stores your repos in the cloud so both partners can access them
- Your local computer has copies that sync with GitHub

**Branching** = Parallel Universes
- You can create a "branch" to try something risky
- If it works, merge it back into the main version
- If it fails, delete the branch — the main version is untouched

**Collaboration** = Working on the Same System Without Colliding
- Each partner works on their local copy
- Push your changes to GitHub when ready to share
- Pull your partner's changes to get their updates
- If you both change the same thing, Git helps you resolve it

### Why This Matters

Without this system:
- "Which version of that script is the right one?"
- "Wait, I think I broke something — what did it look like before?"
- "Did you change this or did I?"
- "How does this thing work? We built it six months ago..."

With this system:
- One source of truth on GitHub
- Complete history of every change
- Clear documentation of how and why things work
- Either partner can pick up where the other left off

---

## 2. Understanding the Claude Ecosystem

Claude comes in several forms, each suited for different work. Understanding which to use when will save you time and frustration.

### Claude Web (claude.ai)

**What it is**: Claude in your browser, accessed at claude.ai

**What it can do**:
- Conversation, research, drafting, analysis
- Process files you upload manually
- Access the web (with your Claude Max plan)

**What it cannot do**:
- Access your local filesystem
- Connect to MCP servers (no Airtable, Autodesk, etc.)
- Execute code on your computer
- Remember context between separate conversations (unless in a Project)

**Best for**:
- Quick questions while you're away from your main computer
- Research and drafting that doesn't need your local files
- Sharing a conversation with your partner via link
- Working within Claude Projects (shared context with your team)

**When NOT to use it**:
- Building or modifying your actual system (use Desktop or CLI)
- Anything requiring file access or MCP server connections

---

### Claude Desktop App

**What it is**: A native application installed on your Mac or Windows computer

The Desktop app has three modes that change what Claude can do:

#### Chat Mode
- Conversation only
- No file access
- Like Claude Web, but in an app
- Use for: quick questions, brainstorming

#### Cowork Mode
- Claude can **read** files you share
- You drag files into the conversation or reference them
- Claude can suggest changes but cannot write them directly
- Use for: reviewing code, getting feedback on configurations

#### Code Mode
- Full filesystem access — Claude can read, write, and execute
- Can run terminal commands
- Can connect to MCP servers
- Use for: actually building and modifying your system

**Where Desktop stores its files (macOS)**:
```
~/Library/Application Support/Claude/
├── claude_desktop_config.json    # MCP server configuration
├── logs/                         # Application logs
└── [other app data]
```

**Where Desktop stores its files (Windows)**:
```
%APPDATA%\Claude\
├── claude_desktop_config.json    # MCP server configuration
├── logs/                         # Application logs
└── [other app data]
```

**Important**: These are personal, local files. They are NOT shared between partners. Each partner configures their own Desktop app.

---

### Claude Code (CLI)

**What it is**: Claude running in your terminal (command line interface)

**How it differs from Desktop Code mode**:
- Runs entirely in terminal — no graphical interface
- More powerful agent capabilities
- Different configuration system
- Better for complex, multi-step operations
- Can be used in any directory

**How to access it**: Open Terminal (Mac) or Command Prompt/PowerShell (Windows) and type `claude`

**Where CLI stores its configuration**:

*Global settings* (apply to all projects, personal to you):
```
~/.claude/
├── settings.json         # Your personal defaults
├── credentials            # API keys (NEVER share)
└── [other global config]

~/.claude.json             # Legacy global config (if present)
```

*Project settings* (apply to one repo, can be shared):
```
your-repo/
└── .claude/
    ├── settings.json      # Project permissions, behaviors
    ├── commands/          # Custom slash commands for this project
    └── [project config]
```

**Key distinction**:
- `~/.claude/` = Personal, never commit to git
- `your-repo/.claude/` = Project-level, CAN commit to git (no secrets!)

---

### Claude Teams / Max Plan Features

**What the Teams layer adds**:

**Projects** (Shared Context):
- Create a Project in claude.ai that both partners can access
- Add documents, instructions, and context to the Project
- Every conversation in that Project has access to that context
- Great for: "Here's how our Airtable is structured" or "Here are our coding standards"

**What's shared at the team level**:
- Projects and their contents
- Conversation history within Projects (if you choose to share)
- Team-wide settings and preferences

**What's still individual**:
- Your local Desktop and CLI configurations
- MCP server credentials
- Personal conversations not in shared Projects

**How to use Projects effectively**:
1. Create a Project for your operational system (e.g., "Company Tech Stack")
2. Add key documentation as Project Knowledge
3. Both partners work within this Project for shared context
4. Claude remembers the context across conversations in this Project

---

### Quick Reference: Which Claude When?

| Task | Best Claude | Why |
|------|-------------|-----|
| Quick question while mobile | Web | Available anywhere |
| Research, drafting | Web or Desktop Chat | No file access needed |
| Review a config file | Desktop Cowork | Read access, can't break anything |
| Build/modify your system | Desktop Code or CLI | Full read/write/execute |
| Complex multi-step automation | CLI | Most powerful agent capabilities |
| Shared context with partner | Web (in a Project) | Projects sync between team members |

---

## 3. Configuration Files: What Lives Where

This is crucial: some files should NEVER leave your computer, while others should ALWAYS be shared. Getting this wrong can expose credentials or leave your partner unable to run things.

### The Golden Rule

**Contains credentials, API keys, or personal preferences?** → NEVER commit to git
**Contains project configuration, code, or documentation?** → Commit to git

---

### Application Configs (NEVER Commit)

These live in system locations on your computer. They contain personal settings, credentials, and API keys.

**Claude Desktop (macOS)**:
```
~/Library/Application Support/Claude/
├── claude_desktop_config.json    # Your MCP server configs (contains credentials!)
└── [other personal app data]
```

**Claude Desktop (Windows)**:
```
%APPDATA%\Claude\
├── claude_desktop_config.json
└── [other personal app data]
```

**Claude Code CLI**:
```
~/.claude/
├── settings.json         # Personal defaults
└── [credentials, keys]

~/.claude.json             # Legacy config (if present)
```

**Why these stay local**:
- Contain API keys and secrets
- Personal preferences that differ between partners
- Different computers may have different paths

---

### Project Configs (CAN Commit)

These live inside your repository and define how the project behaves. They should NOT contain any secrets.

```
your-repo/
└── .claude/
    ├── settings.json      # Project-level permissions and behaviors
    │                      # Example: which directories Claude can write to
    │
    └── commands/          # Custom slash commands
        ├── deploy.md      # /deploy command definition
        └── test.md        # /test command definition
```

**What goes in `.claude/settings.json`**:
```json
{
  "permissions": {
    "allow_file_write": ["src/", "config/"],
    "deny_file_write": ["credentials/", ".env"]
  },
  "behaviors": {
    "auto_commit": false
  }
}
```

**Notice**: No API keys, no passwords, nothing secret. Just project structure and rules.

---

### Company Data and System (SHOULD Commit)

This is the actual system you're building. It absolutely should be in git.

```
shared-tooling/
├── mcp-servers/           # MCP server CODE (not credentials)
│   └── airtable-server/
│       ├── index.js
│       └── README.md
│
├── claude/
│   ├── skills/            # Custom Claude skills
│   └── hooks/             # Pre/post execution hooks
│
├── n8n/
│   └── workflows/         # Exported workflow JSON
│
├── cloud-functions/       # Your cloud function code
│
└── .claude/
    └── settings.json      # Project-level Claude config
```

---

### The Complete Picture

| Location | Contains | In Git? | Shared? |
|----------|----------|---------|---------|
| `~/Library/Application Support/Claude/` | Desktop app config, credentials | NO | NO |
| `~/.claude/` | CLI personal config, credentials | NO | NO |
| `repo/.claude/settings.json` | Project permissions | YES | YES |
| `repo/.claude/commands/` | Custom slash commands | YES | YES |
| `repo/mcp-servers/` | MCP server code | YES | YES |
| `repo/.env` | Environment variables | NO | NO |
| `repo/.env.example` | Template for env vars | YES | YES |

---

## 4. MCP Servers: Configuration by Claude Version

### What Are MCP Servers?

MCP (Model Context Protocol) servers let Claude connect to external tools. Think of them as bridges:

- **Airtable MCP Server** → Claude can read/write your Airtable bases
- **Autodesk MCP Server** → Claude can interact with Autodesk projects
- **Filesystem MCP Server** → Claude can access specific folders
- **Slack MCP Server** → Claude can read/send Slack messages
- **BigQuery MCP Server** → Claude can query your data warehouse

Without MCP servers, Claude can only see what you paste into the conversation. With them, Claude can directly access your tools.

### Configuration by Claude Version

| Claude Version | Config Location | Scope | Shareable? |
|----------------|-----------------|-------|------------|
| Desktop App | `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) | Per-user | NO (local file) |
| Claude Code CLI | `~/.claude.json` (global) or project's `.mcp.json` | Global or per-project | Project-level: YES |
| Claude Web | N/A | None | N/A |

---

### Claude Desktop MCP Configuration

Location: `~/Library/Application Support/Claude/claude_desktop_config.json`

Example configuration:
```json
{
  "mcpServers": {
    "airtable": {
      "command": "npx",
      "args": ["-y", "@airtable/mcp-server"],
      "env": {
        "AIRTABLE_API_KEY": "patXXXXXXXXXXXXXX"
      }
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/yourname/Company"]
    },
    "slack": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "xoxb-XXXXXXXXXXXX"
      }
    }
  }
}
```

**Note**: This file contains API keys! It stays on your computer, never in git.

---

### Claude Code CLI MCP Configuration

The CLI can use global or project-level MCP configs.

**Global** (`~/.claude.json`):
```json
{
  "mcpServers": {
    "airtable": {
      "command": "npx",
      "args": ["-y", "@airtable/mcp-server"],
      "env": {
        "AIRTABLE_API_KEY": "patXXXXXXXXXXXXXX"
      }
    }
  }
}
```

**Project-level** (in your repo):
You can reference MCP servers in your project's `.claude/settings.json`, but credentials should come from environment variables, not the file itself.

---

### Best Practices for MCP Servers

**1. Keep code in the shared repo, credentials local**

Your repo structure:
```
shared-tooling/
└── mcp-servers/
    └── custom-autodesk/
        ├── index.js           # The actual server code (committed)
        ├── package.json       # Dependencies (committed)
        ├── README.md          # How to set it up (committed)
        └── .env.example       # Template showing what's needed (committed)
            # AUTODESK_CLIENT_ID=your-client-id-here
            # AUTODESK_CLIENT_SECRET=your-secret-here
```

Each partner's local `.env` (NOT committed):
```
AUTODESK_CLIENT_ID=abc123
AUTODESK_CLIENT_SECRET=supersecret456
```

**2. Create template configurations**

In your repo, create `shared-tooling/claude/configs/mcp-servers.template.json`:
```json
{
  "mcpServers": {
    "airtable": {
      "command": "npx",
      "args": ["-y", "@airtable/mcp-server"],
      "env": {
        "AIRTABLE_API_KEY": "YOUR_AIRTABLE_API_KEY_HERE"
      }
    },
    "autodesk": {
      "command": "node",
      "args": ["/Users/YOURNAME/Company/shared-tooling/mcp-servers/custom-autodesk/index.js"],
      "env": {
        "AUTODESK_CLIENT_ID": "YOUR_CLIENT_ID_HERE",
        "AUTODESK_CLIENT_SECRET": "YOUR_SECRET_HERE"
      }
    }
  }
}
```

**3. Setup instructions for each partner**

Document in `shared-tooling/mcp-servers/README.md`:
```markdown
# MCP Server Setup

## For Claude Desktop

1. Copy `claude/configs/mcp-servers.template.json`
2. Paste contents into `~/Library/Application Support/Claude/claude_desktop_config.json`
3. Replace all placeholder values with your actual credentials
4. Restart Claude Desktop

## Getting Credentials

### Airtable
1. Go to airtable.com/create/tokens
2. Create a token with the scopes: data.records:read, data.records:write
3. Copy the token (starts with "pat")

### Autodesk
1. Go to aps.autodesk.com
2. Create an app, get client ID and secret
```

---

## 5. Repository and Filesystem Structure

### GitHub Organization Structure

This is what exists on GitHub — the shared, versioned source of truth:

```
your-org/                           # GitHub Organization
│
├── shared-knowledge/               # Repo: Obsidian vault (synced)
│   ├── learnings/                  # What we discovered building the system
│   │   └── 2026-02-01-bigquery-sync-timing.md
│   ├── decisions/                  # Why we chose what we chose
│   │   └── 001-why-n8n-over-zapier.md
│   ├── context/                    # Background, research, references
│   │   └── competitor-analysis.md
│   ├── how-it-works/               # Documentation of current system
│   │   └── slack-to-airtable-flow.md
│   └── troubleshooting/            # When X breaks, do Y
│       └── n8n-webhook-failures.md
│
├── shared-tooling/                 # Repo: The operational system
│   ├── mcp-servers/                # Custom MCP server code
│   │   ├── airtable-server/
│   │   └── autodesk-server/
│   ├── claude/
│   │   ├── skills/                 # Custom Claude skills
│   │   ├── hooks/                  # Pre/post execution hooks
│   │   └── configs/                # Shareable config templates
│   │       └── mcp-servers.template.json
│   ├── n8n/
│   │   ├── workflows/              # Exported workflow JSON files
│   │   └── credentials.example     # Template (no actual secrets)
│   ├── cloud-functions/
│   │   ├── bigquery-sync/
│   │   └── autodesk-webhook/
│   ├── airtable/
│   │   └── schemas/                # Base structures, field definitions
│   ├── scripts/                    # Utility scripts, automation
│   └── .claude/                    # Project-level Claude config (committed)
│       └── settings.json
│
├── partner-a-sandbox/              # Repo: Partner A's experiments
│   └── (experimental work before it's ready for shared-tooling)
│
└── partner-b-sandbox/              # Repo: Partner B's experiments
    └── (experimental work before it's ready for shared-tooling)
```

---

### Local Filesystem Structure

This is how each partner organizes their local machine:

```
~/Company/                          # Root folder for all company work
│
├── shared-knowledge/               # Cloned from: your-org/shared-knowledge
│   └── (This IS your Obsidian vault - open this folder in Obsidian)
│
├── shared-tooling/                 # Cloned from: your-org/shared-tooling
│   └── (The operational system - where real work happens)
│
├── my-sandbox/                     # Cloned from: your-org/partner-[a|b]-sandbox
│   └── (Your personal experimentation space)
│
└── scratch/                        # NOT in git - temporary files, downloads
    └── (Anything here can be deleted anytime)
```

---

### Why This Structure?

**`~/Company/` as the root**:
- All company work in one predictable location
- Separate from personal files and other projects
- Easy to back up, easy to find
- Both partners use the same structure (just different sandbox repos)

**`shared-knowledge/` as the Obsidian vault**:
- Your documentation system IS your git repo
- Every note you write is automatically version-controlled
- Changes sync to GitHub, partner can pull them
- If Obsidian ever goes away, your notes are still markdown files

**`shared-tooling/` for the real system**:
- This is the production-ready code and configuration
- Both partners work here for permanent changes
- Everything here should work and be documented

**`my-sandbox/` for experiments**:
- Try risky things without breaking shared-tooling
- Your own repo — break it freely
- When something works, move it to shared-tooling

**`scratch/` for junk**:
- Downloads, temporary files, random tests
- Not tracked by git
- Delete anything here anytime

---

### What Goes Where?

| Location | What goes here | Tracked in git? |
|----------|----------------|-----------------|
| `~/Company/shared-knowledge/` | Obsidian vault, documentation, learnings | Yes |
| `~/Company/shared-tooling/` | The actual system (skills, workflows, scripts, MCP servers) | Yes |
| `~/Company/my-sandbox/` | Your experiments before they're ready to share | Yes (your own repo) |
| `~/Company/scratch/` | Temporary files, downloads, junk | No |
| `~/Library/Application Support/Claude/` | Claude Desktop app config (personal) | No |
| `~/.claude/` | Claude Code CLI global config (personal) | No |

---

## 6. GitHub and GitHub Desktop

### Git Concepts for Non-Developers

**Repository (repo)**: A folder whose history is tracked. Every change is recorded.

**Commit**: A snapshot of your work at a point in time. Think of it as a save point in a video game — you can always return to it.

**Branch**: A parallel version of your repo. You can experiment on a branch without affecting the main version.

**Main branch**: The "official" current state of your system. What's on main is what's real.

**Clone**: Copying a repo from GitHub to your computer. You work on the local copy.

**Push**: Uploading your local commits to GitHub. This is how you share your work.

**Pull**: Downloading your partner's commits from GitHub. This is how you get their work.

**Pull Request (PR)**: A formal proposal to merge changes from one branch into another. Your partner can review before the merge.

**Merge**: Combining changes from one branch into another.

**Diff**: A comparison showing exactly what changed between versions.

---

### GitHub (Web Interface)

**What you do on github.com**:

**Organization & Repos**:
- Create your organization (e.g., "your-company")
- Create repos within the organization
- Invite your partner as an organization member

**Access & Permissions**:
- Manage who can access what
- Set branch protection (require reviews before merging to main)

**Review & History**:
- View pull requests from your partner
- Comment on changes, request modifications
- See complete history of every file
- Compare any two points in time

**Issues & Discussions**:
- Track bugs, features, ideas
- Discuss decisions with a permanent record

---

### GitHub Desktop (Application)

**Why GitHub Desktop over command line**:
- Visual interface — see changes, history, branches
- No commands to memorize
- Harder to make mistakes
- Clear indication of what's changed

**Getting Started**:

1. **Download**: desktop.github.com
2. **Sign in**: Use your GitHub account
3. **Clone repos**:
   - File → Clone Repository
   - Select your organization's repos
   - Clone to `~/Company/` (choose this as the local path)

**Daily Operations**:

**Seeing what changed**:
- Open GitHub Desktop
- Select a repo from the left sidebar
- "Changes" tab shows all modified files
- Click a file to see exactly what changed (red = removed, green = added)

**Committing**:
- Review your changes
- Write a summary (required) — what did you change?
- Write a description (optional) — why did you change it?
- Click "Commit to main" (or your current branch)

**Pushing**:
- After committing, click "Push origin"
- Your commits are now on GitHub
- Your partner can now pull them

**Pulling**:
- Click "Fetch origin" to check for remote changes
- If there are changes, "Pull origin" appears
- Click it to download your partner's work

**Branching**:
- Current Branch dropdown → New Branch
- Name it something descriptive: "experiment-slack-integration"
- Work on this branch freely
- The main branch remains untouched

**Creating a Pull Request**:
- Push your branch
- GitHub Desktop shows "Create Pull Request" button
- Click it → opens GitHub in browser
- Add description, request review from partner
- Partner reviews, then merges (or requests changes)

---

### GitHub Desktop: Visual Guide

```
┌─────────────────────────────────────────────────────────────┐
│  GitHub Desktop                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Current Repository: [shared-tooling ▼]                     │
│  Current Branch: [main ▼]                                   │
│                                                             │
├──────────────────┬──────────────────────────────────────────┤
│                  │                                          │
│  Changes (3)     │  Modified: n8n/workflows/project-sync.json│
│  ─────────────── │  ────────────────────────────────────────│
│  ☑ project-sync  │  - "interval": 5,                        │
│  ☑ README.md     │  + "interval": 15,                       │
│  ☑ settings.json │                                          │
│                  │  (Diff view shows exactly what changed)  │
│                  │                                          │
├──────────────────┴──────────────────────────────────────────┤
│                                                             │
│  Summary: Increase sync interval to reduce API calls        │
│  ──────────────────────────────────────────────────────     │
│  Description: Was hitting rate limits on Airtable with      │
│  5-minute intervals. 15 minutes is sufficient for our       │
│  use case.                                                  │
│                                                             │
│                           [Commit to main]                  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

### Recommended Workflow with GitHub Desktop

**Morning**:
1. Open GitHub Desktop
2. For each repo (shared-knowledge, shared-tooling, my-sandbox):
   - Select the repo
   - Click "Fetch origin"
   - If changes exist, click "Pull origin"
3. You now have everything your partner did yesterday

**During work**:
1. Make changes (use Claude Desktop or CLI)
2. When something works, open GitHub Desktop
3. Review your changes
4. Commit with a clear message
5. Push immediately (don't let commits pile up)

**Sharing new work**:
1. If small change: commit directly to main, push
2. If significant change: create a branch first
3. If moving from sandbox to shared-tooling: create a PR

**End of day**:
1. Make sure all work is committed
2. Push any remaining commits
3. Quick scan: any uncommitted changes? Either commit or discard

---

## 7. The Complete Workflow

Here's a step-by-step walkthrough of a typical task: adding a new N8N workflow that syncs Airtable project data to BigQuery.

### Phase 1: Start Fresh

```
1. Open GitHub Desktop
2. Select shared-tooling repo
3. Click "Fetch origin"
4. Pull any changes from your partner
5. Repeat for shared-knowledge repo
```

You now have the latest version of everything.

### Phase 2: Decide Where to Work

**If this is experimental** (not sure it'll work):
- Work in `my-sandbox/`
- Move to `shared-tooling/` when it's proven

**If this builds on existing work** (small addition):
- Work directly in `shared-tooling/`
- Consider creating a branch if it's risky

**For this example**: We'll work in `my-sandbox/` first.

### Phase 3: Build with Claude

```
1. Open Claude Desktop (Code mode) or Claude Code CLI
2. Navigate to ~/Company/my-sandbox/
3. Tell Claude what you're building:

"I need to create an N8N workflow that:
- Triggers every hour
- Reads all projects from our Airtable base
- Transforms the data to match our BigQuery schema
- Upserts the records to BigQuery

Our Airtable base ID is appXXXXX, table is 'Projects'.
BigQuery dataset is 'operations', table is 'projects_sync'."

4. Claude builds it (creates the workflow JSON, any helper scripts)
5. Test it in N8N
```

### Phase 4: Commit Your Progress

```
1. Open GitHub Desktop
2. Select my-sandbox repo
3. See your changes listed
4. Write commit message:
   Summary: "Add Airtable to BigQuery project sync workflow"
   Description: "Hourly sync of Projects table. Handles
   field mapping and upsert logic. Tested successfully
   with 50 records."
5. Click "Commit to main"
6. Click "Push origin"
```

### Phase 5: Test Thoroughly

```
1. Run the workflow multiple times
2. Verify data in BigQuery looks correct
3. Test edge cases (empty records, special characters)
4. Note any issues or limitations
```

If something breaks, you can see exactly what you changed and revert if needed.

### Phase 6: Document the Learning

```
Create file: ~/Company/shared-knowledge/learnings/2026-02-01-airtable-bigquery-sync.md

---
# Airtable to BigQuery Sync — What We Learned

## What we built
Hourly sync from Airtable Projects to BigQuery.

## Key decisions
- Used upsert (not insert) to handle re-syncs gracefully
- Mapped Airtable 'Status' field to BigQuery ENUM
- Excluded archived projects (filter on Airtable side)

## Gotchas
- Airtable rate limits: keep batch size under 100 records per API call
- BigQuery streaming inserts have a ~90 second delay for availability
- Airtable 'Created Time' is string, needs parsing for BigQuery TIMESTAMP

## Files
- Workflow: `shared-tooling/n8n/workflows/airtable-bigquery-projects.json`
- Schema: `shared-tooling/airtable/schemas/projects.md`
---
```

Commit and push this learning note.

### Phase 7: Move to Shared Tooling

Now that it works, move it to the shared system.

**Option A: Copy files directly** (simple case)
```
1. Copy workflow JSON from my-sandbox to shared-tooling/n8n/workflows/
2. Copy any helper scripts
3. Open GitHub Desktop
4. Select shared-tooling repo
5. Commit with message: "Add Airtable to BigQuery project sync"
6. Push
```

**Option B: Create a Pull Request** (if you want partner review)
```
1. In shared-tooling repo, create new branch: "add-project-sync"
2. Add your files
3. Commit to the branch
4. Push the branch
5. Click "Create Pull Request" in GitHub Desktop
6. Add description, request partner's review
7. Partner reviews on GitHub, approves
8. Merge the PR
```

### Phase 8: Update Documentation

```
In shared-knowledge/how-it-works/airtable-bigquery-sync.md:

---
# Airtable to BigQuery Sync

## What it does
Syncs project data from Airtable to BigQuery hourly.

## How to use
The workflow runs automatically. No action needed.

## How to modify
1. Open N8N
2. Find workflow "Airtable BigQuery Projects"
3. Make changes
4. Export JSON to `shared-tooling/n8n/workflows/airtable-bigquery-projects.json`
5. Commit and push

## Troubleshooting
See: `troubleshooting/airtable-bigquery-sync.md`
---
```

Commit and push.

---

## 8. Day-to-Day Practices

### Morning Routine (5 minutes)

```
1. Open GitHub Desktop
2. For each repo you work with:
   - Fetch origin
   - Pull if there are changes
3. Quick scan: did partner commit anything new?
   - Look at commit messages
   - If interesting, click to see what changed
```

This prevents working on outdated files.

### When to Commit

**Commit when**:
- Something works that didn't before
- You reach a logical stopping point
- You're about to try something risky
- You're done for the day (never leave work uncommitted overnight)

**Don't commit when**:
- Things are broken
- You're mid-experiment
- You have a bunch of unrelated changes (split them into separate commits)

**Think of commits like checkpoints**: Would you want to come back to this exact state?

### Commit Message Format

**Summary** (required, 50 characters or less):
What changed, in imperative mood: "Add", "Fix", "Update", "Remove"

**Description** (optional, wrap at 72 characters):
Why you made this change, any context future-you needs

**Good examples**:
```
Summary: Add Slack notification when project status changes
Description: Triggers on Airtable webhook when Status field
updates. Sends to #project-updates channel with project name
and new status.

Summary: Fix BigQuery sync timeout on large datasets
Description: Was failing on datasets >1000 records. Added
pagination with 500-record batches. Also increased timeout
from 30s to 120s.

Summary: Update Autodesk MCP server to handle rate limits
Description: Was getting 429 errors during bulk operations.
Added exponential backoff with max 3 retries.
```

**Bad examples**:
```
Summary: updates           # Too vague
Summary: fixed stuff       # What stuff?
Summary: work in progress  # Don't commit WIP to main
```

### When to Use Sandbox vs. Shared Tooling

**Use sandbox when**:
- "I want to try something but I'm not sure it'll work"
- "This might break existing stuff"
- "I'm learning how this tool works"
- "This is a big change that needs iteration"

**Use shared-tooling directly when**:
- "This is a small, safe addition"
- "I'm updating documentation"
- "I'm fixing a bug in known-working code"
- "I'm confident in this change"

**When in doubt**: Use sandbox. It's easy to move code over; it's annoying to untangle a broken shared-tooling.

### When to Create a Branch

**Create a branch when**:
- Working on a feature that will take multiple commits
- Making changes you want partner review on
- Trying an alternative approach to something that exists
- Multiple people might edit the same files

**Branch naming**:
```
add-slack-integration
fix-bigquery-timeout
experiment-new-auth-flow
partner-a-refactor-n8n
```

### How to Review Your Partner's Work

When your partner creates a Pull Request:

```
1. Go to GitHub (website)
2. Navigate to the Pull Request
3. "Files changed" tab shows everything modified
4. Read through the changes
5. If questions: add comments on specific lines
6. If concerns: "Request changes" with explanation
7. If good: "Approve" and merge
```

Even if you don't formally review, skim commits regularly to understand what's changing.

### When Something Breaks

**If you know what commit broke it**:
```
1. Open GitHub Desktop
2. History tab
3. Right-click the breaking commit
4. "Revert Changes in Commit"
5. This creates a new commit that undoes the change
6. Push
```

**If you don't know what broke it**:
```
1. History tab — look at recent commits
2. Check commit messages for clues
3. Click each commit to see what changed
4. Find the likely culprit
5. Revert that commit (or fix manually)
```

**If everything is messed up** (nuclear option):
```
1. Don't panic
2. All your history is safe on GitHub
3. You can reset your local copy to match GitHub:
   - In GitHub Desktop: Branch → Discard all changes
   - Or delete local folder and re-clone
```

### Capturing Learnings

Every time you discover something useful, write it down:

**Create a file in** `shared-knowledge/learnings/` with format:
`YYYY-MM-DD-topic.md`

**Template**:
```markdown
# [Topic] — What We Learned

## Context
What were you trying to do?

## The Learning
What did you discover?

## Details
Specifics, code snippets, configuration examples.

## Gotchas
What surprised you? What would bite someone else?

## Related
Links to related learnings, external docs.
```

**Examples**:
- `2026-02-01-airtable-rate-limits.md`
- `2026-02-03-n8n-error-handling-patterns.md`
- `2026-02-05-bigquery-streaming-vs-batch.md`

This is your institutional memory. In six months, when you hit the same problem, you'll have the answer.

---

## 9. Sharing Claude Context and Learnings

### Using Claude Projects (Teams Feature)

Claude Projects let you create shared context that persists across conversations.

**Create a project for your operational system**:
```
1. Go to claude.ai
2. Create new Project: "Company Operations Stack"
3. Add team member access
4. Both partners can now work in this Project
```

**Add Project Knowledge**:
```
Click "Add content" in the Project:
- Upload your system architecture overview
- Add Airtable schema documentation
- Include key decisions from shared-knowledge/decisions/
- Add troubleshooting guides
```

Now every conversation in this Project has this context. You can say "add a new field to the Projects schema" and Claude knows your schema.

### What Carries Over vs. What Doesn't

**Carries over** (within a Project):
- Project Knowledge documents
- Custom instructions for the Project
- (Optionally) Previous conversations in the Project

**Does NOT carry over**:
- Conversation history from different Projects
- Local file context from Desktop/CLI sessions
- MCP server states

### Exporting Useful Claude Conversations

When you have a Claude conversation with valuable learnings:

```
1. In Claude, conversation menu → "Export"
2. Save as markdown
3. Move to: shared-knowledge/context/conversations/
4. Rename to something descriptive
5. Commit and push
```

Now your partner can read the full conversation and understand your thought process.

### Sharing Custom Skills and Prompts

**Claude Skills** (reusable capabilities):

Location: `shared-tooling/claude/skills/`

Example skill file — `update-project-status.md`:
```markdown
# Update Project Status

When asked to update a project status:

1. Confirm the project name and new status with the user
2. Use the Airtable MCP server to find the project record
3. Update the Status field
4. Trigger a Slack notification to #project-updates
5. Confirm the update to the user

Required context:
- Airtable base ID: appXXXXX
- Table name: Projects
- Status field options: Planning, Active, On Hold, Complete
```

**Sharing Effective Prompts**:

Create: `shared-tooling/claude/prompts/`

Example — `n8n-workflow-creation.md`:
```markdown
# Prompt: Create N8N Workflow

When asking Claude to create an N8N workflow, include:

1. Trigger type (webhook, schedule, manual, etc.)
2. Data sources and destinations
3. Any transformations needed
4. Error handling requirements
5. Where to save the output JSON

Example:
"Create an N8N workflow that:
- Triggers on Airtable webhook when a record is created in 'Leads' table
- Enriches the lead with Clearbit data
- Creates a Slack notification in #new-leads
- Logs to BigQuery leads_log table

Save the workflow JSON to n8n/workflows/lead-enrichment.json"
```

---

## 10. The Bigger Picture

### Why This System Matters

**Institutional Knowledge**:
Everything you learn is captured. Not in someone's head, not in random notes — in a structured, searchable, version-controlled system.

When you're trying to remember how that integration works, the answer is in `shared-knowledge/how-it-works/`.

When you're debugging an issue you've seen before, check `shared-knowledge/troubleshooting/`.

When you need to explain why you chose N8N over Zapier, it's in `shared-knowledge/decisions/`.

**Collaboration Without Collision**:
- Sandboxes let you experiment safely
- Branches let you try alternatives
- Pull requests let you review before merging
- Git history shows who changed what and when

You're never worried about overwriting each other's work or breaking something the other partner depends on.

**Backtracking Capability**:
Every commit is a restore point. Not just "the last good version" but every state your system has ever been in.

Something worked yesterday but not today? Compare the commits. See exactly what changed.

Made a change that broke production? Revert to the commit before.

**Acquirable Infrastructure**:
If your company were acquired tomorrow, the buyer would inherit:
- Complete source code and configurations
- Full history of how it evolved
- Documentation of how everything works
- Explanations of why decisions were made
- Troubleshooting guides for common issues

They wouldn't just get logins — they'd get *understanding*.

**Onboarding Future Team Members**:
When you hire your first developer (or another operations person):
- Clone the repos
- Read `shared-knowledge/` to understand the system
- Look at `shared-tooling/` to see how it works
- Contribute within their first week

No "tribal knowledge." No "ask so-and-so, they know how this works."

### The Long View

You're not just building workflows and integrations. You're building:

1. **A system that documents itself** through commit history
2. **A knowledge base that grows** as you learn
3. **An operational foundation** that can scale with the company
4. **An asset with real value** — not just "it works" but "here's exactly how and why it works"

Most startups lose institutional knowledge when people leave or when the company grows. You're building in a way that preserves and compounds that knowledge.

### Getting Started Checklist

**First-time setup** (do once per partner):

- [ ] Install GitHub Desktop (desktop.github.com)
- [ ] Sign in with your GitHub account
- [ ] Install Claude Desktop app
- [ ] Install Claude Code CLI (if using terminal)
- [ ] Create `~/Company/` folder
- [ ] Clone all repos into `~/Company/`
- [ ] Open `~/Company/shared-knowledge/` in Obsidian
- [ ] Configure MCP servers using template in `shared-tooling/claude/configs/`
- [ ] Test: make a small edit, commit, push, have partner pull

**Daily flow**:

- [ ] Morning: Open GitHub Desktop, fetch/pull all repos
- [ ] Work: Build using Claude Desktop or CLI
- [ ] Commit: When something works, commit with clear message
- [ ] Push: Share to GitHub promptly
- [ ] Document: Capture learnings in shared-knowledge
- [ ] End of day: Verify all work is committed and pushed

**Weekly habits**:

- [ ] Review partner's commits — what did they build?
- [ ] Review shared-knowledge — anything new to learn?
- [ ] Clean up sandbox — delete or promote experiments
- [ ] Update how-it-works docs if system changed

---

## Quick Reference Cards

### File Location Cheat Sheet

| What | Where | In Git? |
|------|-------|---------|
| Obsidian notes | `~/Company/shared-knowledge/` | Yes |
| N8N workflows | `~/Company/shared-tooling/n8n/workflows/` | Yes |
| MCP server code | `~/Company/shared-tooling/mcp-servers/` | Yes |
| Cloud functions | `~/Company/shared-tooling/cloud-functions/` | Yes |
| Project Claude config | `~/Company/shared-tooling/.claude/` | Yes |
| My experiments | `~/Company/my-sandbox/` | Yes (own repo) |
| Temporary junk | `~/Company/scratch/` | No |
| Desktop Claude config | `~/Library/Application Support/Claude/` | No |
| CLI Claude config | `~/.claude/` | No |
| API keys, credentials | Environment variables or local config | No |

### Git Commands in Plain English

| I want to... | In GitHub Desktop... |
|--------------|---------------------|
| Get partner's latest work | Fetch origin → Pull origin |
| Save my current work | Review changes → Write message → Commit |
| Share my commits | Push origin |
| Try something risky | Current Branch → New Branch |
| Propose changes for review | Push branch → Create Pull Request |
| Undo my uncommitted changes | Right-click file → Discard Changes |
| Undo a committed change | History → Right-click commit → Revert |
| See what changed | Changes tab (current) or History tab (past) |

### Claude Mode Quick Reference

| Mode | Can Read Files? | Can Write Files? | Can Execute? | MCP Servers? |
|------|----------------|------------------|--------------|--------------|
| Web (claude.ai) | Upload only | No | No | No |
| Desktop Chat | No | No | No | No |
| Desktop Cowork | Yes (shared) | No | No | No |
| Desktop Code | Yes | Yes | Yes | Yes |
| CLI | Yes | Yes | Yes | Yes |

---

*This document lives at: `shared-knowledge/operating-manual.md`*
*Last updated: 2026-02-01*
*Version: 1.0*
