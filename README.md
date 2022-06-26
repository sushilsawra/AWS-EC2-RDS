# AWS-EC2-RDS

This is a simple Terraform automation to implement a VPC with 1 public subnet and 2 private sub-nets in the Mumbai region.
An EC2 instance has been launched on the public subnet.
A MYSQL RDS service is created on the private subnet.

###########################################################################################################################################################
1.Create our main.tf, variables.tf and output.tf files in the root folder and initialize Terraform.
2.Invoke the vpc module from our main.tf file to create a VPC.
---main.tf
provider "aws" {
  profile                 = "test"
  region                  = var.aws_region
}

module "vpc" {
    source = "../../modules/vpc"
    vpc_name = "db-sample"
    vpc_cidr = "192.168.0.0/16"
    public_cidr = "192.168.1.0/24"
    private1_cidr = "192.168.2.0/24"
    private2_cidr = "192.168.3.0/24"
    private1_az = data.aws_availability_zones.available.names[0]
    private2_az = data.aws_availability_zones.available.names[1]
    public_az = data.aws_availability_zones.available.names[0]
}

data "aws_availability_zones" "available" {
  state = "available"
}


3.Create a configuration file for EC2 instance in the root folder.
--ec2.tf

resource "aws_instance" "public-ec2" {
    ami           = var.ami_id
    instance_type = var.instance_type
    subnet_id     = module.vpc.subnet_public_id
    
    vpc_security_group_ids = [ aws_security_group.ec2-sg.id ]
    associate_public_ip_address = true

    tags = {
        Name = "ec2-main"
    }

    depends_on = [ module.vpc.vpc_id, module.vpc.igw_id, aws_db_instance.default ]

    user_data = <<EOF
#!/bin/sh
sudo apt-get update
sudo apt-get install -y mysql-client
echo ${aws_db_instance.default.address} >/tmp/dbdomain.txt
sudo mv /tmp/dbdomain.txt /dbdomain.txt
sudo apt-get install -y docker
EOF
}

resource "aws_security_group" "ec2-sg" {
  name        = "security-group"
  description = "allow inbound access to the EC2 instance"
  vpc_id      = module.vpc.vpc_id

  ingress {
    protocol    = "-1"
    from_port   = 0
    to_port     = 0
    cidr_blocks = [ "0.0.0.0/0" ]
  }

  egress {
    protocol    = "-1"
    from_port   = 0
    to_port     = 0
    cidr_blocks = ["0.0.0.0/0"]
  
  }
tags = {
        Name = "ec2-sg"
    }
}


4.


