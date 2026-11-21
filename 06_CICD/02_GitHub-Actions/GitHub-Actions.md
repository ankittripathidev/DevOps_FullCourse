======================================================================

# 🧣 DAY-1: GitHub Actions – Introduction & First Workflow

======================================================================

## 1. Introduction to GitHub Actions
- GitHub Actions is a CI/CD automation platform that allows developers to automate software 
development workflows directly from GitHub.

**What can we automate?**

- Running tests and code quality checks.
- Building applications and Docker images.
- Deploying applications.
- Executing workflows on push or pull requests.

### What is CI/CD?

- **CI (Continuous Integration):** Automatically validates code whenever developers push changes.
- **CD (Continuous Delivery/Deployment):** Automates software release and deployment.

---

## 2. Create Your First Repository

**Step 1:** Go to [GitHub](https://github.com) and create a new repository.

**Step 2:** Create the following directory structure:

```text
github-actions-project/
│
└── .github/
    └── workflows/
        └── hello.yml
```

**Important:**

- `.github/workflows/` stores workflow files.
- Workflow files must use `.yml` or `.yaml` extensions.

---

## 3. GitHub Actions Architecture

```text
Workflow
   |
   |---- Trigger (on)
   |
   |---- Jobs
           |
           |---- Runner
           |
           |---- Steps
                   |
                   |---- Action (uses)
                   |
                   |---- Command (run)
```

### Important Components

| Component | Description                  |
| --------- | ---------------------------- |
| `name`    | Workflow name                |
| `on`      | Workflow trigger             |
| `jobs`    | Contains jobs                |
| Job ID    | Unique job identifier        |
| `runs-on` | Runner environment           |
| `steps`   | Individual tasks             |
| `run`     | Executes shell commands      |
| `uses`    | Uses existing GitHub Actions |

---

## 4. Create Your First Workflow – Hello World

Create `hello.yml` inside `.github/workflows/`.

```yaml
# Workflow Name
name: Hello

# Workflow Trigger
on:
  workflow_dispatch:

# Jobs
jobs:
  say-hello:
    # Runner
    runs-on: ubuntu-latest

    # Steps
    steps:
      - name: Say hello to everyone
        run: echo "Hello Dosto"
```

### Explanation

1. `name` – Defines the workflow name.
2. `on` – Defines when the workflow runs.
3. `workflow_dispatch` – Allows manual execution.
4. `jobs` – Contains one or more jobs.
5. Job ID (`say-hello`) – Unique identifier of a job.
6. `runs-on` – Defines the runner environment.
7. `steps` – Contains tasks executed sequentially.
8. `run` – Executes shell commands.
9. `uses` – Executes an existing GitHub Action.

**Example of `uses`:**

```yaml
- name: Checkout repository
  uses: actions/checkout@v7
```

This action checks out your repository code into the runner.

---

## 5. Workflow Trigger – Push Event

To execute the workflow automatically whenever code is pushed:

```yaml
name: Hello

on:
  push:

jobs:
  say-hello:
    runs-on: ubuntu-latest

    steps:
      - name: Say hello to everyone
        run: echo "Hello Dosto"
```

### Common Workflow Triggers

| Trigger             | Purpose                                                        |
| ------------------- | -------------------------------------------------------------- |
| `push`              | Runs when commits are pushed to a repository                   |
| `workflow_dispatch` | Allows manually executing a workflow                           |
| `workflow_call`     | Allows one workflow to be called or reused by another workflow |
| `pull_request`      | Runs on pull request events                                    |
| `schedule`          | Runs on a cron schedule                                        |

---

## 6. Execute Your Workflow

After pushing your YAML file to GitHub:

1. Open your repository.
2. Go to **Actions**.
3. Select the `Hello` workflow.
4. Click **Run workflow**.
5. Select the branch and confirm.

### Verify Execution

Open the workflow run → `say-hello` job → `Say hello to everyone` step.

**Expected Output:**

```text
Hello Dosto
```

---

## 7. Important Difference: `run` vs `uses`

| `run`                   | `uses`                |
| ----------------------- | --------------------- |
| Executes shell commands | Uses existing actions |
| `echo "Hello"`          | `actions/checkout@v7` |
| Runs commands on runner | Reuses an action      |

---

===================================================================================

# 🚀 DAY-2: GitHub Actions – Multiple Jobs, Reusable Workflows & CI/CD Pipeline

====================================================================================

## 1. Introduction
- GitHub Actions allows us to automate tasks using workflows.

We can create:

- **Single Job Workflow:** Executes tasks inside one job.
- **Multiple Job Workflow:** Executes multiple independent or dependent jobs.
- **Reusable Workflow:** Allows one workflow to call another workflow using `workflow_call`.
- **Automated Linter:** Automatically checks code quality whenever changes are pushed.

**Note:** The examples below use GitHub-hosted runners (`ubuntu-latest`). Self-hosted runners are a separate concept.

---

## 2. Example 1: Basic Workflow

**Goal:** Run a simple Hello message manually.

```yaml
# Workflow Name
name: Hello

# Trigger On
on:
  workflow_dispatch:

# Jobs
jobs:
  say-hello:
    runs-on: ubuntu-latest
    steps:
      - name: Hello Bolo
        run: echo "Hello Dosto"
```

**Key Points:**

- `workflow_dispatch`: Manually triggers the workflow.
- `runs-on`: Specifies the runner environment.
- `steps`: Contains tasks to execute.
- `run`: Executes shell commands.

---

## 3. Example 2: Multiple Jobs CI/CD Workflow

**Goal:** Create a basic CI/CD pipeline with four jobs.

```yaml
# Workflow Name
name: CICD

# Triggers on
on:
  workflow_dispatch:

# Jobs
jobs:
  # Job.1
  code-clone:
    runs-on: ubuntu-latest
    steps:
      - name: Clone the Code
        run: echo "Cloning the Code"

  # Job.2
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Build Using Docker
        run: echo "Building Docker Image"

  # Job.3
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Test Code Quality
        run: echo "Test the Code Quality"

  # Job.4
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy on Machine
        run: echo "Deploying code on Machine"
```

**Key Points:**

- Multiple jobs execute independently by default.
- Jobs can execute in parallel.
- `needs` is used to define job dependencies.
- This example only prints messages; it does not actually clone, build, test, or deploy code.

---

## 4. Example 3: Reusable Workflows Using `workflow_call`

**Goal:** Divide a CI/CD pipeline into separate reusable workflows.

### Step 1: Create Separate Workflows

**1. code-clone.yml**

```yaml
name: CODE

on:
  workflow_call:

jobs:
  code-clone:
    runs-on: ubuntu-latest
    steps:
      - name: Code Checkout/Code Clone
        uses: actions/checkout@v7

      - name: Print Message
        run: echo "Code Cloned Successfully"
```

**2. build.yml**

```yaml
name: BUILD

on:
  workflow_call:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Build Image
        run: echo "Building Docker Image"
```

**3. test.yml**

```yaml
name: TEST

on:
  workflow_call:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Test the Code Quality
        run: echo "Testing the Code Quality"
```

**4. deploy.yml**

```yaml
name: DEPLOY

on:
  workflow_call:

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy on Machine
        run: echo "Deploying the Code"
```

### Step 2: Create Main CI/CD Workflow

**cicd.yml**

```yaml
name: CICD Pipeline

on:
  push:

jobs:
  code-clone:
    uses: ./.github/workflows/code-clone.yml

  build:
    needs: code-clone
    uses: ./.github/workflows/build.yml

  test:
    needs: build
    uses: ./.github/workflows/test.yml

  deploy:
    needs: [build, test]
    uses: ./.github/workflows/deploy.yml
```

### Key Points

- `workflow_call`: Makes a workflow reusable.
- `uses`: Calls another reusable workflow.
- `needs`: Defines dependencies between jobs.
- `needs: [build, test]`: Runs deploy only after both jobs succeed.
- Reusable workflows must be stored inside `.github/workflows/`.
- Reusable workflows are called at the job level, not inside steps.

### Execution Flow

```text
       CODE CLONE
            |
          BUILD
            |
           TEST
            |
          DEPLOY
```

**Important:** Each job gets its own runner environment. Files checked out in the `code-clone` job are not automatically available in the `build`, `test`, or `deploy` jobs. Use checkout again or pass files using artifacts when required.

---

## 5. Example 4: Automate Python Code Quality Using Ruff

**Goal:** Whenever Python files are pushed, GitHub Actions automatically runs Ruff to check code quality and formatting.

### Step 1: Create Python Application

**app.py**

```python
# This code is from https://github.com/ankittripathidev/flask-app-ec2.git

from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def hello_world():
    return render_template('index.html')

@app.route('/health')
def health():
    return 'Server is up and running'
```

### Step 2: Create GitHub Actions Workflow

**.github/workflows/linter.yml**

```yaml
name: Auto Linter

on:
  push:
    paths:
      - "**/*.py"

jobs:
  code:
    runs-on: ubuntu-latest

    steps:
      # Checkout Code
      - uses: actions/checkout@v7

      # Setup Python
      - uses: actions/setup-python@v7
        with:
          python-version: "3.13"

      # Install Ruff & Run Checks
      - name: Run Ruff Checks
        run: |
          pip install ruff
          ruff check .
          ruff format --check .
```

### What is Ruff?

Ruff is a fast Python linter and code formatter.

**Ruff checks:**

- Python code quality.
- Syntax and common coding errors.
- Import sorting.
- Code formatting (when configured).

### Important Commands

| Command                 | Purpose                                      |
| ----------------------- | -------------------------------------------- |
| `pip install ruff`      | Installs Ruff                                |
| `ruff check .`          | Checks linting errors                        |
| `ruff check . --fix`    | Automatically fixes supported linting errors |
| `ruff format .`         | Automatically formats Python files           |
| `ruff format --check .` | Checks formatting without modifying files    |

**Important:**

- `ruff check .` detects linting errors.
- `ruff check . --fix` fixes supported linting errors.
- `ruff format .` formats Python files.
- `ruff format --check .` verifies formatting without modifying files.
- If either CI check fails, the GitHub Actions job fails.

**Best Practice:** In CI/CD pipelines, use check-only commands. Fix errors locally, commit the changes, and let GitHub Actions verify them.

---

## 6. Important GitHub Actions Concepts

| Concept                | Purpose                                         |
| ---------------------- | ----------------------------------------------- |
| `workflow_dispatch`    | Manually runs a workflow                        |
| `workflow_call`        | Makes a workflow reusable                       |
| `push`                 | Runs when commits are pushed                    |
| `pull_request`         | Runs on pull request events                     |
| `schedule`             | Runs on a cron schedule                         |
| `runs-on`              | Specifies the runner environment                |
| `uses`                 | Uses an action or reusable workflow             |
| `run`                  | Executes shell commands                         |
| `needs`                | Defines job dependencies                        |
| `paths`                | Filters workflow triggers by changed file paths |
| `actions/checkout`     | Checks out repository code                      |
| `actions/setup-python` | Sets up Python                                  |

---
