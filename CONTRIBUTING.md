# Contributing to CAUR Technologies

**Git Flow — GitHub Contribution Guide**
*R&D Team · v1.0 · April 2026*

---

## Table of Contents

1. [Why a Git Flow?](#1-why-a-git-flow)
2. [Branch Structure](#2-branch-structure)
3. [Complete Workflow](#3-complete-workflow)
4. [Flow Diagram](#4-flow-diagram)
5. [Essential Rules](#5-essential-rules)
6. [Admins & Reviewers](#6-admins--reviewers)

---

## 1. Why a Git Flow?

Without a shared convention, every developer works differently: branches without issues, inconsistent naming, PRs directly to the main branch. The result: conflicts, unreviewed code in production, and an unreadable history.

This document establishes a simple convention, based on what the team was already doing partially, with a few adjustments to make the workflow reliable.

---

## 2. Branch Structure

### Permanent Branches

| Branch | Role | Rules |
|--------|------|-------|
| `main` | Stable production | No direct commits. Merge from `develop` only. Tag required at every merge (`v1.x.x`). |
| `develop` | Integration — testing | Default working branch. All temporary branches are created from it and merged back via PR. |

### Temporary Branches — Naming Convention

```
type/#issue-short-description
```

| Type | Usage | Example |
|------|-------|---------|
| `feature` | New feature | `feature/#42-autopicker-ui` |
| `fix` | Bug fix | `fix/#87-credentials-path-error` |
| `test` | Test / experimentation | `test/#91-xcorr-local-subset` |
| `doc` | Documentation only | `doc/#15-update-readme` |
| `refactor` | Refactoring, cleanup | `refactor/#33-move-libs-to-lib` |
| `security` | Security patch | `security/#55-rotate-sa-key` |

---

## 3. Complete Workflow

### Step 1 — Create a GitHub Issue

> **All work starts with an Issue. No issue = no branch.**

**Option A — GitHub web interface**

1. Go to [CAUR-Technologies/xnoise → Issues → New Issue](https://github.com/CAUR-Technologies/xnoise/issues/new/choose)
2. Choose the right template: Bug Report, Feature Request, or Task
3. Fill in: clear title, description, acceptance criteria, assignee, label
4. GitHub automatically assigns a number (`#123`) — this goes in the branch name

**Option B — GitHub CLI (`gh`)**

Install the CLI if needed:

```bash
# macOS
brew install gh

# Linux / WSL
sudo apt install gh
# or:
curl -sS https://webi.sh/gh | sh
```

Authenticate once:

```bash
gh auth login
```

Create an issue interactively:

```bash
gh issue create --repo CAUR-Technologies/xnoise
```

Or in one line:

```bash
gh issue create \
  --repo CAUR-Technologies/xnoise \
  --title "feat: add local Dask mode to xcorr processor" \
  --body "Run xcorrelation locally without Coiled for dev/test." \
  --label "feature" \
  --assignee "@me"
```

The CLI returns the issue URL and number — use that number in your branch name.

---

### Step 2 — Create the Branch from `develop`

Always start from an up-to-date `develop`:

```bash
git checkout develop
git pull origin develop
git checkout -b feature/#123-short-description
```

Or with the GitHub CLI (creates the branch and links it to the issue automatically):

```bash
gh issue develop 123 --repo CAUR-Technologies/xnoise --checkout
```

---

### Step 3 — Work and Commit

Frequent commits, clear messages in English:

```bash
git add .
git commit -m "feat: add local Dask mode to xcorrelation processor"
```

**Commit message format:**

| Prefix | Usage |
|--------|-------|
| `feat:` | new feature |
| `fix:` | bug fix |
| `docs:` | documentation |
| `refactor:` | restructuring without functional change |
| `test:` | adding or modifying tests |
| `chore:` | maintenance task (deps, CI, etc.) |

---

### Step 4 — Open a Pull Request to `develop`

```bash
git push origin feature/#123-short-description
```

Then open a PR on GitHub, or via CLI:

```bash
gh pr create \
  --base develop \
  --title "#123 — Add local Dask mode" \
  --body "Closes #123. Adds local Dask execution mode to xcorr processor." \
  --reviewer oletourneau-caurtech
```

**In the PR:**
- Base branch: `develop` (never `main`)
- Title: `#123 — Short description`
- Body: what was done, how to test, screenshots if relevant
- Write `Closes #123` — GitHub closes the issue automatically on merge
- Assign a reviewer — see [Section 6](#6-admins--reviewers)

---

### Step 5 — Review and Merge into `develop`

- At least **1 approval** required before merging
- **Squash and merge** recommended (keeps history clean)
- The temporary branch is deleted after merge

---

### Step 6 — Merge `develop` into `main` (release)

When `develop` is stable and ready for a client delivery:

1. Open a PR: `develop` → `main`
2. Title: `Release vX.Y.Z`
3. Use **merge commit** (not squash — preserves full history)
4. Create a GitHub tag `vX.Y.Z` with release notes

> Only admins can merge into `main`. See [Section 6](#6-admins--reviewers).

---

## 4. Flow Diagram

```
main      ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━► (v1.0.0)  (v1.1.0)
               ↑                                              ↑
develop   ──┬──┴────────────────────────────────────────┬────┴──►
            │                                            │
feat/#42  ──┘  (from develop)          (PR → develop)  ─┘

fix/#87   ─────────────────────────────────────────────────────►
               from develop ──────────────────── PR → develop
```

---

## 5. Essential Rules

| | Rule |
|-|------|
| ✅ | Always create an Issue before starting |
| ✅ | Always start from `develop` (`git pull origin develop` before creating the branch) |
| ✅ | Name the branch with the type and issue number |
| ✅ | Open a PR to `develop` — never directly to `main` |
| ✅ | Write `Closes #123` in the PR to link the issue automatically |
| ✅ | At least 1 review before merging |
| ✅ | Tag `main` at every release (`v1.x.x`) |
| ❌ | Never commit directly to `main` or `develop` |
| ❌ | Never merge without review |
| ❌ | Never keep a branch alive too long — small branches, frequent PRs |

---

## 6. Admins & Reviewers

The following people are authorized to review PRs, approve merges to `main`, and carry out admin-level tasks on the repository. When opening a PR, assign the reviewer based on the domain of your change.

| Person | GitHub | Reviews / Approves |
|--------|--------|--------------------|
| Olivier Létourneau | [@oletourneau-caurtech](https://github.com/oletourneau-caurtech) | Pipeline, processing scripts, infrastructure, releases to `main` |
| David Shak | [@dshak](https://github.com/dshak) | Platform, API, web frontend, security, dependency changes |

Only admins can merge PRs from `develop` into `main`. All other contributors open PRs to `develop` and assign one of the above as reviewer.

---

*CAUR Technologies · Confidential — Internal Use Only · April 2026*
