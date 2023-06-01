## Deploying The Infrastructure

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
