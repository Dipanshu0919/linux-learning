# ⚡ Lesson 3: AWS Basics — Amazon Cloud

---

## 📚 Learning Objectives

- AWS kya hai
- Free tier use karna
- EC2 instance create karna
- AWS CLI use karna
- S3 basics

---

## ⚡ AWS kya hai?

```
Amazon Web Services = World's largest cloud platform
= 200+ services globally

Key services:
EC2         = Virtual Machine (server)
S3          = Object/File storage
RDS         = Managed databases
Lambda      = Serverless functions
ECS/EKS     = Container orchestration
CloudFront  = CDN
Route 53    = DNS management
IAM         = Identity and Access Management
```

---

## 🆓 AWS Free Tier

```
Free 12 months:
- EC2 t2.micro/t3.micro: 750 hours/month
- S3: 5 GB storage, 20,000 GET requests
- RDS: 750 hours db.t2.micro
- Lambda: 1M requests/month (always free!)

Setup: aws.amazon.com
```

---

## 🛠️ AWS CLI Setup

```bash
# Install:
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Configure:
aws configure
# AWS Access Key ID: (IAM se generate karo)
# AWS Secret Access Key: (IAM se)
# Default region: ap-south-1  (Mumbai!)
# Default output format: json

# Test:
aws sts get-caller-identity

# Version:
aws --version
```

---

## 🖥️ EC2 Instance — Launch karo

```bash
# Key pair create karo:
aws ec2 create-key-pair \
    --key-name MyLinuxKey \
    --query 'KeyMaterial' \
    --output text > MyLinuxKey.pem

chmod 400 MyLinuxKey.pem

# Security Group (firewall):
aws ec2 create-security-group \
    --group-name MyAppSG \
    --description "Flask App Security Group"

# SSH aur HTTP allow karo:
SG_ID=$(aws ec2 describe-security-groups \
    --group-names MyAppSG \
    --query 'SecurityGroups[0].GroupId' \
    --output text)

aws ec2 authorize-security-group-ingress \
    --group-id $SG_ID \
    --protocol tcp --port 22 --cidr 0.0.0.0/0

aws ec2 authorize-security-group-ingress \
    --group-id $SG_ID \
    --protocol tcp --port 80 --cidr 0.0.0.0/0

# Ubuntu 22.04 AMI ID (Mumbai):
# ami-0f58b397bc5c1f2e8

# EC2 Launch:
INSTANCE_ID=$(aws ec2 run-instances \
    --image-id ami-0f58b397bc5c1f2e8 \
    --count 1 \
    --instance-type t3.micro \
    --key-name MyLinuxKey \
    --security-group-ids $SG_ID \
    --query 'Instances[0].InstanceId' \
    --output text)

echo "Instance ID: $INSTANCE_ID"

# Public IP wait karo:
aws ec2 wait instance-running --instance-ids $INSTANCE_ID

PUBLIC_IP=$(aws ec2 describe-instances \
    --instance-ids $INSTANCE_ID \
    --query 'Reservations[0].Instances[0].PublicIpAddress' \
    --output text)

echo "Connect: ssh -i MyLinuxKey.pem ubuntu@$PUBLIC_IP"

# Stop (billing reduce!):
aws ec2 stop-instances --instance-ids $INSTANCE_ID

# Terminate (billing stop!):
aws ec2 terminate-instances --instance-ids $INSTANCE_ID
```

---

## 🪣 S3 — File Storage

```bash
# Bucket create:
aws s3 mb s3://my-unique-bucket-name-12345

# File upload:
aws s3 cp local_file.txt s3://my-bucket/
aws s3 cp image.jpg s3://my-bucket/images/
aws s3 sync ./static/ s3://my-bucket/static/    # Folder sync

# List:
aws s3 ls s3://my-bucket/
aws s3 ls s3://my-bucket/ --recursive

# Download:
aws s3 cp s3://my-bucket/file.txt ./local_file.txt

# Delete:
aws s3 rm s3://my-bucket/file.txt
aws s3 rb s3://my-bucket --force   # Bucket delete

# Static website hosting:
aws s3 website s3://my-bucket \
    --index-document index.html \
    --error-document error.html

# Make file public:
aws s3api put-object-acl \
    --bucket my-bucket \
    --key index.html \
    --acl public-read
```

---

## 🚀 Elastic Beanstalk — Easy Deploy

```bash
# EB CLI install:
pip install awsebcli

# Flask app mein:
cd my_flask_project

# EB init:
eb init -p python-3.11 my-flask-app --region ap-south-1

# Procfile banao:
echo "web: gunicorn --bind 0.0.0.0:8000 app:app" > Procfile

# Deploy:
eb create flask-production
eb open    # Browser mein kholega

# Update code:
eb deploy

# Status:
eb status

# Logs:
eb logs

# Delete:
eb terminate flask-production
```

---

## 🏋️ Practice

```bash
# AWS CLI check:
if command -v aws &>/dev/null; then
    aws --version
    echo "AWS CLI available!"

    # Regions list:
    aws ec2 describe-regions --query 'Regions[*].RegionName' --output table 2>/dev/null || echo "Login needed: aws configure"
else
    echo "Install AWS CLI first!"
fi
```

---

## 📖 Summary

```
AWS         = Amazon cloud (world's largest)
EC2         = Virtual machines
S3          = File storage
RDS         = Managed databases
Lambda      = Serverless
aws configure = CLI setup
aws ec2 run-instances = Launch VM
aws s3 cp     = File upload
ap-south-1    = Mumbai region
Free tier     = 750 hours EC2/month
```

**Next:** `09-Cloud-And-Servers/04-Server-Setup.md` → Complete server setup! 🖥️
