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
