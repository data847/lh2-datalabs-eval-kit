# 🔍 Repository Evaluation Kit — User Guide

A simple, step-by-step guide to evaluate your GitHub or GitLab projects. No programming experience required.

---

## What Does This Tool Do?

This tool scans your code projects and generates a **quality report** for each one — covering things like:

- How many files, tests, and lines of code exist
- How healthy the development process is (commits, pull requests, contributors)
- Whether CI/CD pipelines and test frameworks are set up
- Whether the code may be AI-generated
- Whether the project is likely open-source

You can scan **one project at a time**, or **all projects you have access to** in one go.

---

## 📋 One-Time Setup (Do This First)

You only need to follow these steps **once** on your computer.

### Step 1 — Check that Python and Git are installed

Open your **Terminal** app:
- **Mac**: Press `Cmd + Space`, type `Terminal`, hit Enter
- **Windows**: Press `Win`, type `cmd`, hit Enter

Then type these two commands (one at a time, pressing Enter after each):

```
python3 --version
```
```
git --version
```

✅ If you see version numbers (e.g. `Python 3.12.0` and `git version 2.39.0`), you're good — skip to Step 2.

❌ If either says "not found":
- Install Python from https://www.python.org/downloads/ (pick 3.10 or higher)
- Install Git from https://git-scm.com/downloads

### Step 2 — Download this tool

Run this in your Terminal:

```
git clone <repo-url>
```

> 📌 Replace `<repo-url>` with the actual link you were given for this tool.

Then go into the downloaded folder:

```
cd lh2-datalabs-eval-kit
```

### Step 3 — Install the required software

Run these three commands one at a time:

```
python3 -m venv .venv
```
```
source .venv/bin/activate
```
```
pip install -r requirements.txt
```

> **Windows users**: Replace the second command with `.venv\Scripts\activate`

### Step 4 — Get your access token

A token is like a special password that lets this tool read your projects.

#### If your projects are on **GitHub**:

1. Open https://github.com/settings/tokens in your browser
2. Click **"Generate new token"** → pick **"Classic"**
3. Give it a name (e.g. `eval-kit`)
4. Tick these checkboxes:
   - ☑️ **repo**
   - ☑️ **read:org**
5. Click **"Generate token"** at the bottom
6. **Copy the token right away** (starts with `ghp_`) — you won't see it again!

#### If your projects are on **GitLab**:

1. Open https://gitlab.com/-/user_settings/personal_access_tokens in your browser
   *(Self-hosted? Use `https://your-company-gitlab/-/user_settings/personal_access_tokens`)*
2. Give it a name (e.g. `eval-kit`)
3. Tick these checkboxes:
   - ☑️ **read_api**
   - ☑️ **read_repository**
   - ☑️ **read_user**
4. Click **"Create personal access token"**
5. **Copy the token right away** (starts with `glpat-`)

### Step 5 — Save your token

This way you won't have to paste it every time you run the tool.

In your Terminal (make sure you're still inside the `lh2-datalabs-eval-kit` folder), run **one** of these:

**GitHub users:**
```
echo "GITHUB_TOKEN=ghp_PASTE_YOUR_TOKEN_HERE" > .env
```

**GitLab users:**
```
echo "GITLAB_TOKEN=glpat-PASTE_YOUR_TOKEN_HERE" > .env
```

> ⚠️ Replace `ghp_PASTE_YOUR_TOKEN_HERE` (or `glpat-...`) with the actual token you copied.

✅ **Setup is complete!** You only had to do this once.

---

## ⚠️ Every Time You Open a New Terminal

Before running any evaluation commands, you must first navigate to the tool folder and activate it:

```
cd lh2-datalabs-eval-kit
source .venv/bin/activate
```

You'll know it's active when you see `(.venv)` at the start of your Terminal line.

---

## 🚀 Evaluate ONE Project

Use this when you know which project you want to check.

> 📌 Your project name is the `owner/repo` you see in the URL of your project. For example, if the URL is `github.com/acme-corp/billing-service`, the project name is `acme-corp/billing-service`.

### GitHub project

```
python repo_evaluator.py acme-corp/billing-service --json --output results.json
```

### GitLab project

```
python repo_evaluator.py gitlab:acme-corp/billing-service --platform gitlab --json --output results.json
```

### Faster version (skips slower AI-powered checks)

```
python repo_evaluator.py acme-corp/billing-service --json --output results.json --skip-f2p --skip-quality-checks --skip-taxonomy --skip-pr-rubrics
```

### Where are my results?

After it finishes, you'll have:

| File | What it is |
|------|------------|
| `results.json` | The full detailed report |
| `billing-service.csv` | A spreadsheet-friendly version you can open in Excel or Google Sheets |

---

## 🚀 Evaluate ALL Your Projects at Once

Use this when you want to scan **every project** across all your organizations.

### Step 1 — Preview (see what will be scanned, nothing is changed)

**GitHub:**
```
python run_all_repos.py --dry-run
```

**GitLab:**
```
python run_all_repos.py --platform gitlab --dry-run
```

You'll see a list like this:

```
======================================================================
  📋  REPOSITORY INVENTORY [GITHUB] — 47 repo(s) across 3 org(s)
======================================================================

  🏢 acme-corp  (25 repos)
  ────────────────────────────────────────
    • acme-corp/billing-service  [🔒 private, Python]
    • acme-corp/web-app          [🔒 private, TypeScript]
    • acme-corp/mobile-app       [🔒 private, Kotlin]
    ...

  🏢 acme-labs  (12 repos)
  ────────────────────────────────────────
    • acme-labs/ml-pipeline  [🔒 private, Python]
    ...
```

👀 **Look through the list.** If it looks right, continue to Step 2.

### Step 2 — Run the evaluation

**GitHub:**
```
python run_all_repos.py --run
```

**GitLab:**
```
python run_all_repos.py --platform gitlab --run
```

**Faster version (recommended for large organizations):**
```
python run_all_repos.py --run --evaluator-args "--skip-f2p --skip-quality-checks --skip-taxonomy --skip-pr-rubrics"
```

This will take a while — the tool evaluates each project one by one (up to 4 in parallel).

### Step 3 — Find your results

Everything is saved in a folder called `eval_results/`:

```
eval_results/
├── _summary.json                ← overview of the entire run
├── acme-corp/
│   ├── billing-service/
│   │   ├── billing-service.json ← detailed report
│   │   └── billing-service.csv  ← spreadsheet version
│   ├── web-app/
│   │   ├── web-app.json
│   │   └── web-app.csv
│   └── ...
└── acme-labs/
    └── ...
```

### Step 4 (Optional) — Combine all results into one spreadsheet

```
python consolidate_output.py
```

This creates a single `output/combined.csv` file you can open in **Excel** or **Google Sheets**.

---

## 🎯 Common Scenarios — Just Copy and Paste

### "I want to evaluate just one specific organization"

```
python run_all_repos.py --run --org acme-corp
```

### "I also want to include my personal repos"

```
python run_all_repos.py --run --include-user-repos
```

### "I only care about private repos"

```
python run_all_repos.py --run --visibility private
```

### "I want to skip a specific project"

```
python run_all_repos.py --run --exclude-repo acme-corp/old-project
```

### "I use self-hosted GitLab (not gitlab.com)"

```
python run_all_repos.py --platform gitlab --run --gitlab-url https://gitlab.mycompany.com
```

---

## 📊 What's In The Report?

Each project's report tells you:

| Item | What it means |
|------|---------------|
| **Primary language** | The main programming language (e.g. Python, Java, TypeScript) |
| **Source files / Test files** | How many code files and test files the project has |
| **Lines of code** | Total size of the project |
| **CI/CD detected** | Whether automated build/deploy pipelines are set up |
| **Test frameworks** | What testing tools are used (e.g. pytest, jest, junit) |
| **Total commits** | How many code changes have been made over the project's lifetime |
| **Contributors** | How many people have worked on the project |
| **PR acceptance rate** | What percentage of pull requests passed quality checks |
| **Open source likelihood** | Low / Medium / High — whether the project appears to be open source |
| **AI risk level** | Low / Medium / High — whether the code shows signs of being AI-generated |

---

## ❓ Something Not Working?

| Problem | Solution |
|---------|----------|
| **"No GitHub token provided"** | Your `.env` file is missing or has the wrong token. Redo [Step 5](#step-5--save-your-token) of the setup. |
| **"API rate limit exceeded"** | Your token isn't being picked up. Make sure the `.env` file is in the `lh2-datalabs-eval-kit` folder. |
| **"No repos found"** | Your token may not have the right permissions. Re-create it and make sure you tick `repo` + `read:org` (GitHub) or `read_api` (GitLab). Try adding `--include-user-repos` as well. |
| **"command not found: python"** | Try `python3` instead of `python` in all the commands above. |
| **It's running very slowly** | Use the "faster version" commands shown above — they skip the time-consuming AI analysis steps. |
| **Commands don't work after I re-open Terminal** | You need to re-activate the tool every time: run `cd lh2-datalabs-eval-kit` then `source .venv/bin/activate` |
| **GitLab: "insufficient_granular_scope"** | Your token is missing scopes. Re-create it with `read_api`, `read_repository`, and `read_user` checked. |

---

## 🧾 All Commands at a Glance

| What you want to do | Command to run |
|---|---|
| See all your GitHub repos (preview) | `python run_all_repos.py --dry-run` |
| See all your GitLab repos (preview) | `python run_all_repos.py --platform gitlab --dry-run` |
| Evaluate **all** GitHub repos | `python run_all_repos.py --run` |
| Evaluate **all** GitLab repos | `python run_all_repos.py --platform gitlab --run` |
| Evaluate all repos **(fast mode)** | `python run_all_repos.py --run --evaluator-args "--skip-f2p --skip-quality-checks --skip-taxonomy --skip-pr-rubrics"` |
| Evaluate only one organization | `python run_all_repos.py --run --org my-org-name` |
| Evaluate **one** GitHub project | `python repo_evaluator.py owner/repo --json --output results.json` |
| Evaluate **one** GitLab project | `python repo_evaluator.py gitlab:group/repo --platform gitlab --json --output results.json` |
| Evaluate one project **(fast)** | `python repo_evaluator.py owner/repo --json --output results.json --skip-f2p --skip-quality-checks --skip-taxonomy --skip-pr-rubrics` |
| Combine all CSVs into one spreadsheet | `python consolidate_output.py` |

---

*For advanced options and developer reference, see [README.md](README.md).*

*Internal tool — LH2 Tech / Datalabs*

