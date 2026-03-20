# Day 39 – What is CI/CD?

> **Goal:** Understand why CI/CD exists and what it actually does — before writing a single pipeline.

---

## Task 1 – The Problem (Why CI/CD Exists)

### The Scenario

Imagine 5 developers all working on the same codebase. They each work separately for 2 weeks, then on Friday afternoon everyone pushes their code and someone manually copies files to the production server.

### What Goes Wrong?

| Problem | What Actually Happens |
|---|---|
| **Integration Hell** | Dev A changed a function Dev B depends on. Both worked locally. Together they crash production. Nobody knows why. |
| **"It works on my machine"** | Dev's laptop: Python 3.11. Production: Python 3.7. Staging: Python 3.9. Same code, three different behaviors. |
| **No rollback plan** | Production breaks at 5pm Friday. Nobody knows which of the 5 manual deploys caused it, or how to undo just that one change. |
| **Manual = forgotten steps** | A human copying files can forget to restart a service, deploy the wrong branch, or overwrite a config file. A script never forgets. |
| **Features pile up** | Because deploys are risky, teams avoid doing them. Features sit untested for weeks. The bigger the release, the bigger the risk. |

### "It Works on My Machine" — Why It's a Real Problem

When a developer says this, they're describing an environment mismatch. Their local machine has:
- A different OS version or runtime version
- Different environment variables set
- Different package versions installed
- Different file paths and permissions

The code behaves differently because it is running in a fundamentally different environment. CI/CD solves this by running your code in a **controlled, reproducible environment** (a clean Ubuntu VM) every single time — the same environment for every developer, every time.

### How Often Can a Team Deploy Manually?

Without CI/CD: **1–2 times per day at most**, and that is considered risky. Every deploy requires a human present, watching, ready to roll back.

With CI/CD:
- Netflix: 100+ deploys per day
- Amazon: 1 deploy every 11.6 seconds
- Etsy: 50+ deploys per day

They achieve this because every deploy is automated, tested, and easily reversible.

---

## Task 2 – CI vs CD vs CD

### Continuous Integration (CI)

Developers merge their code into a shared branch **multiple times a day**. Every single merge automatically triggers a test run. The "integration" means integrating your code with everyone else's — constantly, not once every two weeks.

**What it catches:** broken tests, merge conflicts, code style violations, type errors — within minutes of pushing.

**Real-world example:** A developer on the React team pushes a bug fix. Within 3 minutes, GitHub Actions runs 4,000 unit tests across 10 Node.js versions. If anything breaks, the developer gets an email before they've even made coffee.

---

### Continuous Delivery

Everything CI does, plus: after tests pass, the code is automatically **packaged and made ready to deploy**. A human still makes the final decision to push to production — but that decision takes one click, not an hour of preparation.

The word "delivery" means the software is delivered to the doorstep. A human decides when to let it in.

**Real-world example:** A startup's pipeline automatically builds a Docker image, pushes it to staging, runs smoke tests, and sends a Slack message saying "v1.4.2 is ready to deploy to production." A developer clicks one button to release it.

---

### Continuous Deployment

No human approval. If all tests pass, the code goes **straight to production automatically**. Every commit that passes tests is live within minutes.

This requires extremely high confidence in your test suite and strong monitoring/rollback capability.

**Real-world example:** Netflix. Every code change that passes their automated test suite gets deployed to production automatically. They use feature flags to control which users see new features, so they can roll back without a redeploy.

### The Spectrum

```
Manual Deploy          CI              Continuous Delivery      Continuous Deployment
     |_________________|___________________|__________________________|
   (risky,           (auto-test,        (auto-test + auto-build,    (fully automated,
    slow)             notify)            human approves prod)         no human needed)
```

Most companies aim for **Continuous Delivery** — the human approves the final production step.

---

## Task 3 – Pipeline Anatomy

A pipeline is a series of automated steps that run every time code changes.

### The 6 Parts

**Trigger**
The event that starts the pipeline. Nothing runs without a trigger.
- `push` — when code is pushed to a branch
- `pull_request` — when a PR is opened or updated
- `schedule` — cron job (e.g. every night at 2am)
- `workflow_dispatch` — manual button click in GitHub UI

**Stage**
A logical phase of work. Stages run in order. If Stage 1 (Test) fails, Stage 2 (Build) never starts. This prevents broken code from progressing further.
- Common stages: Test → Build → Deploy

**Job**
A unit of work inside a stage. Multiple jobs in one stage can run in parallel on different runners, saving time.
- Example: "unit-tests" and "security-scan" both in the Test stage, running at the same time

**Step**
A single command inside a job. Steps run sequentially — one after another. If one step fails, the job stops.
- Example: `pip install -r requirements.txt` → `pytest` → `coverage report`

**Runner**
The actual machine (VM) that executes the job. Think of it as a fresh computer that gets created for your job, runs it, and is destroyed after.
- GitHub provides: `ubuntu-latest`, `windows-latest`, `macos-latest`
- You can self-host runners on your own servers

**Artifact**
A file produced by a job that gets saved and passed to later stages. Compiled binaries, Docker images, test reports, APK files.
- Example: Build stage produces `myapp:1.4.2` Docker image → Deploy stage pulls and runs it

### How They Nest

```
Pipeline
  └── Stage: Test
        ├── Job: unit-tests          (runs in parallel)
        │     ├── Step: pip install
        │     ├── Step: pytest
        │     └── Step: coverage report
        └── Job: lint                (runs in parallel)
              └── Step: flake8 .
  └── Stage: Build
        └── Job: docker-build
              ├── Step: docker build -t myapp .
              ├── Step: docker tag myapp:latest registry/myapp:v1.4.2
              └── Step: docker push  ← Artifact produced
  └── Stage: Deploy
        └── Job: deploy-staging
              ├── Step: SSH to staging server
              ├── Step: docker pull registry/myapp:v1.4.2
              ├── Step: docker run new container
              └── Step: curl /health to verify
```

---

## Task 4 – Pipeline Diagram

**Scenario:** Developer pushes code → tested → built into Docker image → deployed to staging

```
┌─────────────────────────────────────────┐
│               TRIGGER                   │
│  git push to main / PR merged           │
└─────────────────────┬───────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────┐
│          STAGE 1 — TEST                 │
│  Runner: ubuntu-latest (fresh VM)       │
│                                         │
│  Step 1: actions/checkout               │
│  Step 2: pip install -r requirements    │
│  Step 3: flake8 . (linter)             │
│  Step 4: pytest (unit tests)            │
│  Step 5: coverage report               │
└─────────────────────┬───────────────────┘
          Pass        │        Fail
                      │          └──► Pipeline stops
                      │               Dev gets email/Slack
                      ▼
┌─────────────────────────────────────────┐
│          STAGE 2 — BUILD                │
│  Produces artifact: Docker image        │
│                                         │
│  Step 1: docker build -t myapp .        │
│  Step 2: docker tag myapp registry/     │
│          myapp:$GITHUB_SHA              │
│  Step 3: docker push to registry        │
└─────────────────────┬───────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────┐
│       STAGE 3 — DEPLOY TO STAGING       │
│  Uses artifact from Stage 2             │
│                                         │
│  Step 1: SSH into staging server        │
│  Step 2: docker pull registry/myapp     │
│  Step 3: docker stop old container      │
│  Step 4: docker run new container       │
│  Step 5: curl http://staging/health     │
│  Step 6: notify team on Slack           │
└─────────────────────┬───────────────────┘
                      │
                      ▼
            ✅ Staging server updated
               Team notified in Slack
```

### In GitHub Actions YAML (preview of Day 40+)

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: pip install -r requirements.txt
      - run: pytest

  build:
    needs: test               # only runs if test passes
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: docker build -t myapp .
      - run: docker push registry/myapp:latest

  deploy:
    needs: build              # only runs if build passes
    runs-on: ubuntu-latest
    steps:
      - run: ssh user@staging "docker pull && docker run..."
```

---

## Task 5 – Exploring in the Wild

### How to Find a Real Pipeline

1. Go to any popular repo on GitHub (FastAPI, React, Kubernetes)
2. Click the `.github/` folder
3. Click `workflows/`
4. Open any `.yml` file

### What to Look For

```yaml
on:                         # ← TRIGGER — what starts the pipeline?
  push:
    branches: [main]
  pull_request:

jobs:                       # ← JOBS — how many? what are they named?
  test:
    runs-on: ubuntu-latest  # ← RUNNER — what machine?
    steps:
      - uses: actions/checkout@v4   # ← STEPS — what does each one do?
      - run: pytest
```

### My Notes — Repo I Explored

**Trigger — what starts it?**
```
Write your answer here...
```

**How many jobs does it have?**
```
Write your answer here...
```

**What does it do? (your best guess)**
```
Write your answer here...
```

**Anything you didn't understand?**
```
Write your answer here...
```

### FastAPI's CI as a Reference Example

FastAPI's `test.yml` is one of the clearest examples to study:

| Part | What it does |
|---|---|
| Trigger | Push or PR to any branch — every single contribution gets tested |
| Jobs | 3 jobs: `test`, `lint` (mypy), `coverage` |
| Matrix | Tests run on Python 3.8, 3.9, 3.10, 3.11 simultaneously |
| Steps | `checkout` → `setup-python` → `pip install` → `pytest --cov` → upload coverage |
| Why it matters | If code passes on 3.8 but fails on 3.11, the matrix tells you exactly which version broke |

---

## Quick Reference – CI/CD in One Page

```
PROBLEM:   Manual deploys → integration hell → "works on my machine" → Friday fear
SOLUTION:  Automate the boring parts — test, build, deploy — on every push

CI:   push → auto-test → notify (green or red)
CD:   push → auto-test → auto-package → [human clicks deploy]
CD++: push → auto-test → auto-package → auto-deploy to prod

PIPELINE PARTS:
  Trigger  → what starts it (push, PR, schedule)
  Stage    → logical phase (Test, Build, Deploy)
  Job      → unit of work inside a stage (can run in parallel)
  Step     → single command inside a job (run in order)
  Runner   → the VM that executes the job
  Artifact → file produced by a job, passed to next stage
```

---
