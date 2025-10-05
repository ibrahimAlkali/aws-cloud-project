# AWS 8-Week Roadmap – Final Project

This repository documents my journey through an **8-week AWS Cloud Engineering Roadmap** and my final **Cloud Notes App infrastructure project**.  
It includes Infrastructure-as-Code (CloudFormation templates), test evidence (screenshots), and supporting documentation.

The final deployment demonstrates a modular AWS architecture with a **VPC, Bastion Host, Application Load Balancer (ALB), Auto Scaling Group (ASG), RDS MySQL Database, and CloudWatch Monitoring**.  
All resources were deployed and verified through CloudFormation using best practices for subnet isolation and least-privilege access.

---

## Repository Structure
```
aws-final-project/
├── templates/ # Modular CloudFormation stacks
│ ├── vpc-foundation.yaml # VPC, subnets, route tables, security groups
│ ├── web-tier.yaml # ALB, Launch Template, ASG, Bastion host
│ └── rds-tier.yaml # RDS MySQL instance in private subnets
│
├── screenshots/ # Blurred AWS Console + CLI proofs
│ ├── 01-cloudformation.png # 3 stacks in CREATE_COMPLETE
│ ├── 02-vpc-resource-map.png
│ ├── 03-sg-bastion.png
│ ├── 04-sg-web.png
│ ├── 05-sg-db.png
│ ├── 06-bastion-ssh.png
│ ├── 07-asg.png
│ ├── 08-alb.png
│ ├── 09-target-group.png
│ ├── 10-curl-test.png
│ ├── 11-rds.png
│ ├── 12-mysql-connect.png
│ ├── 13-cloudwatch-alarm.png
│ ├── 14-sns-email.png
│ ├── 15-tags.png
│ └── 16-delete-complete.png # optional
│
├── 8-Week AWS Roadmap Overview.pdf # Combined handbook for Weeks 1–8
└── README.md

```
---

## Final Architecture Features

- **Networking**
  - Custom VPC with **2 public** and **2 private** subnets (multi-AZ)
  - Internet Gateway for public subnet access
  - Private RDS and web servers — no NAT Gateway used (full internal isolation)

- **Security**
  - Separate **Security Groups** for Bastion, Web, and DB tiers
  - SSH access restricted to the Bastion host only
  - DB accessible only from WebSG on port 3306

- **Compute**
  - Auto Scaling Group (ASG) hosting Nginx-based web servers
  - Application Load Balancer (ALB) spanning 2 public subnets
  - Launch Template installs PHP Notes App automatically via user data

- **Database**
  - RDS MySQL instance in private subnets
  - Not publicly accessible, verified via Bastion

- **Monitoring**
  - CloudWatch CPU alarm with SNS email notification
  - Alarm tested by generating temporary CPU load

---

## Tests and Evidence

All screenshots in `/screenshots` demonstrate successful validation of each layer:

1. CloudFormation – 3 stacks deployed successfully  
2. VPC layout – subnets, routes, and IGW  
3. Security Groups – Bastion, Web, and DB with correct inbound rules  
4. SSH access – confirmed Bastion login  
5. Auto Scaling – 2 healthy EC2 instances in separate AZs  
6. ALB DNS – accessible via Bastion using `curl`  
7. RDS – private endpoint accessible only inside VPC  
8. CloudWatch Alarm – email alert received after CPU stress test  
9. Tags – consistent resource tagging across stacks  

---

## Documentation

- **8-Week AWS Roadmap Overview (PDF):**  
  Combined handbook summarising all eight weeks of learning and implementation — IAM, VPC, EC2, RDS, ALB, ASG, and monitoring.

---

## Usage

To deploy a stack:

```bash
1.
aws cloudformation create-stack \
  --stack-name vpc-foundation \
  --template-body file://templates/vpc-foundation.yaml \
  --capabilities CAPABILITY_IAM
2.
aws cloudformation create-stack \
  --stack-name rds-tier \
  --template-body file://templates/rds-tier.yaml \
  --capabilities CAPABILITY_IAM
3.
aws cloudformation create-stack \
  --stack-name web-tier \
  --template-body file://templates/web-tier.yaml \
  --capabilities CAPABILITY_IAM
```
Outputs include:

ALB DNS – Web entry point

Bastion IP – SSH access

DB Endpoint – Private MySQL address

## Author 
Ibrahim Alkali: MEng Electronic & Software Engineering – University of Aberdeen

## Notes

All sensitive data (IPs, ARNs, account IDs, and emails) are blurred in screenshots.

Templates and configurations are for educational and demonstrative use only.

NAT Gateway intentionally omitted — private tier remains isolated for cost efficiency.
