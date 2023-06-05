In this project, we'll learn how to build a system to schedule data backups from a DynamoDB table to an AWS S3 bucket using a step functions workflow and an
EventBridge schedular.

Once deployed, EventBridge scheduler invokes the Step Function every day at 12 pm and passes the bucket and table name to the Step Function. 

The Step Function then invokes a Lambda function that fetches data from a DynamoDB table and stores it in an S3 bucket as a JSON file.

Here are the tools and AWS Serverless Services we'll be using to accomplish this task:

* **DynamoDB** : The source database that contains the data to be backed up.
* **S3** : The destination storage location for the backed-up data.
* **Lambda** : The function that reads data from DynamoDB and writes it to S3.
* **EventBridge scheduler** : allows you to schedule events that can trigger AWS Lambda functions or other targets at specified times.
* **Terraform** : The infrastructure-as-code tool used to deploy and manage the project.

Before proceeding, you will need to have an AWS account and the AWS CLI installed on your local machine. 

You will also need to configure your AWS credentials and set up the necessary IAM roles and policies.

## Solutions Architecture

![alt text](https://raw.githubusercontent.com/EducloudHQ/learning_terraform/master/assets/project_2.png)

