# DevOps Code Challenge

## Project Overview
This project deploys a containerized React frontend and Express backend to AWS. The application runs on Amazon ECS Fargate behind an Application Load Balancer. Docker is used for containerization, Amazon ECR stores images, Jenkins automates build and deployment, and Terraform provisions all infrastructure.

---

## Architecture
When a user accesses the application, the request first reaches an Application Load Balancer (ALB). The ALB routes frontend traffic to the React service and API requests to the Express backend service.

Both services run as containers on ECS Fargate. Images are stored in Amazon ECR. Jenkins builds and deploys updated images, and Terraform manages all AWS resources including networking, compute, and load balancing.

---

## Tech Stack
- React (Frontend)
- Node.js / Express (Backend)
- Docker
- Amazon ECR
- Amazon ECS Fargate
- Application Load Balancer
- Jenkins
- Terraform

---

## CI/CD Pipeline
Jenkins is used to automate deployments.

Pipeline flow:
1. Pull latest code from GitHub  
2. Build Docker images (frontend and backend)  
3. Authenticate to Amazon ECR  
4. Push images to ECR  
5. Force new ECS deployment  

This removes manual deployment steps and ensures consistency.

---

## Infrastructure (Terraform)
Terraform provisions:

- VPC with public and private subnets  
- Internet Gateway and NAT Gateway  
- Security Groups  
- Application Load Balancer  
- ECS Cluster  
- ECS Services (frontend and backend)  
- ECR repositories  

This allows the environment to be recreated consistently.

---

## Auto Scaling
ECS services use target tracking auto scaling based on CPU utilization.

- Minimum tasks: 1  
- Maximum tasks: 4  
- Metric: ECSServiceAverageCPUUtilization  

During load testing, the service scaled from 1 task to multiple tasks. New tasks entered the Pending state and then became Running, confirming scaling behavior.

There is a delay between load and scaling due to CloudWatch metric collection and task startup time.

---

## Deployment Verification
The deployment was verified by:

- Accessing the frontend through the ALB  
- Calling the backend API and receiving a UUID response  
- Observing successful Jenkins pipeline runs  
- Confirming ECS tasks are running  
- Observing scaling activity under load  

Test command:

```bash
curl http://devops-challenge-alb-542438279.us-east-2.elb.amazonaws.com/api/
```
---


## GitOps Bonus Implementation

A GitOps-based CI/CD pipeline was implemented using GitHub Actions on a dedicated `gitops` branch.

### Key Features
- OIDC authentication between GitHub and AWS (no static credentials)
- Automated Docker image builds for frontend and backend
- Image push to Amazon ECR
- Automated ECS service deployments via AWS CLI

### Workflow
1. Code is pushed to the `gitops` branch
2. GitHub Actions workflow triggers
3. AWS credentials are assumed via OIDC
4. Docker images are built and pushed to ECR
5. ECS services are updated with new deployments

### Architecture
- AWS ECS (Fargate) for container orchestration
- Application Load Balancer (ALB) for routing
- Amazon ECR for container registry
- Terraform for infrastructure provisioning

### Branch Strategy
- `main` branch: Jenkins-based CI/CD implementation
- `gitops` branch: GitHub Actions GitOps implementation
