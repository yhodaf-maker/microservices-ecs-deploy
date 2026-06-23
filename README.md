# Inventory & Orders Microservices Platform

A cloud-native microservices application deployed on AWS ECS Fargate using a fully automated CI/CD pipeline with GitHub Actions and OIDC authentication.

This project demonstrates how to build, containerize, deploy, and operate multiple services on AWS using modern DevOps practices without storing AWS access keys.

---

# Project Overview

The platform consists of two Python Flask microservices:

### Inventory Service

The Inventory Service maintains product availability information and exposes an API used by other services to determine whether a product is currently in stock.

Example inventory data:

```json
{
  "widget": true,
  "gadget": false
}
```

---

### Orders Service

The Orders Service receives customer orders and validates inventory availability before confirming or rejecting the order.

Example request:

```http
POST /orders
```

```json
{
  "sku": "widget",
  "quantity": 2
}
```

Possible responses:

```json
{
  "status": "confirmed"
}
```

or

```json
{
  "status": "backordered"
}
```

The Orders Service depends on the Inventory Service and communicates with it through AWS Service Connect and Cloud Map service discovery.

---

# Architecture

```text
                ┌─────────────────┐
                │     Client      │
                └────────┬────────┘
                         │
                         ▼
          ┌─────────────────────────────┐
          │ Application Load Balancer   │
          └──────────────┬──────────────┘
                         │
                         ▼
                ┌─────────────────┐
                │ Orders Service  │
                │   ECS Fargate   │
                └────────┬────────┘
                         │
                         │ Service Connect
                         │
                         ▼
                ┌─────────────────┐
                │ Inventory       │
                │ Service         │
                │ ECS Fargate     │
                └─────────────────┘
```

---

# AWS Infrastructure

The platform is deployed entirely on AWS and includes the following components:

- Amazon ECS Fargate
- Amazon ECR
- Application Load Balancer (ALB)
- AWS Cloud Map
- AWS Service Connect
- CloudWatch Logs
- IAM Roles
- GitHub OIDC Provider
- ECS Task Definitions
- ECS Services
- Security Groups

---

# CI/CD Pipeline

Deployments are fully automated using GitHub Actions.

Every push to the `main` branch triggers a deployment pipeline that:

1. Checks out the source code
2. Authenticates to AWS using OIDC
3. Builds Docker images
4. Pushes images to Amazon ECR
5. Updates ECS Task Definitions
6. Deploys new revisions to ECS Fargate
7. Waits for service stability validation

No AWS access keys or secrets are stored inside GitHub.

Authentication is performed using OpenID Connect (OIDC) and temporary AWS credentials.

---

# Deployment Flow

```text
Developer
    │
    ▼
Git Push
    │
    ▼
GitHub Actions
    │
    ├── Build Docker Images
    │
    ├── Push Images to Amazon ECR
    │
    ├── Render ECS Task Definitions
    │
    └── Deploy to ECS Fargate
                │
                ▼
        Running Services
```

---

# Repository Structure

```text
.
├── .github
│   └── workflows
│       └── deploy.yml
│
├── inventory-service
│   ├── app.py
│   ├── Dockerfile
│   ├── requirements.txt
│   └── task-definition.json
│
├── orders-service
│   ├── app.py
│   ├── Dockerfile
│   ├── requirements.txt
│   └── task-definition.json
│
└── README.md
```

---

# Technologies Used

### Programming

- Python
- Flask

### Containers

- Docker

### AWS

- Amazon ECS Fargate
- Amazon ECR
- Application Load Balancer
- Cloud Map
- Service Connect
- IAM
- CloudWatch Logs

### CI/CD

- GitHub Actions
- OpenID Connect (OIDC)

---

# Key DevOps Concepts Demonstrated

- Containerization using Docker
- Microservices architecture
- Service-to-service communication
- Service discovery using AWS Cloud Map
- AWS Service Connect
- Infrastructure deployment on ECS Fargate
- Application Load Balancing
- Continuous Integration and Continuous Deployment
- Secure AWS authentication using OIDC
- Immutable deployments using image tags
- Centralized logging with CloudWatch

---

# Local Development

Build Inventory Service:

```bash
docker build -t inventory-service ./inventory-service
```

Build Orders Service:

```bash
docker build -t orders-service ./orders-service
```

Run locally using Docker Compose:

```bash
docker compose up --build
```

---

# Example API Calls

Health Check:

```bash
curl http://localhost:8080/health
```

Create Order:

```bash
curl -X POST http://localhost:8080/orders \
-H "Content-Type: application/json" \
-d '{"sku":"widget","quantity":2}'
```

Expected Response:

```json
{
  "status": "confirmed"
}
```

---

# Learning Objectives

This project was created to demonstrate a complete deployment workflow for microservices running on AWS.

The focus areas include:

- Designing containerized services
- Deploying workloads on ECS Fargate
- Implementing service discovery
- Building secure CI/CD pipelines
- Using GitHub Actions with AWS OIDC
- Automating deployments without long-lived credentials

---

# Future Improvements

Potential enhancements include:

- Infrastructure as Code using Terraform
- Blue/Green deployments
- Automated testing stages
- AWS WAF integration
- ECS Auto Scaling
- Monitoring dashboards with CloudWatch
- Distributed tracing and observability

---

# Author

Yehuda Feder

DevOps Engineer in Training | AWS | Docker | Kubernetes | CI/CD | GitHub Actions