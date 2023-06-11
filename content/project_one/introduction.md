## Introduction

In this project, we'll learn how to deploy an EC2(Elastic Cloud Compute) instance, connected  to a load balancer and a security group.

An EC2 instance is a virtual machine that runs on AWS infrastructure.

The Purpose of this project is to learn how to use `provider, resources, variables, outputs and modules` of terraform.

Providers are Terraform plugins that let Terraform interact with other cloud providers through APIs.

There are tons of providers to manage resources on Amazon Web Services(AWS),Azure and Google Cloud Provider(GCP) on the [Terraform Registry](https://registry.terraform.io/browse/providers?product_intent=terraform).

You can compose `resources` from different providers into reusable Terraform configurations called `modules`, and manage them with a consistent language and workflow.


![alt text](https://raw.githubusercontent.com/EducloudHQ/learning_terraform/master/assets/terraform.png)
To deploy infrastructure with Terraform:

- **Scope:** Identify the infrastructure for your project.
- **Author:** Write the configuration for your infrastructure.
- **Initialize:** Install the plugins Terraform needs to manage the infrastructure.
- **Plan:** Preview the changes Terraform will make to match your configuration.
- **Apply:** Make the planned changes.


## Solutions Architecture 

![alt text](https://raw.githubusercontent.com/EducloudHQ/learning_terraform/master/assets/tf_intro.png)