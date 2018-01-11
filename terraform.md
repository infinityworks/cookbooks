# Terraform Style Guide

This repository gives coding conventions for Terraform's HashiCorp Configuration Language (HCL). Terraform allows infrastructure to be described as code. As such, we should adhere to a style guide to ensure readable and high quality code.

## Table of Contents

* [Editor](#editor)
* [Syntax](#Syntax)
* [Naming](#Naming)
* [Environments](#Environments)

## Editor

VSCode works well with the [Terraform extension](https://github.com/mauve/vscode-terraform)

## Links and Info

[Awesome Terraform](https://github.com/shuaibiyy/awesome-terraform)

## Syntax

Use 2 spaces when defining resources except when defining inline policies or other inline resources.

```
resource "aws_iam_role" "iam_role" {
  name = "${var.resource_name}-role"
  assume_role_policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": "sts:AssumeRole",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Effect": "Allow",
      "Sid": ""
    }
  ]
}
EOF
}
```


When commenting use two "//" and a space in front of the comment.

```
// Create ELK Auto Scaling Group Resource
...
```

## Naming

### Resource and Variable Names

Only use "\_" (underscore/underbar) when naming Terraform resource TYPES, NAMES, etc  
Only use "-" (dash/hyphen/minus sign/etc) when naming the created resources.

```
resource "aws_security_group" "security_group" {
  name = "${var.resource_name}-security-group"
...
```

A resource's NAME should be the same as the TYPE minus the provider.

```
resource "aws_autoscaling_group" "autoscaling_group" {
...
```

If there are multiple resources of the same TYPE defined, add a minimalistic identifier to differentiate between the two resources. A blank line should sperate resource definitions contained in the same file.

```
// Create Data S3 Bucket
resource "aws_s3_bucket" "data_s3_bucket" {
  bucket = "${var.environment_name}-data-${var.aws_region}"
  acl = "private"
  versioning {
    enabled = true
  }
}

// Create Images S3 Bucket
resource "aws_s3_bucket" "images_s3_bucket" {
  bucket = "${var.environment_name}-images-${var.aws_region}"
  acl = "private"
}
```

## Files

### Variables

Variables should be provided in a `variables.tf` file at the root of your project.

A description should be provided for each declared variable.

```hcl
// bad
variable "vpc_id" {}

// good
variable "vpc_id" {
  description = "The VPC this security group will go in"
}
```

### Outputs

Outputs should be provided in an `outputs.tf` file at the root of your project.

## Environments

Use the `workspaces` feature of terraform to manage multiple environments.

### Environment Specific Variables

For variables that differ among the variables use a `tfvars` file to hold them and then pass it on the command line when invoking commands: `terraform plan -var-file=dev.tfvars`

### Scripting

Helpful bash script to enforce workspace check when running commands:

```bash
#/bin/bash

workspace=$(terraform workspace show)
if [ $workspace != "dev" ]; then
    echo "Workspace is not DEV!"
    exit 64
fi
terraform plan -var-file=dev.tfvars
```