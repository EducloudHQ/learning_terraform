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
