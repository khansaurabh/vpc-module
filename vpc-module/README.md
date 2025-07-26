# VPC Terraform Module

A simple and reusable Terraform module for creating an AWS VPC with public and private subnets.

## Features

- VPC with configurable CIDR block
- Public and private subnets across multiple availability zones
- Internet Gateway for public subnet internet access
- NAT Gateways for private subnet internet access (optional)
- Route tables and associations
- Configurable DNS settings
- Flexible tagging support

## Usage

```hcl
module "vpc" {
  source = "./vpc-module"

  vpc_name           = "my-vpc"
  vpc_cidr           = "10.0.0.0/16"
  availability_zones = ["us-west-2a", "us-west-2b"]
  
  public_subnet_cidrs  = ["10.0.1.0/24", "10.0.2.0/24"]
  private_subnet_cidrs = ["10.0.10.0/24", "10.0.20.0/24"]
  
  enable_nat_gateway = true
  
  tags = {
    Environment = "production"
    Project     = "my-project"
  }
}
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| vpc_name | Name of the VPC | `string` | `"main-vpc"` | no |
| vpc_cidr | CIDR block for the VPC | `string` | `"10.0.0.0/16"` | no |
| availability_zones | List of availability zones | `list(string)` | `["us-west-2a", "us-west-2b"]` | no |
| public_subnet_cidrs | CIDR blocks for public subnets | `list(string)` | `["10.0.1.0/24", "10.0.2.0/24"]` | no |
| private_subnet_cidrs | CIDR blocks for private subnets | `list(string)` | `["10.0.10.0/24", "10.0.20.0/24"]` | no |
| enable_dns_hostnames | Enable DNS hostnames in the VPC | `bool` | `true` | no |
| enable_dns_support | Enable DNS support in the VPC | `bool` | `true` | no |
| enable_nat_gateway | Enable NAT Gateway for private subnets | `bool` | `true` | no |
| tags | A map of tags to assign to the resources | `map(string)` | `{}` | no |

## Outputs

| Name | Description |
|------|-------------|
| vpc_id | ID of the VPC |
| vpc_cidr_block | CIDR block of the VPC |
| internet_gateway_id | ID of the Internet Gateway |
| public_subnet_ids | IDs of the public subnets |
| private_subnet_ids | IDs of the private subnets |
| public_subnet_cidrs | CIDR blocks of the public subnets |
| private_subnet_cidrs | CIDR blocks of the private subnets |
| nat_gateway_ids | IDs of the NAT Gateways |
| public_route_table_id | ID of the public route table |
| private_route_table_ids | IDs of the private route tables |

## Requirements

| Name | Version |
|------|---------|
| terraform | >= 1.0 |
| aws | >= 5.0 |

## Architecture

This module creates:

1. **VPC** - Virtual Private Cloud with the specified CIDR block
2. **Internet Gateway** - Provides internet access to public subnets
3. **Public Subnets** - Subnets with direct internet access via Internet Gateway
4. **Private Subnets** - Subnets with internet access via NAT Gateway (if enabled)
5. **NAT Gateways** - One per public subnet (if enabled) for private subnet internet access
6. **Route Tables** - Proper routing for public and private subnets
7. **Elastic IPs** - For NAT Gateways (if enabled)

## Cost Considerations

- NAT Gateways incur hourly charges and data processing fees
- Set `enable_nat_gateway = false` to reduce costs if private subnets don't need internet access
- Consider using a single NAT Gateway for multiple private subnets in cost-sensitive environments
