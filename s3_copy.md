# Copy S3 Objects Across AWS Accounts

This guide explains how to copy objects from a source S3 bucket in one AWS account to a destination bucket in another account, using cross-account bucket policies and an IAM role.

# 1. IAM Role (in the migration account)

## 1.1 Create a role with permissions to read from the source bucket and write to the destination bucket
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetObject"
      ],
      "Resource": [
        "arn:aws:s3:::<source-bucket>",
        "arn:aws:s3:::<source-bucket>/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:PutObject",
        "s3:PutObjectAcl",
        "s3:PutObjectTagging",
        "s3:GetObjectTagging",
        "s3:GetObjectVersion",
        "s3:GetObjectVersionTagging"
      ],
      "Resource": [
        "arn:aws:s3:::<dest-bucket>",
        "arn:aws:s3:::<dest-bucket>/*"
      ]
    }
  ]
}
```

`<source-bucket>` → the source bucket name (from another account).

`<dest-bucket>` → the destination bucket name.


### 1.2 Assume the role

```
aws sts assume-role \
  --role-arn "arn:aws:iam::<migration-account-id>:role/S3MigrationRole" \
  --role-session-name AWSCLI-Session \
  --duration-seconds 43200
```

## 2. Bucket policies

### 2.1 Source bucket policy (grant read access)

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCrossAccountListAndRead",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<migration-account-id>:root"
      },
      "Action": [
        "s3:ListBucket",
        "s3:GetObject"
      ],
      "Resource": [
        "arn:aws:s3:::<source-bucket>",
        "arn:aws:s3:::<source-bucket>/*"
      ]
    }
  ]
}
```

### 2.2 Destination bucket policy (grant write access)

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCrossAccountWrite",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<migration-account-id>:root"
      },
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::<dest-bucket>/*"
    }
  ]
}
```

# 3. Verification
Before running the full copy, verify access with test commands:

`aws s3 ls s3://<source-bucket>/ --region <source-region>`


# 4. Copy command

```
time aws s3 cp \
    "s3://bucket-origin/folder" \
    "s3://bucket-dest/folder" \
    --recursive \
    --source-region <source-region>
    --region <dest-region>  \
    --copy-props none
```
