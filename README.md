# **FedRAMP S3 Bucket**
This project is a demonstration how to easily create a S3 bucket in AWS and apply security measures that comply with FedRAMP requirements. This includes enabling server-side encryption, configuring bucket policies, setting up access logging, and enabling versioning.

### Prerequietes
AWS Account
AWS CLI installed and configured
Basic knowlegde of S3 and IAM

### Setup Instructions

1. **Create an S3 Bucket**
```
aws s3api create-bucket --bucket my-fedramp-compliant-bucket --region us-east-2 --create-bucket-configuration LocationConstraint=us-east-2
```

2. **Enable Server-side encryption** 
```
aws s3api put-bucket-encryption --bucket my-fedramp-compliant-bucket --server-side-encryption-configuration '{`
  `"Rules": [{`
    `"ApplyServerSideEncryptionByDefault": {`
      `"SSEAlgorithm": "AES256"`
    `}`
  `}]`
`}'
```

3. **Configure Bucket Policy**
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-fedramp-compliant-bucket/*",
      "Condition": {
        "Bool": {
          "aws:SecureTransport": "false"
        }
      }
    }
  ]
}
```

4. **Apply bucket policy**
```
aws s3api put-bucket-policy --bucket my-fedramp-compliant-bucket --policy file://bucket-policy.json 
```



5. **Create a log bucket**
```
aws s3api create-bucket --bucket my-log-bucket --region us-east-2 --create-bucket-configuration LocationConstraint=us-east-2
```

6. **Enable logging on the main bucket**
```
aws s3api put-bucket-logging --bucket my-fedramp-compliant-bucket --bucket-logging-status '{
  "LoggingEnabled": {
    "TargetBucket": "my-log-bucket",
    "TargetPrefix": "my-fedramp-compliant-bucket-logs/"
  }
}'
```

7. **Enable Versioning**
```
aws s3api put-bucket-versioning --bucket my-fedramp-compliant-bucket --versioning-configuration Status=Enabled
```

8. **Create an IAM role with S3 access policy**
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": [
        "arn:aws:s3:::my-fedramp-compliant-bucket",
        "arn:aws:s3:::my-fedramp-compliant-bucket/*"
      ]
    }
  ]
}
```

9. **Create the role**
```
aws iam create-role --role-name S3AccessRole --assume-role-policy-document file://trust-policy.json
```

10. **Attach the policy to the role**
```
aws iam put-role-policy --role-name S3AccessRole --policy-name S3AccessPolicy --policy-document file://s3-access-policy.json
```


### Summary
#### AWS resources created
S3 bucket with server-side encryption using AE256
S3 bucket policy to enforce HTTPS access
S3 access logging enabled
S3 versioning enabled
IAM role with S3 access policy

### License
This project is licensed under the MIT License