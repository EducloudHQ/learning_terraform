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
  * *`handler` specifies the handler function that AWS Lambda calls to start execution.*
  * *`runtime` specifies the runtime environment for the Lambda function.*
  * *`filename` specifies the path to the deployment package within the local filesystem.*
  * *`timeout` specifies the amount of time that AWS Lambda allows a function to run before stopping it.*
  * *`memory_size` specifies the amount of memory that is allocated to the Lambda function.*
  * *In this case, the `role` argument references the ARN of an IAM role resource named `my_lambda_role`.*
  
Create a file called `backup.py` and type in the code below.

```python

import boto3
import json

def handler(event, context):
    
    print("Event : ", event['Payload'])
    print("Context : ", context)
    payload = event['Payload']
    #payload = payload['detail']['custom_detail_type']
    print("payload : ",  payload)
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table(payload['tableName'])
    s3 = boto3.client('s3')
    response = table.scan()
    data = response['Items']
    while 'LastEvaluatedKey' in response:
        response = table.scan(ExclusiveStartKey=response['LastEvaluatedKey'])
        data.extend(response['Items'])
    s3.put_object(Body=json.dumps(data), Bucket=payload['s3BucketName'], Key='file_name.json')
    
    return {
                    "statusCode": 200,
                    "headers": {    
                        "Access-Control-Allow-Headers" : "Content-Type",
                        "Access-Control-Allow-Origin": "*", 
                        "Access-Control-Allow-Methods": "GET" 
                    },         
                    "body": json.dumps({"message": "success"}),
                }

```
The function gets a dynamodb table name through a payload, scans the table for items and saves
the items in a s3 bucket. 
