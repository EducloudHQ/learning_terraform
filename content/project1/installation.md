## Installing Terraform

The easiest way to install Terraform is by following the step to step installation guide on the official [Terraform site](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli).

After successfully installing Terraform, verify your installation by running this command from your commandline interface(cli) to list Terraform's available subcommands.


`terraform -help`

Let's get Started.

From your CLI, let's create a folder called `introduction_tf` and cd(change directory) into it

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
  ```
The `required_providers` block specifies that this configuration requires the AWS provider from HashiCorp with a version of at least 4.16 and a Terraform 
version of 1.2.0 or later.








