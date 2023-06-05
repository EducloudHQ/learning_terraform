As stated earlier, we'll be using a lambda function to fetch data from a dynamodb table
and store it in s3.

Let's create the lambda function resource.

```
resource "aws_lambda_function" "backup_lambda_function" {
  function_name = "backup_lambda_function"
  role = aws_iam_role.my_lambda_role.arn
  handler = "backup.handler"
  runtime = "python3.8"
  filename = "backup.zip"
  timeout = 300
  memory_size = 128

}
```

This is a Terraform resource block that creates an AWS Lambda function.

The various arguments specify the settings for the Lambda function:

  * *`function_name` specifies the name of the Lambda function.*
  * *`role` specifies the ARN of the IAM role that the Lambda function assumes when it executes.*
  * *`handler` specifies the handler function that AWS Lambda calls to start execution of the Lambda function.*
  * *`runtime` specifies the runtime environment for the Lambda function.*
  * *`filename` specifies the path to the deployment package within the local filesystem.*
  * *`timeout` specifies the amount of time that AWS Lambda allows a function to run before stopping it.*
  * *`memory_size` specifies the amount of memory that is allocated to the Lambda function.*
  * *In this case, the `role` argument references the ARN of an IAM role resource named `my_lambda_role`.*
