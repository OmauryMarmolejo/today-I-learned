# The state file and how to manage it in a CI/CD pipeline

## Introduction

In Terraform, the "state" is a file that keeps track of the resources it has created and their current configuration. We can think of it as a detailed inventory of everything Terraform has provisioned in your infrastructure. (e.g, EC2 instances, VPCs, security groups, etc.)

The state file is essential because it allows Terraform to understand the current state of the infrastructure and determine what changes need to be made when you modify your configuration files (e.g, adding or removing resources, changing resource settings, etc.)

The state file also helps Terraform understand dependencies between resources. For example, if you have an ECS task definition that depends on an Elastic Load Balancer (ELB) target group, Terraform can use the state file to ensure the ELB target group is created before the task definition, avoiding potential errors.

## The state file in the CI/CD cycle

When using a CI/CD pipeline like Github Actions to deploy the Terraform infrastructure, the recommended approach for handling the state file is to use a remote backend, such as AWS S3, Azure Storage, or Google Cloud Storage.

Let's say we want to use Github Actions and AWS S3:

1. Configure a remote backend: In the Terraform configuration files, specify a remote backend to store the state file:

```hcl
terraform {
    backend "s3" {
        bucket = "the-name-of-the-state-bucket"
        key    = "the-path-to-the-state-file.tfstate"
        region = "us-west-2" # or any other region
    }
}
```

2. Create the Github Actions workflow: In your workflow file yo'll need to set up the necessary steps to run Terraform commands. Here's an example of a Github Actions workflow that runs Terraform commands. We need:
   1. Set up credentials: You'll need to use the necessary credentials in the Github Actions workflow to be able to access the S3 bucket. For S3, you can use the `aws-actions/configure-aws-credentials` Github Action to set up the AWS credentials.
   2. Set up Terraform: Use the `hashicorp/setup-terraform` Github Action to set up Terraform.
   3. Run Terraform commands: Use the `terraform init`, `terraform plan`, and `terraform apply` commands to initialize Terraform, create an execution plan, and apply the changes to the infrastructure.
      1. Make sure to replace the `terraform apply` command with `terraform apply -auto-approve` to automatically approve the changes without user interaction.
   4. Additionally, we need to add the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY as secrets in the Github repository settings.

```yaml
name: Deploy ECS Fargate Infrastructure
on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-west-2

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v2

      - name: Terraform Init
        run: terraform init

      - name: Terraform Plan
        run: terraform plan

      - name: Terraform Apply
        run: terraform apply -auto-approve
```
