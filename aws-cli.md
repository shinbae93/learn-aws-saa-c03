
## 💻 AWS CLI (Command Line Interface)

- **AWS CLI** is a command-line tool for interacting with AWS services
- Allows you to manage AWS resources from your terminal/command prompt
- Built on top of the **AWS SDK for Python (Boto3)**

### Installation & Setup:

```bash
# Install AWS CLI v2 (recommended)
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /

# Configure credentials
aws configure
# Prompts for: Access Key ID, Secret Access Key, Default region, Output format
```

### Configuration Files:

- **Credentials**: `~/.aws/credentials`
- **Config**: `~/.aws/config`
- **Profiles**: Support multiple AWS accounts/environments

### Common Commands:

```bash
# List S3 buckets
aws s3 ls

# Create EC2 instance
aws ec2 run-instances --image-id ami-12345678 --instance-type t2.micro

# Get caller identity (who am I?)
aws sts get-caller-identity

# List IAM users
aws iam list-users
```

### Authentication Methods:

1. **Access Keys** (stored in `~/.aws/credentials`)
2. **IAM Roles** (for EC2 instances)
3. **Environment Variables** (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`)
4. **SSO** (Single Sign-On)

✅ **Exam Tip**: AWS CLI uses the **same permissions** as the IAM user/role whose credentials are configured.