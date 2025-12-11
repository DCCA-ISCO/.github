# 🛠️ AWS Systems Manager (SSM) Deployment Summary

This document serves as the standard instructional guide for using **AWS Systems Manager (SSM)** as the deployment mechanism for all applications within DCCA-ISCO.

## 1. The Value Proposition: Why Use SSM

AWS Systems Manager (SSM) is the standard method for executing remote commands from GitHub Actions to target EC2 instances. It is implemented because it securely eliminates the need for open ports or storing sensitive server access credentials.

| Feature | Standard Remote Access (e.g., WinRM/SSH) | AWS Systems Manager (SSM) |
| :--- | :--- | :--- |
| **Security/Network** | Requires open inbound ports (5985/22) | **No inbound ports needed** (Agent communicates outbound only) ✅ |
| **Authentication** | Passwords/Keys stored in secrets | **IAM Roles** (password-less authentication) ✅ |
| **Audit/Compliance** | Limited native logging | **Full CloudTrail logging** of all commands** ✅ |
| **Network Access** | Requires direct IP/hostname connectivity | Works securely via **VPC Endpoints** (in private subnets) ✅ |
| **AWS Integration** | Generic tool | **AWS-native**, highly integrated ✅ |

## 2. Core Architecture and Flow

The deployment process uses GitHub-hosted runners to orchestrate the deployment securely via the AWS API. 

1.  **Code Push:** A developer pushes code to GitHub (`dev` or `master`).
2.  **CI Runner:** A GitHub-hosted Ubuntu (or any other OS or container) runner runs tests and configures AWS credentials.
3.  **API Call:** The runner uses the AWS CLI to send a command document to the **AWS Systems Manager service**.
4.  **Target Execution:** The **SSM Agent** running on the target EC2 instance receives the command and executes the required deployment script.
5.  **Output:** Execution logs are saved to a designated S3 bucket and streamed back to the GitHub Action.

## 3. Setup Requirements

The target EC2 instance and GitHub repository must be configured to enable SSM communication.

| Component | Requirement | Configuration Detail |
| :--- | :--- | :--- |
| **IAM Role (EC2)** | Instance Profile with SSM permissions. | Must include the `AmazonSSMManagedInstanceCore` policy. |
| **IAM User (GitHub)** | User with `ssm:SendCommand` permissions. | **Access Key ID** and **Secret Access Key** are stored in GitHub Secrets. |
| **SSM Agent** | Must be installed and running on the target server. | **Windows:** `AmazonSSMAgent` service. **Linux:** `ssm-agent` service. |
| **GitHub Secrets** | Must store AWS credentials, server identifiers, and paths. | Requires `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_REGION`, `INSTANCE_ID`, and `APP_PATH`. |

## 4. OS-Specific Command Execution

The GitHub Actions reusable workflow handles the OS switch internally based on the input provided. The deployment logic uses the appropriate SSM Document and shell command to ensure correct execution.

| Operating System | SSM Document Name | Scripting Language | Process Management Command (Example) |
| :--- | :--- | :--- | :--- |
| **Windows** | `AWS-RunPowerShellScript` | PowerShell | `Get-WmiObject Win32_Process ... | Stop-Process -Force` |
| **Linux** | `AWS-RunShellScript` | Bash | `pkill -f "$SCRIPT_NAME"` or `killall` |

## 5. Troubleshooting and Monitoring

All command output, including errors and standard out, is managed by SSM.

* **View History:** Use the **AWS Console: Systems Manager $\rightarrow$ Run Command** to see a history of all deployment commands and their status.
* **View Logs:** Command output is sent to a designated **S3 bucket** (`SSM_OUTPUT_BUCKET`) for long-term audit and debugging.
* **Common Failure:** An "Access Denied" error during deployment almost always indicates that the EC2 instance's **IAM Role is missing or incorrect**.

## 6. Security Best Practices

1.  **IAM Least Privilege:** The GitHub deployment user should only have permissions necessary for `ssm:SendCommand` and S3 logging.
2.  **CloudTrail:** SSM commands are logged in CloudTrail, providing an auditable record of all code deployments.
3.  **VPC Endpoints:** For servers in private subnets, configure VPC Endpoints for SSM to keep traffic entirely within the AWS network.
