# AWS Linux Fleet Management with AWS Systems Manager

## Project Status

In Progress

## Objective

Build and operate a small Linux server fleet on AWS using AWS Systems Manager (SSM) without requiring inbound SSH access.

The project will progressively cover:

- EC2 Linux provisioning
- IAM instance profiles
- AWS Systems Manager
- Session Manager
- Run Command
- Fleet operations
- Linux administration
- Security
- Troubleshooting
- Automation
- Cost management
- Production-oriented architecture

## Environment

- AWS Region: ap-south-1
- Operating System: Amazon Linux 2023
- Instance Type: t3.micro
- Management: AWS Systems Manager
- SSH: Not required
- Repository: githubguru333/devops-lab

## Phase 0 — AWS Preflight

Completed.

Validated:

- AWS CLI authentication
- AWS account identity
- AWS region
- Existing EC2 resources
- Existing SSM managed nodes
- VPC and subnet configuration
- NAT Gateway / Elastic IP status
- EC2 AMI availability
- Instance type availability

## Phase 1 — Linux Fleet

### Linux-01

- Instance ID: i-07956d71e4bd48ec3
- Instance Type: t3.micro
- Private IP: 172.31.38.142
- Availability Zone: ap-south-1a
- OS: Amazon Linux 2023
- Root Volume: 20 GiB gp3
- Root Volume Encryption: Enabled
- IAM Instance Profile: AmazonSSMManagedInstanceProfile
- Security Group: ssm-linux-fleet-sg

### SSM Validation

SSM managed node status:

- PingStatus: Online
- SSM Agent: 3.3.4624.0

### Session Manager

Successfully established an interactive shell using:

AWS CLI → Systems Manager → SSM Agent → Linux-01

No inbound SSH access was required.

### SSM Run Command

Successfully executed a baseline diagnostic command using:

- Document: AWS-RunShellScript
- Command ID: 178387ee-36fd-4064-bb7c-17500ead3476
- Status: Success

Commands executed:

```text
hostname
uptime
df -h /
free -h

## Cost and Cleanup

This lab is designed to minimize AWS costs.

Resources created during the lab must be reviewed and removed when no longer required.

### Resources to review before cleanup

- EC2 instances
- EBS volumes
- IAM instance profile
- IAM role
- Security group
- Any future networking resources

### Cost-control decisions

- Use small EC2 instance types.
- Avoid NAT Gateway unless required for a specific lab.
- Avoid unnecessary Elastic IPs.
- Stop or terminate compute resources when the lab is complete.
- Delete unused storage and networking resources.
- Verify the AWS console after cleanup to ensure no billable resources remain.

### Cleanup principle

Every lab should have an explicit cleanup phase.

Before starting a new AWS lab, verify that resources from previous labs are either intentionally retained or deleted.