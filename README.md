# AWS-EC2-RDS

This is a simple Terraform automation to implement a VPC with 1 public subnet and 2 private sub-nets in the Mumbai region.
An EC2 instance has been launched on the public subnet.
A MYSQL RDS service is created on the private subnet.

################################################################################

1.Create our main.tf, variables.tf and output.tf files in the root folder and initialize Terraform.   

2.Invoke the vpc module from our main.tf file to create a VPC.   
 
3.Create a configuration file for EC2 instance in the root folder.

4. Initialize and setup the RDS database instance.

5. terraform apply.
