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

