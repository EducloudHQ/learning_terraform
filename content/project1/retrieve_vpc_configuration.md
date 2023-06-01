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
