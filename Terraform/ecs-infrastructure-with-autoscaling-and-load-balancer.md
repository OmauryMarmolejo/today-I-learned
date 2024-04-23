# AWS ECS infrastructure with autoscaling and load balancer

This is a guide to create an ECS infrastructure with autoscaling and load balancer using Terraform.

## Prerequisites

- AWS account
- Terraform installed
- AWS CLI installed and configured
- Docker installed
- Git installed
- Basic knowledge of Docker and ECS

## Resources

There are several ways to deploy an ECS infrastructure with autoscaling and load balancer. In this guide, we will use a combination of ECS services, autoscaling groups, and load balancers to create a scalable and highly available ECS infrastructure:

- VPC
- Subnets
- Internet Gateway
- Route Table
- Route Table Association
- Security Group
- Launch template
- Auto Scaling Group
- Load Balancer
- Load Balancer Listener
- Load Balancer Target Group
- ECR Repository
- ECS Cluster
- ECS Capacity Provider
- ECS Service
- ECS Task Definition
- CloudWatch Metrics Alarm
- Autoscaling Target
- Autoscaling Policy

## Step 1 - Create (or reference) a VPC

You can use the following Terraform code to create a VPC:

```hcl
resource "aws_vpc" "vpc_name" {
    cidr_block = "10.0.0.0/16"
    enable_dns_hostnames = true

    tags = {
        Name = "vpc_name"
    }
}
```

Where:

- `vpc_name` is the name of the VPC
- `cidr_block` is the CIDR block of the VPC: The CIDR block is a range of IP addresses that define the IP address space of the VPC. A common value is `10.0.0.0/16`.
- `enable_dns_hostnames` is set to `true` to enable DNS hostnames in the VPC. This is required for ECS.
- `tags` is a map of tags to assign to the VPC.

Note: In case you already have a VPC, you can reference it using its ID:

```hcl
data "aws_vpc" "vpc" {
    id = "vpc-12345678"
}
```

Where `vpc-12345678` is the ID of the VPC.

## Step 2 - Create (or reference) subnets

You can use the following Terraform code to create subnets:

```hcl
resource "aws_subnet" "private" {
    vpc_id = var.vpc_id
    cidr_block = "10.0.2.0/24"
    map_public_ip_on_launch = true
    availability_zone = "us-west-2b"
}
```

Where:

- `vpc_id` is the ID of the VPC where the subnet will be created.
- `cidr_block` is the CIDR block of the subnet.
- `map_public_ip_on_launch` is set to `true` to enable public IP addresses for instances in the subnet.
- `availability_zone` is the availability zone where the subnet will be created.

Note: In case you already have subnets, you can reference them using their IDs:

```hcl
data "aws_subnet" "private" {
    id = "subnet-12345678"
}
```

Where `subnet-12345678` is the ID of the subnet.
