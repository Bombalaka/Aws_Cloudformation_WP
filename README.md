# WordPress Cloud Architecture on AWS

## Overview
This CloudFormation template deploys a **production-ready WordPress environment** on AWS with automated scaling, high availability, and shared storage.

## Architecture Features
- **Auto Scaling Group** - Automatically scales EC2 instances (2-4+) based on CPU utilization
- **Application Load Balancer** - Distributes traffic across healthy instances
- **EFS Shared Storage** - Enables file sharing across all WordPress instances
- **RDS MariaDB** - Managed database with automated backups
- **LAMP Stack** - Automated installation (Linux, Apache, MariaDB, PHP)
- **Multi-AZ Deployment** - High availability across availability zones
- **Security Groups** - Layered network security (5 specialized groups)

## Quick Deploy
```bash
aws cloudformation create-stack \
  --template-body file://template.yaml \
  --stack-name WordPress-Infrastructure
```

