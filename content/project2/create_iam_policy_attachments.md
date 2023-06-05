Now, we need to attach the iam roles to the iam policies, to create a policy attachment.

```
resource "aws_iam_role_policy_attachment" "step_function_lambda_invoke_policy_attachment" {
  role = aws_iam_role.step_function_lambda_invoker.name
  policy_arn = aws_iam_policy.step_function_lambda_invoke_policy.arn
}

resource "aws_iam_role_policy_attachment" "event_bridge_step_function_invoke_policy_attachment" {
  role = aws_iam_role.event_bridge_invoker.name
  policy_arn = aws_iam_policy.event_bridge_step_function_invoke_policy.arn
}

resource "aws_iam_role_policy_attachment" "lambda_cloudwatch_invoke_policy_attachment" {
  role = aws_iam_role.my_lambda_role.name
  policy_arn = aws_iam_policy.lambda_cloudwatch_putmetric_policy.arn
}
```

Each block specifies the name of the IAM role and the ARN of the IAM policy to attach to the role.

Remember we created the roles and policies above.

The first block creates an IAM role policy attachment that attaches an IAM policy named `step_function_lambda_invoke_policy` to an IAM role named `step_function_lambda_invoker`

The second block creates an IAM role policy attachment that attaches an IAM policy named `event_bridge_step_function_invoke_policy` to an IAM role named `event_bridge_invoker`

The third block creates an IAM role policy attachment that attaches an IAM policy named `lambda_cloudwatch_putmetric_policy` to an IAM role named `my_lambda_role`