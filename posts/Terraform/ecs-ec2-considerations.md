# ECS EC2 considerations found in the wild

## About the ECS agent registration

When using ECS with EC2 instance is important to add a ecs.sh file with the following content:

```bash
#!/bin/bash
echo ECS_CLUSTER=${ecs_cluster_name} >> /etc/ecs/ecs.config
```

This file should be added to the user data of the EC2 instance. It's important because the ECS agent will not be able to connect to the cluster without this configuration.

---

### About choose the correct image

When choosing the AMI for the EC2 instance, it's important to choose the ECS optimized AMI. This AMI already comes with the ECS agent installed and configured.

You can find the ECS optimized AMI using the following code in Terraform:

```hcl
data "aws_ami" "amzn" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-ecs-hvm*"]
  }

  filter {
    name   = "architecture"
    values = ["x86_64"]
  }
}

```
