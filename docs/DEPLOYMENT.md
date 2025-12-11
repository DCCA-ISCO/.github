# 🛠️ Standard Operating Procedure: Deployment Configuration

This SOP details the required AWS and GitHub setup for any new application deployment.

## Part 1: Configure IAM Permissions

### Step 1: Create IAM Role for EC2 Instances (if not already existing)
The role must be created once and attached to all application servers.

* **Role Name:** `Standard-Python-SSM-Role`
* **Trust Policy:** Must allow `ec2.amazonaws.com` to assume the role.
* **Managed Policy:** Attach `arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore`.

### Step 2: Attach IAM Role to EC2 Instances
Attach the `Standard-Python-SSM-Profile` to the target Dev and Prod EC2 instances via the AWS Console or CLI.

### Step 3: Create IAM User for GitHub Actions
The user requires permissions to invoke SSM commands.

* **User Name:** `github-actions-deployer`
* **Policy:** The user must have a custom IAM policy granting `ssm:SendCommand`, `ssm:GetCommandInvocation`, and `s3:PutObject` (for log storage).
* **Credentials:** Generate and secure the **Access Key ID** (`AWS_ACCESS_KEY_ID`) and **Secret Access Key** (`AWS_SECRET_ACCESS_KEY`) for use in GitHub Secrets.

## Part 2: AWS Server Configuration (Windows)

### Step 1: Verify SSM Agent
Verify the `AmazonSSMAgent` service is running and set to start automatically.

### Step 2: Application Directory Setup
1.  Create the application directory (e.g., `C:\Apps\PROJECT-NAME`).
2.  Ensure the directory and its contents are accessible to the `LocalSystem` account (where the SSM commands run).

## Part 3: GitHub Secrets Checklist

Ensure the following secrets are configured in the target project repository:

* **Repository Secrets (Global):** `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_REGION`, `APP_PATH`, `SCRIPT_NAME`, `SSM_OUTPUT_BUCKET`.
* **Environment Secrets (Development/Production):** `INSTANCE_ID`, `SERVER_HOST`.
