After successfully deploying the infrastructure, it's only right we test to make sure 
everything works as expected. 

Firstly, let's check if the EventBridge rule was created successfully.

Sign-in to the aws console,search and open EventBridge from the search bar. 

Click on `rules` at the middle right-hand side of the screen and search for `backup---`.

![alt text](https://raw.githubusercontent.com/EducloudHQ/learning_terraform/master/assets/eventbridge_rule.png)

If the infrastructure deployed correctly, should see your EventBridge rule. 

This rule invokes a step functions state machine every `12pm` with a payload containing the s3 bucket name and the dynamodb table name.

But for testing purposes and since we can't wait till 12pm, we'll send the payload manually as input 
to the state machine.

From the AWS console, search and open step functions.Search for the state machine 
`backup_dynamodb_integration_to_s3` and open it. 

Click on `start execution` and input this payload. 

```json
{"s3BucketName":"backup-bucket-dd1d8368-4aae-9197-7e5a-1fe1632f145e",
  "tableName":"Course-xfr5eaifvncphcvpnbjizspjeu-migration"}

```

![alt text](https://raw.githubusercontent.com/EducloudHQ/learning_terraform/master/assets/sf_start_execution.png)

From the bottom right-hand side, click on `start execution`

![alt text](https://raw.githubusercontent.com/EducloudHQ/learning_terraform/master/assets/sf_success.png)

You would have a similar view if the backup succeeded. 

Now you can go to s3 and see the backed up file.

![alt text](https://raw.githubusercontent.com/EducloudHQ/learning_terraform/master/assets/s3_files.png)


Lastly, please destroy all created resources in order not to incur unnecessary charges.



