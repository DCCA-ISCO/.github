
# ⚙️ DCCA-ISCO Standard Python/AWS CI/CD Guide

This repository contains the standardized, reusable GitHub Actions workflows for deploying **any** Python application to AWS Windows Servers using AWS Systems Manager (SSM).

This pipeline enforces standard quality checks (testing, linting, security) and provides a secure, password-less deployment mechanism.

---

## 🚀 Getting Started with a New Project

### Phase 1: AWS Server Preparation (One-Time Setup)

The target AWS EC2 instance must meet these core requirements:

1.  **IAM Role:** The EC2 instance must have the appropriate IAM Role attached (e.g., `Standard-Python-SSM-Role`), granting the `AmazonSSMManagedInstanceCore` policy.
2.  **SSM Agent:** The **AmazonSSMAgent service** must be running and connected to AWS.
3.  **Base Setup:** Git and Python must be installed system-wide. The application code must be cloned once to the target directory, and the Python virtual environment (`venv`) must be created inside it.

### Phase 2: GitHub Environment & Secrets Configuration

All deployment parameters are managed through GitHub Secrets.

#### A. Repository-Level Secrets (Global)

These secrets are shared by both Development and Production deployments and should be set at the repository level.

| Secret Name | Description | Example Value |
|-------------|-------------|---------------|
| `AWS_ACCESS_KEY_ID` | Access key for the IAM user authorized to run SSM commands. | `AKIAIOSFODNN7EXAMPLE` |
| `AWS_SECRET_ACCESS_KEY` | Secret key for the IAM user. | `wJalrXUtnFEMI/...` |
| `AWS_REGION` | The region where your EC2 instances are located. | `us-east-1` |
| `APP_PATH` | **Full root path** to the application on the server. | `C:\Apps\YOUR-PROJECT-NAME` (Win) or `/home/ec2-user/your-project` (Linux) |
| `SCRIPT_NAME` | The main Python script filename to run. | `app.py` |
| `SSM_OUTPUT_BUCKET` | S3 bucket name for storing SSM command logs. | `dcca-isco-ssm-logs` |

#### B. Environment-Level Secrets (Per Environment)

These secrets define the target server for deployment. Go to: **Repository $\rightarrow$ Settings $\rightarrow$ Environments** and configure both **`development`** and **`production`**.

| Secret Name | Description |
|-------------|-------------|
| `INSTANCE_ID` | The unique EC2 Instance ID (e.g., `i-123...`) of the target server. |
| `SERVER_HOST` | The DNS name or hostname used for the health check (e.g., `app-dev.example.com`). |

### Phase 3: CI/CD Activation

1.  **Create Workflow File:** Go to the project's **Actions** tab $\rightarrow$ **New workflow**.
2.  **Select Template:** Choose the **`DCCA Python CI/CD (Starter Template)`**.
3.  **Commit and Push:** Commit the resulting file to your `dev` branch. Deployment will now be automated.

---

## 🐧 Alternative Configuration: AWS Linux Servers

If the target deployment server is **Linux** (instead of Windows), the reusable deployment workflow requires a specific environment variable to switch the execution commands from PowerShell to Bash.

### 1. The Requirement

To switch the reusable workflow (`ssm-deploy-reusable.yml`) to use Linux commands, the calling workflow must pass an input variable.

### 2. Implementation: Modify the Starter Workflow

In your project's main workflow file (the one copied from the starter), modify the deployment job to pass an input that indicates the OS.

```yaml
# Inside the deploy-dev or deploy-prod job in your project's main CI file

  deploy-dev:
    name: Deploy to Dev Server
    needs: test-and-quality
    # ...
    uses: DCCA-ISCO/.github/.github/workflows/ssm-deploy-reusable.yml@main
    with:
      branch-name: dev
      server-env: development
      # ADD THIS LINE TO SIGNAL LINUX
      target-os: linux 
      # Remove this line if the server is Windows (default behavior)
    secrets: inherit
