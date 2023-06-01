## Installing Terraform

The easiest way to install Terraform is by following the step to step installation guide on the official [Terraform site](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli).

After successfully installing Terraform, verify your installation by running this command from your commandline interface(cli) to list Terraform's available subcommands.


`terraform -help`

Let's get Started.

From your CLI, let's create a folder called `introduction_tf` and cd into it

```
mkdir introduction_tf
cd introduction_tf
```

Once inside that folder, let's create a file called `main.tf` that'll be the entry point to your project.

Now, we are going to define the provider we'll be using.

Type this code into the `main.tf` file.

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

provider "aws" {
  region = var.region
  profile = var.profile
}
```
This configuration file allows Terraform to create and manage resources on AWS using the specified provider settings.

The provider block configures the AWS provider with the values of the region and profile variables, which must be defined elsewhere in the configuration or passed as input variables.

We'll be using the default VPC(virtual private container) container, created by amazon in our region.

Inside this VPC, we'll create `security groups` `an ec2 instance` and an `application load balancer`.

Before creating these resources, we need to define some variables that we are going to use in the root module.







