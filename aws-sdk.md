## 🛠️ AWS SDK (Software Development Kit)

- **AWS SDKs** are libraries that allow you to integrate AWS services into your applications
- Available for multiple programming languages:
  - **Python** (Boto3)
  - **JavaScript/Node.js**
  - **Java**
  - **C#/.NET**
  - **Go**
  - **Ruby**
  - **PHP**
  - And many more...

### Key Features:

- **Type-safe APIs** for AWS services
- **Automatic retries** and error handling
- **Built-in authentication** (same methods as CLI)
- **Pagination support** for large result sets
- **Waiters** for resource state changes

### Example (Python/Boto3):

```python
import boto3

# Create S3 client
s3 = boto3.client('s3')

# List buckets
response = s3.list_buckets()
for bucket in response['Buckets']:
    print(bucket['Name'])

# Upload file
s3.upload_file('local-file.txt', 'my-bucket', 'remote-file.txt')
```

### Authentication (Same as CLI):

1. **Access Keys** in credentials file
2. **IAM Roles** (for EC2/Lambda)
3. **Environment Variables**
4. **Instance Profile** (for EC2)

✅ **Exam Tip**: SDKs automatically handle **credential chain** - they look for credentials in a specific order.
