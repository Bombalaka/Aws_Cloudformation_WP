# WordPress on AWS

## 🎯 What This Project Does

This CloudFormation template creates a **production-ready WordPress website** on AWS that can automatically handle traffic spikes, share files between multiple servers, and keep your data safe. Think of it as building a complete WordPress hosting environment in the cloud!

## 🏗️ Architecture Overview

Let me explain what each component does and **why** we need it:

### 🖥️ **Auto Scaling Group (ASG)**
- **What it does**: Automatically creates 2-4 EC2 servers based on how busy your website is
- **Why we need it**: When many people visit your site, AWS automatically adds more servers. When traffic is low, it removes servers to save money
- **How it works**: Monitors CPU usage - if it goes above 50%, it adds a server. If it goes below 50%, it removes one

### ⚖️ **Application Load Balancer (ALB)**
- **What it does**: Acts like a traffic director, sending visitors to the least busy server
- **Why we need it**: Without this, all visitors would go to one server and crash it. This spreads the load evenly
- **How it works**: Like a smart receptionist who directs people to available staff members

### 📁 **EFS (Elastic File System)**
- **What it does**: Shared storage that all your WordPress servers can access
- **Why we need it**: When you upload a photo or install a plugin, ALL servers can see it immediately
- **How it works**: Like a shared Google Drive that all your servers can read and write to

### 🗄️ **RDS MariaDB Database**
- **What it does**: Stores all your WordPress content (posts, pages, comments, user accounts)
- **Why we need it**: WordPress needs a database to remember everything. RDS is AWS's managed database service
- **How it works**: Like a super-organized filing cabinet that never loses your data

### 🔒 **Security Groups (5 Different Ones)**
- **What they do**: Act like digital bouncers, controlling who can access what
- **Why we need them**: Security! Each component has different access rules:
  - **ASG Security Group**: Allows web traffic (port 80) and SSH (port 22)
  - **Load Balancer Security Group**: Only allows web traffic (port 80)
  - **Database Security Group**: Only allows database connections (port 3306) from WordPress servers
  - **Deploy Security Group**: Temporary access for initial setup
  - **EFS Security Group**: Only allows file sharing (port 2049) between servers

## 🚀 How to Deploy

### Prerequisites
Before running this template, you need:
1. **AWS CLI configured** with your credentials
2. **VPC and Subnets** already created in your AWS account
3. **EC2 Key Pair** for SSH access
4. **Amazon Linux 2023 AMI ID** for your region

### Step 1: Update the Template
Edit `WP_template.yaml` and replace these placeholders with your actual values:
```yaml
# Replace these with your actual AWS resource IDs:
VpcId: vpc-12345678          # Your VPC ID
Subnet1: subnet-12345678     # Your first subnet ID
Subnet2: subnet-87654321     # Your second subnet ID  
Subnet3: subnet-11223344     # Your third subnet ID
ImageId: ami-12345678        # Amazon Linux 2023 AMI ID
KeyName: your-key-pair-name  # Your EC2 key pair name
MasterUserName: admin        # Database username
MasterUserPassword: password123  # Database password
```

### Step 2: Deploy the Stack
```bash
aws cloudformation create-stack \
  --template-body file://WP_template.yaml \
  --stack-name MyWordPressSite \
  --capabilities CAPABILITY_IAM
```

### Step 3: Wait for Completion
```bash
# Check stack status
aws cloudformation describe-stacks --stack-name MyWordPressSite

# Wait until Status shows "CREATE_COMPLETE"
```

### Step 4: Access Your Website
After deployment, you'll get a Load Balancer DNS name. Visit that URL to see your WordPress site!

## 🔧 What Happens During Deployment

1. **Database Creation**: RDS creates a MariaDB database
2. **EFS Setup**: Creates shared file storage with mount points in all subnets
3. **Deploy Instance**: A temporary server installs WordPress and configures the database
4. **Load Balancer**: Creates the traffic director
5. **Auto Scaling Group**: Launches 2 WordPress servers that connect to shared storage
6. **Cleanup**: The deploy instance shuts down after 20 minutes

## 💰 Cost Management

**⚠️ IMPORTANT**: This setup costs money! Always delete the stack when done:

```bash
aws cloudformation delete-stack --stack-name MyWordPressSite
```

## 🛠️ Troubleshooting

### Common Issues:
1. **Stack fails to create**: Check that all your resource IDs are correct
2. **Can't access website**: Wait 10-15 minutes for all services to start
3. **Database connection errors**: Ensure security groups allow port 3306
4. **EFS mount failures**: Check that EFS security group allows port 2049

### Useful Commands:
```bash
# Check stack events for errors
aws cloudformation describe-stack-events --stack-name MyWordPressSite

# Get stack outputs (including website URL)
aws cloudformation describe-stacks --stack-name MyWordPressSite --query 'Stacks[0].Outputs'

# Check running instances
aws ec2 describe-instances --filters "Name=tag:Name,Values=MyAutoscalingGroup-Instance"
```

## 📚 Next Steps for Learning

1. **Monitor your stack** in the AWS Console
2. **Test auto-scaling** by generating load
3. **Explore CloudWatch** for monitoring and logs
4. **Learn about RDS backups** and point-in-time recovery
5. **Study security best practices** for production deployments



