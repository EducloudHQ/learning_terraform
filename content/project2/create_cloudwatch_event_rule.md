Finally, we need to create an event bridge rule which invokes the step functions state machine 
every day at 12pm.


```
resource "aws_cloudwatch_event_rule" "backup_dynamodb_to_s3" {
  name = "backup_dynamodb_to_s3"
  schedule_expression = "cron(0 * 12 * ? *)"
}

resource "aws_cloudwatch_event_target" "yada" {
  target_id = "event_target"
  rule      = aws_cloudwatch_event_rule.backup_dynamodb_to_s3.name
  arn       = aws_sfn_state_machine.backup_dynamodb_to_s3.arn
  role_arn = aws_iam_role.event_bridge_invoker.arn
  input = jsonencode(
{
  "s3BucketName":  "${aws_s3_bucket.backup_bucket.id}",
  "tableName": "Product"

})
}
```

These are two Terraform resource blocks that create an AWS CloudWatch Events rule and target.

The first block creates a CloudWatch Events rule named `backup_dynamodb_to_s3` with a specified schedule expression.

The second block creates a CloudWatch Events target that specifies the target for the previously created rule.

The `target_id` argument specifies a unique identifier for the target. 

The `rule` argument specifies the name of the rule to associate with the target.

The `arn` argument specifies the ARN of the target resource.

The `role_arn` argument specifies the ARN of the IAM role to assume when invoking the target.

The `input` argument specifies the JSON input to pass to the target.

In this case, the target is a Step Functions state machine resource named `backup_dynamodb_to_s3`, and the input includes the ID of an S3 bucket resource named `backup_bucket` and a table name.

We're done creating all resources. 

Let's proceed to deploy the application.



