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
