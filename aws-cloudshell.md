## ☁️ AWS CloudShell

- **AWS CloudShell** is a **browser-based shell** environment provided by AWS
- Pre-configured with **AWS CLI**, **SDKs**, and common development tools
- **No additional charges** for CloudShell usage (only for AWS resources you create)

### Key Features:

- **Pre-installed tools**:
  - AWS CLI v2
  - Python, Node.js, PowerShell
  - Git, Docker, kubectl
  - Text editors (vim, nano)
- **1 GB of persistent storage** per region
- **Automatic authentication** (uses your current AWS Console session)
- **Multiple shell environments** (Bash, PowerShell, Z shell)

### Benefits:

- **No setup required** - ready to use immediately
- **Always up-to-date** with latest AWS CLI and tools
- **Secure** - runs in AWS infrastructure
- **Consistent environment** across different devices
- **Session persistence** - files and settings are saved

### Limitations:

- **Limited compute resources** (1 vCPU, 2 GB RAM)
- **Session timeout** after inactivity
- **Regional availability** (not available in all regions)
- **Network restrictions** in some corporate environments

### Usage:

1. **Access**: Click CloudShell icon in AWS Console
2. **Authentication**: Automatically uses your Console session credentials
3. **File persistence**: Home directory (`/home/cloudshell-user`) persists between sessions

### Example Commands:

```bash
# Already authenticated - no aws configure needed!
aws sts get-caller-identity

# Create and manage resources
aws s3 mb s3://my-unique-bucket-name
aws ec2 describe-instances

# Use pre-installed tools
git clone https://github.com/my-repo/my-project.git
python3 my-script.py
```

✅ **Exam Tip**: CloudShell is perfect for **quick AWS operations** without setting up local development environment.

⚠️ **Important**: CloudShell inherits the **same permissions** as your AWS Console user - ensure proper IAM policies are in place.
