The EventBridge schedular invokes the step functions state machine, which invokes a lambda function 
to scan a dynamodb table and save results in an s3 bucket.

We need to create an EventBridge to step functions invoke policy, step functions to lambda funciton 
invoke policy and since we'll love to have logs in cloudwatch for debugging purposes,we'll create a cloudwatch
log group for lambda.

```

resource "aws_iam_policy" "event_bridge_step_function_invoke_policy" {
  name = "event-bridgw-step-function-invoke-policy"
  policy = jsonencode(
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "states:StartExecution",
      "Resource": "${aws_sfn_state_machine.backup_dynamodb_to_s3.arn}"
    }
  ]
})

}

resource "aws_cloudwatch_log_group" "lambda_log_group" {
  name = "/aws/lambda/${aws_lambda_function.backup_lambda_function.function_name}"
  retention_in_days = 7
  lifecycle {
    prevent_destroy = false
  }
}

resource "aws_iam_policy" "lambda_cloudwatch_putmetric_policy" {
  name = "lambda-cloudwatch-putmetric-policy"
  policy = jsonencode(
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "logs:*",
      "Resource": "arn:aws:logs:*:*:*"
    },
    {
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "dynamodb:*",
      "Resource": "*"
    }
  ]
})

}

resource "aws_iam_policy" "step_function_lambda_invoke_policy" {
  name = "step-function-lambda-invoke-policy"
  policy = jsonencode(
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "lambda:InvokeFunction",
      "Resource": "${aws_lambda_function.backup_lambda_function.arn}"
    }
  ]
})

}
```

These are several Terraform resource blocks that create AWS IAM roles, IAM policies, and a CloudWatch log group.

The second block creates an IAM policy named `event-bridgw-step-function-invoke-policy` that allows starting an execution of a specific Step Functions state machine.

The third block creates a CloudWatch log group for a specific Lambda function with a retention period of 7 days.

The fourth block creates an IAM policy named `lambda-cloudwatch-putmetric-policy` that allows various actions on logs, S3, and DynamoDB resources.

The fifth block creates an IAM policy named `step-function-lambda-invoke-policy` that allows invoking a specific Lambda function by a step functions state machine.
