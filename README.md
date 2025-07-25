# Terraform EC2 Instance Deployment

This repository contains Terraform configuration files to deploy an EC2 instance on AWS with a complete networking setup including VPC, subnet, security group, and internet gateway.

## Architecture

The Terraform configuration creates:
- **VPC**: A custom Virtual Private Cloud with DNS support
- **Internet Gateway**: For internet access
- **Public Subnet**: Where the EC2 instance will be deployed
- **Route Table**: To route traffic through the internet gateway
- **Security Group**: With rules for SSH (22), HTTP (80), and HTTPS (443) access
- **Key Pair**: For SSH access to the instance
- **EC2 Instance**: Amazon Linux 2 instance with Apache web server

## Prerequisites

1. **AWS CLI configured** with appropriate credentials
2. **Terraform installed** (version >= 1.0)
3. **SSH key pair** for accessing the EC2 instance

## Quick Start

1. **Clone and navigate to the directory:**
   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```

2. **Generate SSH key pair (if you don't have one):**
   ```bash
   ssh-keygen -t rsa -b 4096 -f ~/.ssh/terraform-key
   ```

3. **Create terraform.tfvars file:**
   ```bash
   cp terraform.tfvars.example terraform.tfvars
   ```

4. **Edit terraform.tfvars** and add your SSH public key:
   ```bash
   # Copy your public key content
   cat ~/.ssh/terraform-key.pub
   
   # Edit the tfvars file
   nano terraform.tfvars
   ```

5. **Initialize Terraform:**
   ```bash
   terraform init
   ```

6. **Plan the deployment:**
   ```bash
   terraform plan
   ```

7. **Apply the configuration:**
   ```bash
   terraform apply
   ```

8. **Access your instance:**
   - Web server: Use the `web_url` output
   - SSH: Use the `ssh_connection_command` output

## Configuration Variables

| Variable | Description | Default | Required |
|----------|-------------|---------|----------|
| `aws_region` | AWS region for resources | `us-west-2` | No |
| `project_name` | Project name for resource naming | `terraform-ec2-demo` | No |
| `vpc_cidr` | CIDR block for VPC | `10.0.0.0/16` | No |
| `public_subnet_cidr` | CIDR block for public subnet | `10.0.1.0/24` | No |
| `instance_type` | EC2 instance type | `t3.micro` | No |
| `public_key` | SSH public key for access | `""` | Yes |

## Outputs

After deployment, you'll see:
- Instance ID and public IP address
- SSH connection command
- Web server URL
- VPC and subnet IDs
- Security group ID

## Security Considerations

⚠️ **Important Security Notes:**

1. The security group allows SSH access from anywhere (0.0.0.0/0) - restrict this to your IP in production
2. HTTP/HTTPS ports are open to the internet - adjust based on your needs
3. Store your private SSH key securely and never commit it to version control

## Customization

### Instance Type
Change the instance type in `terraform.tfvars`:
```hcl
instance_type = "t3.small"  # or t3.medium, etc.
```

### Security Group Rules
Modify the security group in `main.tf` to restrict access:
```hcl
# Restrict SSH to your IP only
ingress {
  description = "SSH"
  from_port   = 22
  to_port     = 22
  protocol    = "tcp"
  cidr_blocks = ["YOUR_IP/32"]  # Replace with your IP
}
```

### User Data Script
The instance comes with Apache pre-installed. Modify the `user_data` section in `main.tf` to customize the setup.

## Cleanup

To destroy all resources:
```bash
terraform destroy
```

## Troubleshooting

### Common Issues

1. **AWS credentials not configured:**
   ```bash
   aws configure
   ```

2. **Terraform not found:**
   Install Terraform from [terraform.io](https://www.terraform.io/downloads.html)

3. **SSH key not working:**
   Ensure the public key in `terraform.tfvars` matches your private key

4. **Instance not accessible:**
   Check security group rules and ensure your IP is allowed

### Getting Help

- Check AWS CloudTrail for API errors
- Use `terraform plan` to preview changes
- Enable Terraform debug logging: `export TF_LOG=DEBUG`

## Cost Estimation

This configuration uses:
- 1 t3.micro EC2 instance (eligible for AWS Free Tier)
- Standard networking components (minimal cost)
- EBS storage (8GB gp2, included in Free Tier)

Estimated monthly cost: $0-10 (depending on usage and Free Tier eligibility)

## License

This project is licensed under the MIT License - see the LICENSE file for details.
