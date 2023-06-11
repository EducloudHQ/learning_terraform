We have to create iam roles to access for a couple of resources.

- Invoke a lambda function
- Invoke a step functions state machine
- Invoke EventBridge schedular

```
resource "aws_iam_role" "my_lambda_role" {
  name = "my_lambda_role"
  assume_role_policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF
}

resource "aws_iam_role" "step_function_lambda_invoker" {
  name = "step-function-lambda-invoker"
  assume_role_policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "states.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF

}

resource "aws_iam_role" "event_bridge_invoker" {
  name = "event-bridge-invoker"
  assume_role_policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "events.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF

}
```

These are three Terraform resource blocks that create AWS IAM roles. Each block specifies the name of the IAM role and an assume role policy that defines which principals are allowed to assume the role.

The first block creates an IAM role named `my_lambda_role` with an assume role policy that allows the `lambda.amazonaws.com` service to assume the role.

The second block creates an IAM role named `step-function-lambda-invoker` with an assume role policy that allows the `states.amazonaws.com` service to assume the role.

The third block creates an IAM role named `event-bridge-invoker` with an assume role policy that allows the `events.amazonaws.com` service to assume the role.