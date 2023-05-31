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

## Defining Variables
Create a file named `variables.tf` inside your current folder and type in this code.

```

variable "vpc_id" {
  
    type = string
    description = "Vpc id"
}

variable "region" {

    type = string
    description = "AWS Region"
    default = "us-east-1"
}

variable "profile" {

    type = string
    description = "AWS Profile"
    default = "default"
}
```
Variables are defined with the keywords `variable`, followed by the name of that variable.

The parameters for a variable are 

* type :  `string, number, bool, list(<Type>) , set (<Type>) , map(<Type>) , object({<ATTR NAME> = <TYPE>, ... }), tuple([<TYPE>, ...]) `
* description : A short description of the purpose of the variable
* default : The default value of the variable
* validation : A block to define validation rules
* sensitive : Use to limit the output of the variable in the Terraform UI
* nullable : Specify if the variable can be null

For Example,
```    
  variable example {

      type = number
      description = "Example description"
      default = 007
      validation {
  	condition = can(regex("^((25[0-5]|2[0-4][0-9]|1[0-9][0-		9]|[1-9]?[0-9])\.){3}(25[0-5]|2[0-4][0-9]|1[0-9][0-9]|[1-9]?[0-	9])$", var.ip_address))
  	error_message = "The ip_address value must be a valid IP 	address."
  	}
      sensitive = true
      nullable = true
  }
  ```

## Retrieve VPC Configuration

Next we need to retrieve the default vpc configuration from our aws account and the attached subnets.

Inside the `main.tf` file, type in the code below.

```
data "aws_vpc" "main" {
  id = var.vpc_id
}

data "aws_subnets" "default" {
  filter {
    name   = "vpc-id"
    values = [var.vpc_id]
  }
}
```

In order to retrieve information from a variety of sources, including cloud providers, other Terraform 
configurations and functions, Terraform uses `data` sources.

Data sources  make your Terraform configurations more dynamic and flexible.

*For example*

```
data "data_source_name" "name" {
  [config_options]
}
```

## Create Security Group

Next, we need create our first resource. `security group`.

The `aws_security_group` resource in Terraform allows you to create and manage security groups in AWS.

A security group is a collection of rules that control the traffic that is allowed to flow in and out of an instance.

Type this code fragment into `main.tf` file.

```
resource "aws_security_group" "secGroup" {
  name        = "secGroup"
  description = "Allow Http inbound traffic"
  vpc_id      = data.aws_vpc.main.id

  ingress {
    from_port        = 80
    to_port          = 80
    protocol         = "TCP"
    cidr_blocks      = ["0.0.0.0/0"]
  }

  egress {
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
  }

}
```


The `name` attribute is the name of the security group. 

The `description` attribute is a description of the security group.

The `vpc_id` attribute is the ID of the VPC that the security group will be created in.

The `ingress` block defines the rules that allow traffic to flow into the security group.

The `from_port` attribute is the starting port number. 

The `to_port` attribute is the ending port number. 

The `protocol` attribute is the protocol that the traffic will use. 

The `cidr_blocks` attribute is a list of CIDR blocks that are allowed to send traffic to the security group.

The `egress` block defines the rules that allow traffic to flow out of the security group.

The `from_port` attribute is the starting port number. 

The `to_port` attribute is the ending port number. 

The `cidr_blocks` attribute is a list of CIDR blocks that are allowed to receive traffic from the security group.


## Create An AWS Instance
We need to create an AWS instance.

The `aws_instance` resource in Terraform allows you to create and manage EC2 instances in AWS. 

Type this code fragment into `main.tf` file.

```
resource "aws_instance" "default" {
  ami = "ami-0889a44b331db0194"
  instance_type = "t2.micro"
  vpc_security_group_ids = ["${aws_security_group.secGroup.id}"]
  # availability_zone = "us-east-1b"
  # associate_public_ip_address = true
  key_name = "defaultKeypair"
  user_data_replace_on_change = true
  user_data_base64 = "IyEvYmluL2Jhc2gNCnN1ZG8geXVtIHVwZGF0ZSAteQ0Kc3VkbyB5dW0gaW5zdGFsbCAteSBodHRwZA0Kc3VkbyBzeXN0ZW1jdGwgc3RhcnQgaHR0cGQNCnN1ZG8gc3lzdGVtY3RsIGVuYWJsZSBodHRwZA0Kc3VkbyB1c2VybW9kIC1hIC1HIGFwYWNoZSBlYzItdXNlcg0Kc3VkbyBjaG93biAtUiBlYzItdXNlcjphcGFjaGUgL3Zhci93d3cNCnN1ZG8gY2htb2QgMjc3NSAvdmFyL3d3dw0Kc3VkbyBmaW5kIC92YXIvd3d3IC10eXBlIGQgLWV4ZWMgY2htb2QgMjc3NSB7fSBcOw0Kc3VkbyBmaW5kIC92YXIvd3d3IC10eXBlIGYgLWV4ZWMgY2htb2QgMDY2NCB7fSBcOw0Kc3VkbyBlY2hvICI8P3BocCBwaHBpbmZvKCk7ID8+IiA+IC92YXIvd3d3L2h0bWwvcGhwaW5mby5waHA="

}
```

The `ami` attribute is the ID of the Amazon Machine Images(AMI) that you want to use to create the instance.

The `instance_type` attribute is the type of instance that you want to create. 

The `vpc_security_group_ids` attribute is a list of IDs of security groups that you want to attach to the instance.

The `availability_zone` attribute is the availability zone that you want to create the instance in. 

The `associate_public_ip_address` attribute specifies whether you want to associate a public IP address with the instance or not.

The `key_name` attribute is the name of the SSH key that you want to use to connect to the instance. 

The `user_data` attribute is a block of text that you want to run as the first command when the instance starts.

In the code above, we are creating an EC2 instance named `default` that uses the AMI with ID `ami-0889a44b331db0194`. 

The instance will be of type `t2.micro` and will be created in the `us-east-1b` availability zone. 

The instance will have a public IP address and will be associated with the security group named `secGroup`. 

The instance will also have the SSH key named `defaultKeypair` attached to it.

The `user_data` attribute is set to a base64-encoded string that contains the following commands:

```
#!/bin/bash
sudo yum update -y
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd
sudo usermod -a -G apache ec2-user
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www
sudo find /var/www -type d -exec chmod 2775 {} \;
sudo find /var/www -type f -exec chmod 0664 {} \;
sudo echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php
```

The script will install the Apache web server on an Amazon Linux instance, and then configure it to serve a PHPinfo page.

## Create Application Load Balancer

For this resource we need to create a Terraform module where we will create 
- `Load balancer,`
- `a load balancer target group,` 
- `a load balancer listener`
- `a load balancer target group attachment `

A Terraform module is a collection of Terraform configuration files that can be used to create and manage infrastructure resources. 

Modules are typically organized into directories, with each directory containing a set of configuration files that define a specific resource or group of resources.

To create a module,first create a local folder called `load_balancer` inside your current folder. 

This folder will contain our local terraform module. 

Inside this folder, create three files.

- main.tf, 
- variables.tf, 
- outputs.tf

Open up the ` varaibles.tf` file and type in the code below.

```
variable "vpc_id" {

    type = string
    description = "Vpc id"
  
}

variable "load_balancer_name" {
  
  type = string
  description = "load balancer name"
  default = "default"

}

variable "load_balancer_type" {

    type = string
    description = "load balancer type"
    default = "application"
}

variable "security_groud_id" {

    type = string
    description = "Security group name"
}

variable "subnets_ids" {

    type = list(string)
    description = "Subnets ids list"
}

variable "aws_alb_target_group_name" {

    type = string
    description = "application load balancer target group name"
    default = "my-alb-target-group"
}

variable "aws_alb_target_group_port" {

    type = number
    description = "application load balancer target group name"
    default = 80
}

variable "aws_alb_target_group_protocol" {

    type = string
    description = "application load balancer target group protocol"
    default = "HTTP"
}

variable "aws_alb_target_group_type" {

    type = string
    description = "application load balancer target group type"
    default = "instance"
}

variable "aws_alb_listener_port" {

    type = number
    description = "application load balancer listener port type"
    default = 80
}


variable "aws_alb_listener_protocol" {

    type = string
    description = "application load balancer listener protocol"
    default = "HTTP"
}


variable "aws_alb_listener_default_action_type" {

    type = string
    description = "application load balancer listener default action type"
    default = "forward"
}

variable "aws_lb_target_group_attachment_port" {

    type = number
    description = "application load balancer attachement port"
    default = 80
}

variable "aws_lb_target_group_attachment_instance_id" {

    type = string
    description = "application load balancer attachement instance id"
}



```

These variables will be used to create an Application Load Balancer (ALB) in AWS. 

The ALB will be named `default` and will be of type `application`. It will be associated with the security group with ID `security_groud_id` and it'll be created in the subnets with IDs listed in the `subnets_ids` variable.

The ALB will have a target group named `my-alb-target-group` that will listen on port 80 and use the HTTP protocol. 

The target group will be of type `instance` and will be attached to the instance with ID `aws_lb_target_group_attachment_instance_id` on port `aws_lb_target_group_attachment_port`.

The ALB will have a listener that listens on port 80 and uses the HTTP protocol. 

The listener will have a default action of `forward`.

In the `outputs.tf` file, type in this content.

```
output "load_balancer_dns_name" {
    value = aws_lb.default.dns_name
    description = "Load balancer dns name"
}
```

The output defined will return the DNS name of the ALB. The DNS name can be used to access the ALB from the internet.

For example, if the DNS name is `example.com`, you can access the ALB by opening a web browser and navigating to the URL `http://example.com`.

Inside the load_balancer folder in ` main.tf` let's create a load balancer

The `aws_lb` resource in Terraform allows you to create and manage Application Load Balancers (ALBs) in AWS. An ALB is a highly available, scalable, and elastic load balancer that distributes traffic across multiple targets, such as EC2 instances, Lambda functions, and containers.

```
resource "aws_lb" "default" {

  name               = var.load_balancer_name
  internal           = false
  load_balancer_type = var.load_balancer_type
  security_groups    = [var.security_groud_id]
  subnets            = [for id in var.subnets_ids : id]

}
```

The `name` attribute is the name of the ALB. The `internal` attribute specifies whether the ALB is internal or external. An internal ALB is only accessible from within the VPC in which it is created. An external ALB is accessible from the internet.

The `load_balancer_type` attribute specifies the type of ALB. The default value is `application`. An application ALB is used to distribute traffic to web applications.

The `security_groups` attribute is a list of IDs of security groups that you want to associate with the ALB. The ALB will use these security groups to control the traffic that is allowed to flow to and from the ALB.

The `subnets` attribute is a list of IDs of subnets in which you want to create the ALB. The ALB will be created in the subnets that you specify.

The target group:

The `aws_alb_target_group` resource in Terraform allows you to create and manage target groups for Application Load Balancers (ALBs) in AWS. A target group is a collection of targets, such as EC2 instances, Lambda functions, and containers, that can receive traffic from an ALB.

```
resource "aws_alb_target_group" "default" {
  name = var.aws_alb_target_group_name
  port = var.aws_alb_target_group_port
  protocol = var.aws_alb_target_group_protocol
  vpc_id = var.vpc_id
  health_check {
    interval = 30
    timeout = 5
    path = "/"
    unhealthy_threshold = 2
    healthy_threshold = 2
  }
  target_type = var.aws_alb_target_group_type

}
```

The `name` attribute is the name of the target group. The `port` attribute is the port on the target group that will be used to receive traffic from the ALB. The `protocol` attribute is the protocol that will be used to communicate with the target group. The `vpc_id` attribute is the ID of the VPC in which the target group will be created.

The `health_check` block defines the health check that will be used to monitor the targets in the target group. The `interval` attribute is the interval in seconds at which the health check will be performed. The `timeout` attribute is the amount of time in seconds that the health check will wait for a response from a target before considering it unhealthy. The `path` attribute is the path that will be used to perform the health check. The `unhealthy_threshold` attribute is the number of consecutive health checks that must fail before a target is considered unhealthy. The `healthy_threshold` attribute is the number of consecutive health checks that must succeed before a target is considered healthy.

The `target_type` attribute specifies the type of targets that can be added to the target group. The default value is `instance`. An instance target group can be used to distribute traffic to EC2 instances.

The load balancer listener:

The `aws_alb_listener` resource in Terraform allows you to create and manage listeners for Application Load Balancers (ALBs) in AWS. A listener is a rule that defines how the ALB will handle traffic that is received on a particular port and protocol.

```
resource "aws_alb_listener" "default" {
  load_balancer_arn = aws_lb.default.arn
  port = var.aws_alb_listener_port
  protocol = var.aws_alb_listener_protocol
  default_action {
    target_group_arn = aws_alb_target_group.default.arn
    type = var.aws_alb_listener_default_action_type
  }
}
```

The `load_balancer_arn` attribute is the ARN of the ALB that the listener will be associated with. The `port` attribute is the port on the ALB that the listener will listen on. The `protocol` attribute is the protocol that the listener will use to communicate with the targets.

The `default_action` block defines the default action that will be taken when traffic is received on the listener. The `target_group_arn` attribute is the ARN of the target group that will receive traffic when the default action is taken. The `type` attribute specifies the type of default action. The default value is `forward`. A forward action will forward traffic to the target group.

And the target group attachment :

The `aws_lb_target_group_attachment` resource in Terraform allows you to attach a target to an Application Load Balancer (ALB) target group in AWS. A target is an EC2 instance, Lambda function, or container that can receive traffic from an ALB.

```
resource "aws_lb_target_group_attachment" "test" {
  target_group_arn = aws_alb_target_group.default.arn
  target_id        = var.aws_lb_target_group_attachment_instance_id
  port = var.aws_lb_target_group_attachment_port
}
```

The `target_group_arn` attribute is the ARN of the target group that the target will be attached to. The `target_id` attribute is the ID of the target that will be attached to the target group. The `port` attribute is the port on the target that will be used to receive traffic from the ALB.

Now our module is done. Let's go back to the man file and use our module

```
module "load_balancer" {
  source = "./load_balancer"

  vpc_id = data.aws_vpc.main.id
  load_balancer_name = "default"
  load_balancer_type = "application"
  security_groud_id = aws_security_group.secGroup.id
  subnets_ids = data.aws_subnets.default.ids
  aws_alb_target_group_name = "my-alb-target-group"
  aws_alb_target_group_port = 80
  aws_alb_target_group_protocol = "HTTP"
  aws_alb_target_group_type = "instance"
  aws_alb_listener_port = 80
  aws_alb_listener_protocol = "HTTP"
  aws_alb_listener_default_action_type = "forward"
  aws_lb_target_group_attachment_port = 80
  aws_lb_target_group_attachment_instance_id = aws_instance.default.id
}

```


The module `load_balancer` is a Terraform module that use to create an Application Load Balancer (ALB) in AWS. The module takes a number of arguments, including the VPC ID, the load balancer name, the load balancer type, the security group ID, the subnets IDs, the ALB target group name, the ALB target group port, the ALB target group protocol, the ALB target group type, the ALB listener port, the ALB listener protocol, the ALB listener default action type, and the ALB target group attachment port.

The module creates the following resources:

* An ALB
* An ALB target group
* An ALB listener
* An ALB target group attachment

The ALB is created in the VPC specified by the `vpc_id` argument. 

The load balancer name is specified by the `load_balancer_name` argument. 

The load balancer type is specified by the `load_balancer_type` argument.

The security group ID is specified by the `security_groud_id` argument.

The subnets IDs are specified by the `subnets_ids` argument.

The ALB target group is created with the name specified by the `aws_alb_target_group_name` argument. 

The ALB target group port is specified by the `aws_alb_target_group_port` argument.

The ALB target group protocol is specified by the `aws_alb_target_group_protocol` argument.

The ALB target group type is specified by the `aws_alb_target_group_type` argument.

The ALB listener is created with the port specified by the `aws_alb_listener_port` argument.

The ALB listener protocol is specified by the `aws_alb_listener_protocol` argument.

The ALB listener default action type is specified by the `aws_alb_listener_default_action_type` argument.


Now, type this code into the `outputs.md` file.

```
output "load_balancer_name" {
  
  value = module.load_balancer.load_balancer_dns_name
  description = "Load balancer dns name"
}
```

The `output` block in Terraform allows you to define outputs that can be used to access the values of resources or variables. 

The `value` attribute of the `output` block specifies the value of the output.

The `description` attribute of the `output` block provides a description of the output.

In this case, the `output` block defines an output named `load_balancer_name` that will contain the DNS(Domain Name System) name of the ALB created by the `load_balancer` module. 

The `value` attribute of the `output` block is set to the `load_balancer_dns_name` attribute of the `module.load_balancer` resource. 

The `description` attribute of the `output` block is set to "Load balancer dns name".

We are done provisioning all resources for our application. Now is the time to deploy and test.

## Deploying The Application

First we need to initialize our infrastructure so that terraform can download the necessary provider and module.

```
$ terraform init
```

Next 

```
$ terraform plan
```



The `terraform plan` command in Terraform creates an execution plan, which lets you preview the changes that Terraform plans to make to your infrastructure. 

By default, when Terraform creates a plan it:

* Lists all resources that will be created or modified.
* Shows the changes that will be made to each resource.
* Shows the resources that will be destroyed.

Next we need to provision our infrastructure

```
$ terraform apply
```

The `terraform apply` command applies the Terraform configuration to the target infrastructure. 

This command creates, updates, or destroys infrastructure resources based on the current state of the Terraform configuration.

You will need to provide a vpc id and enter yes when the prompt will ask.

Finally, if you want to destroy your resource.

```
terraform destroy
```

