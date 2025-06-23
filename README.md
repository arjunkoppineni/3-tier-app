Final_Project – Multi-Tier High Availability Application on AWS
📀 Overview
This project demonstrates the deployment of a multi-tier/monolithic application in a highly available architecture using Amazon Web Services (AWS). It uses CloudFormation for infrastructure-as-code (IaC), provisioning compute, networking, and database components automatically.

Frontend/Backend Deployment: Application runs in an Amazon EKS cluster across private subnets, with Container Insights enabled for detailed monitoring.

Database Layer: MySQL RDS configured in Multi-AZ mode for fault tolerance.

Networking: Private/public subnet architecture across two availability zones with NAT gateway.

Monitoring & Notifications: CloudWatch alarms and SNS for CPU utilization alerts.

🏐 Architecture Diagram
            Internet
                |
              [ IGW ]
                |
    +---------------------------+
    |  Public Subnets (2 AZs)   |
    |                           |   
    +---------------------------+
                |
          [ EKS Control Plane ]
                |
    +---------------------------+
    | Private Subnets (2 AZs, 2 per AZ) |
    |  - EKS Worker Nodes       |
    |  - CloudWatch Container   |
    |    Insights Agent         |
    |  - RDS MySQL (Multi-AZ)   |
    |  - NAT Gateway            |
    +---------------------------+

🚀 AWS Resources
💬 Networking
Resource

Details

VPC

CIDR: 10.0.0.0/16 or 20.0.0.0/18 (selectable)

Subnets

2x Public, 4x Private (spread across 2 AZs)

NAT Gateway

1, placed in public subnet

Internet Gateway

Enables internet access to public subnets

Route Tables

Configured for public/private routing

💡 Compute
Resource

Details

Amazon EKS

Cluster spans public/private subnets

Node Group

2 worker nodes (t3.medium), auto-scaled

IAM Roles

EKS Cluster Role, EKS Node Group Role with necessary policies

📂 Database
Resource

Details

Amazon RDS

MySQL, db.t3.small, Multi-AZ, 20 GB storage

Subnet Group

2 private subnets configured for high availability

Security

SG opens port 3306 for database connections

📊 Monitoring & Alerts
Feature

Description

CloudWatch Alarm

RDS CPU > 70%

CloudWatch Alarm

EKS Pod CPU > 80%

CloudWatch Container Insights

Collects metrics & logs from EKS pods/nodes

SNS

Sends alarm notifications via email

📄 CloudFormation Features
Fully parameterized template: VPC CIDR, DB credentials, email endpoint, etc.

Automatic deployment of VPC, subnets, route tables, NAT Gateway, IGW.

EKS cluster and node group with IAM roles and subnet configuration.

IAM roles and permissions configured for CloudWatch Container Insights.

RDS with DB subnet group and Multi-AZ enabled.

CloudWatch alarms configured to trigger SNS notifications.

IAM role for CloudWatch log access by EKS components.

⚙️ How to Use
Clone this repo:

git clone https://github.com/arjunkoppineni/Final_Project.git
cd Final_Project

Edit Parameters:

Modify email in MySNSEmailEndpoint

Change DB credentials if needed (MyDBRootUserName, MyDBRootUserPassword)

Deploy Stack:

Via AWS Console or:

aws cloudformation deploy \
  --template-file template.yaml \
  --stack-name my-eks-stack \
  --capabilities CAPABILITY_NAMED_IAM

Verify Email Subscription:

Check inbox for SNS confirmation link and accept subscription.

Deploy Container Insights Agent (Post-CloudFormation):

Configure kubectl: Ensure your kubectl is configured to connect to your new EKS cluster. Replace <YOUR_AWS_REGION> with your actual AWS region.

aws eks update-kubeconfig --name MyEKSCluster --region <YOUR_AWS_REGION>

Deploy the Container Insights Agent: Replace <YOUR_AWS_REGION> with your actual AWS region.

curl https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-yaml/quickstart/cwagent-fluentd-quickstart.yaml | sed "s/{{cluster_name}}/MyEKSCluster/;s/{{region_name}}/<YOUR_AWS_REGION>/" | kubectl apply -f -

This command deploys the CloudWatch agent and FluentD (for log collection) as DaemonSets across your EKS nodes, enabling Container Insights.

Use Output Values:

RDS endpoint available in CloudFormation Outputs

Connect kubectl to EKS using AWS CLI tools

🚫 Security Tips
Replace hardcoded passwords with AWS Secrets Manager.

Restrict access to RDS by whitelisting known security groups only.

Make RDS non-public if accessed only internally.

Use AWS WAF for ALB to prevent web-layer attacks.

🔢 Enhancement Ideas
Integrate ECR and CI/CD (CodePipeline, CodeBuild)

Add Route 53 DNS, ACM certificates for HTTPS

Create dashboards for application and infra monitoring

Use Karpenter or Cluster Autoscaler for EKS optimization

Implement EKS Ingress Controller (e.g., AWS Load Balancer Controller) to automatically provision ALBs.

🚀 Author
Arjun Koppineni
