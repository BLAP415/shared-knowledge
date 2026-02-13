# NNMG Dev Environment SOP

Standard operating procedure for local development environment and git workflow. Applies to all team members working on Northern Nights / FaaST platform code.

**Last updated:** 2026-02-12
**Maintainer:** Andrew Borgelt

---

## 1. Local Folder Structure

Everything lives in `~/Dev/`. No exceptions. No project folders anywhere else in `~`.

```
~/Dev/
├── nn-data-platform/          # Main platform repo (fork → upstream)
├── NorthernNights-Knowledge/  # Team documentation repo
├── shared-knowledge/          # Team SOPs and operating manual
├── shared-tooling/            # Reusable tools and configs
├── AB-Knowledge/              # Andrew — personal checkpoints (private)
├── FaaST/                     # OntosCo platform repo
└── mcp-files/                 # Credentials and API keys (NEVER in git)
```

### Rules

1. **One folder per repo, named exactly like GitHub.** No renaming, no suffixes like `-old`, `-v2`, `-copy`.
2. **Never clone the same repo twice.** Use git branches for experiments, not folder copies.
3. **No loose project files in `~`, `~/Desktop/`, or `~/Downloads/`.** If it's NN-related, it has a home in one of the folders above.
4. **Before running `mkdir ~/Something-New/`, stop.** Ask: does this belong inside an existing repo? 90% of the time, yes.

---

## 2. GitHub Repository Map

### Upstream (Peter)

| Repo | GitHub | Purpose |
|------|--------|---------|
| `nn-data-platform` | `ultraknock/nn-data-platform` | Source of truth for all platform code |
| `NorthernNights-Knowledge` | `ultraknock/NorthernNights-Knowledge` | Team documentation |
| `FaaST` | `OntosCo/FaaST` | Festival-as-a-Service platform |

### Andrew (Fork + Personal)

| Repo | GitHub | Purpose |
|------|--------|---------|
| `nn-data-platform` | `BLAP415/nn-data-platform` | Andrew's fork — PRs go to ultraknock |
| `AB-Knowledge` | `BLAP415/AB-Knowledge` | Personal checkpoints, notes, archive |
| `shared-knowledge` | `BLAP415/shared-knowledge` | Team SOPs (this doc lives here) |
| `shared-tooling` | `BLAP415/shared-tooling` | Reusable tools and Airtable configs |

### Not on GitHub

| Folder | Why |
|--------|-----|
| `~/Dev/mcp-files/` | Contains API keys, service account JSON, tokens. Never committed to any repo. |

---

## 3. Git Workflow

### nn-data-platform (Fork Model)

This is the only repo that uses a fork workflow. Everything else pushes directly.

```
ultraknock/nn-data-platform    ← upstream (Peter's — source of truth)
        ↑ Pull Request
BLAP415/nn-data-platform       ← origin (Andrew's fork)
        ↑ git push
~/Dev/nn-data-platform/        ← local
```

**Daily sync:**
```bash
git fetch upstream
git rebase upstream/main
```

**Feature work:**
```bash
git checkout -b feat/rfid-research
# ... work ...
git add <files>
git commit -m "feat: add RFID hardware evaluation"
git push origin feat/rfid-research

# Open PR to Peter's upstream
gh pr create --repo ultraknock/nn-data-platform \
  --title "feat: RFID hardware evaluation" \
  --body "..."
```

### All Other Repos (Direct Push)

For `AB-Knowledge`, `shared-knowledge`, `shared-tooling` — push directly to `main`:

```bash
git add <files>
git commit -m "docs: update operating manual"
git push origin main
```

---

## 4. Branch Naming

| Type | Pattern | Example |
|------|---------|---------|
| Feature | `feat/scope-description` | `feat/rfid-research` |
| Bug fix | `fix/scope-description` | `fix/tixr-sync-dedup` |
| Docs | `docs/scope-description` | `docs/rfid-requirements` |
| Audit | `audit/scope-description` | `audit/data-platform-review` |
| Chore | `chore/scope-description` | `chore/cleanup-stale-scripts` |

**Rule:** Never work directly on `main` in `nn-data-platform`. Always branch, always PR.

---

## 5. Commit Messages

Use conventional prefixes:

```
feat:     New feature or capability
fix:      Bug fix
docs:     Documentation only
chore:    Maintenance, cleanup, dependencies
refactor: Code restructuring (no behavior change)
```

**Format:**
```
feat: short description (imperative mood, <72 chars)

Optional body explaining WHY, not WHAT.

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>   ← if Claude helped
```

---

## 6. Where Things Go

### New platform code (scripts, cloud functions, RFID, etc.)

→ `nn-data-platform/` in the appropriate subfolder

| Content | Subfolder |
|---------|-----------|
| Cloud Functions | `cloud-functions/<function-name>/` |
| Pipeline scripts | `scripts/` |
| Architecture docs | `docs/architecture/` |
| Runbooks | `docs/runbooks/` |
| Research & specs | `docs/<topic>/` (e.g., `docs/rfid/`) |
| MCP servers | `mcp-servers/<server-name>/` |
| Airtable extensions | `airtable-apps/<app-name>/` |
| SQL | `sql/` |

### Personal work notes & checkpoints

→ `AB-Knowledge/Checkpoints/` using naming convention: `YY-MM-DD-APP-description.md`

### Team SOPs & operating procedures

→ `shared-knowledge/`

### Loose docs (JDs, contracts, meeting transcripts)

→ `AB-Knowledge/NN26 Docs/` — NOT loose in `~`

### Credentials & API keys

→ `~/Dev/mcp-files/credentials/` — NEVER in any git repo

---

## 7. Credential Management

All secrets live in `~/Dev/mcp-files/credentials/`:

```
mcp-files/
└── credentials/
    ├── airtable-api-key.txt
    ├── slack-bot-token.txt
    ├── tixr-bigquery-service-account.json
    ├── nn-sales-analytics-*.json        # GCP service account
    ├── gcp-oauth.keys.json
    ├── agent_gmail_credentials.json
    ├── sheets-token.json
    ├── aps-tokens.json
    ├── N8N-api-key.txt
    ├── .gdrive-server-credentials.json
    └── gmail/
        └── token.json
```

### Rules

- **Never hardcode credentials** in scripts. Use `os.path.expanduser()` or env vars.
- **Never commit credentials.** Verify `.gitignore` covers `credentials/`, `.env`, `*.json` key files.
- **One canonical location.** Don't copy credential files into project folders. Reference them from `mcp-files/credentials/`.

---

## 8. File Naming Convention

All project files follow: `YY-MM-DD-APP-description[-seq].ext`

**App codes:**

| Code | System |
|------|--------|
| AT | Airtable |
| BQ | BigQuery |
| QBO | QuickBooks |
| N8N | n8n |
| GML | Gmail |
| SLK | Slack |
| MCP | MCP Servers |
| SYS | System / Infra |
| CLD | Claude |
| OBS | Obsidian |
| GH | GitHub |
| TXR | TIXR |
| APS | Autodesk |
| GDR | Google Drive |
| GCP | Google Cloud |
| FF | Fireflies |
| CHR | Chrome |

**Examples:**
- `26-02-12-SYS-rfid-research.md`
- `26-02-09-AT-master-tags-baseline.json`
- `26-01-27-QBO-AT-reconciliation.py`

---

## 9. Keeping It Clean

### Weekly check (2 minutes)

```bash
# Are there any NN-related folders outside ~/Dev/?
ls -d ~/*/ | grep -v -E '(Library|Desktop|Documents|Downloads|Movies|Music|Pictures|Public|Applications|google-cloud-sdk)'
```

If anything shows up that's not a standard macOS folder, it probably belongs in `~/Dev/`.

### Before deleting any folder

Run this to check for files that don't exist elsewhere:

```bash
python3 -c "
import os
dev_files = set()
for root, dirs, files in os.walk(os.path.expanduser('~/Dev')):
    dirs[:] = [d for d in dirs if d not in {'.git','node_modules','__pycache__','.venv'}]
    for f in files:
        if f != '.DS_Store': dev_files.add(f)

target = os.path.expanduser('~/FOLDER_TO_CHECK')
for root, dirs, files in os.walk(target):
    dirs[:] = [d for d in dirs if d not in {'.git','node_modules','__pycache__','.venv','__MACOSX'}]
    for f in files:
        if f not in dev_files and f != '.DS_Store':
            print(f'ORPHAN: {os.path.join(root, f)}')
"
```

If no orphans print, safe to delete. If orphans exist, move them first.

---

## 10. Onboarding a New Team Member

1. Grant GitHub access to `ultraknock/nn-data-platform` and `BLAP415/shared-knowledge`
2. Have them create `~/Dev/` and clone repos into it
3. Copy credential templates from `.env.example` files — they supply their own API keys
4. Point them to this SOP and `shared-knowledge/operating-manual.md`
5. First task: make a small edit, commit, push, verify it shows on GitHub

---

*This SOP lives at: `shared-knowledge/dev-environment-sop.md`*
*Related: `shared-knowledge/operating-manual.md` (general concepts)*
