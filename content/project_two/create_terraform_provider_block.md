In the newly created folder, create a file named `main.tf`.

This file would contain all the configurations for our api.

Next, type the following code into `main.tf`.

```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.16"
    }
  }

  required_version = ">= 1.2.0"
}

```

The `required_providers` block specifies that this configuration requires the AWS provider from HashiCorp,with a version of at least 4.16. 

The `required_version` block specifies that this configuration requires Terraform version 1.2.0 or later.

Next, we have to specify the aws region we'll be deploying the app to and also the aws profile we plan on using.

Within the `terraform {...}` block, type in the code below.

```
provider "aws" {
  region = "us-east-1"
  profile = "educloud"
}

```