In order to avoid overriding existing backups in s3, we'll generate random UUID's and assign them 
as names to our s3 buckets.

Type in the following code into `main.tf`

```
resource "random_uuid" "random" {
}
```

This `random_uuid` resource generates a random UUID that is used as part of the S3 bucket name.

Next, we'll create an `aws_s3_bucket` resource with the unique UUID .

```
resource "aws_s3_bucket" "backup_bucket" {
  bucket = "backup-bucket-${random_uuid.random.id}"
}
```

Now, we have to secure the s3 bucket, by blocking public access to it. 


```
resource "aws_s3_bucket_public_access_block" "public_access" {
  bucket = aws_s3_bucket.backup_bucket.id

  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}
```
The `aws_s3_bucket_public_access_block` resource blocks public access to the S3 bucket.

Then, using a `aws_iam_policy_document` data block, we'll create an IAM(Identity and Access Management) policy document that allows access 
to the bucket.

```
data "aws_iam_policy_document" "allow_access" {
  statement {
    principals {
      type        = "*"
      identifiers = ["*"]
    }

    actions = [
      "s3:*",
    ]

    resources = [
      "${aws_s3_bucket.backup_bucket.arn}",
      "${aws_s3_bucket.backup_bucket.arn}/*",
    ]
  }
}
```
This policy document allows access to the S3 bucket created earlier by specifying the bucket ARN in the resources parameter and allowing all S3 actions in the `actions` parameter.

```
resource "aws_s3_bucket_policy" "allow_access" {
  bucket = aws_s3_bucket.backup_bucket.id
  policy = data.aws_iam_policy_document.allow_access.json
}

```
Next, we'll create a `aws_s3_bucket_policy` resource, that attaches the IAM policy document to the S3 bucket.

The bucket argument specifies the ID of the S3 bucket to which the policy should be applied. 

The policy argument specifies the JSON representation of the IAM policy document that defines the policy.
