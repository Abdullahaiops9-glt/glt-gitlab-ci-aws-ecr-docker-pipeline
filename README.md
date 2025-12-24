GitLab CI/CD → Docker → AWS ECR Pipeline
📌 Project Overview

This project demonstrates an end-to-end CI/CD pipeline using:

GitLab CI/CD

Docker (Docker-in-Docker)

AWS Elastic Container Registry (ECR)

Whenever code is pushed to the GitLab repository, the pipeline:

Builds a Docker image

Authenticates to AWS ECR

Pushes the image to ECR with a unique version tag

🏗 Architecture Flow
GitLab Repository
        |
        v
GitLab CI/CD Pipeline
        |
        v
Docker Build (DinD)
        |
        v
AWS ECR (Private Registry)

📂 Project Structure
.
├── public/
│   └── index.html
├── Dockerfile
├── .gitlab-ci.yml
└── README.md

🌐 Web Application
public/index.html
<html>
  <head>
    <h1>It works... Webpage from GitLab to AWS ECR</h1>
  </head>
</html>


This static webpage is served using Nginx inside a Docker container.

🐳 Docker Configuration
Dockerfile
FROM nginx:alpine
COPY ./public /usr/share/nginx/html


Explanation

Uses lightweight nginx:alpine

Copies static content into Nginx default HTML directory

☁️ AWS Configuration (Your Details)
✅ AWS Region
us-east-1

✅ AWS Account ID
881490088323

✅ ECR Repository Name
gitlab-ci-aws-ecr-docker-pipeline

✅ ECR Repository URI
881490088323.dkr.ecr.us-east-1.amazonaws.com/gitlab-ci-aws-ecr-docker-pipeline

🚀 GitLab CI/CD Pipeline
.gitlab-ci.yml
stages:
  - build

variables:
  DOCKER_REGISTRY: 881490088323.dkr.ecr.us-east-1.amazonaws.com
  AWS_DEFAULT_REGION: us-east-1
  APP_NAME: gitlab-ci-aws-ecr-docker-pipeline
  DOCKER_HOST: tcp://docker:2375
  DOCKER_DRIVER: overlay2
  DOCKER_TLS_CERTDIR: ""

publish:
  stage: build
  image: docker:latest
  services:
    - docker:19-dind
  before_script:
    - apk add --no-cache python3 py3-pip
    - pip install awscli
    - aws --version
    - aws ecr get-login-password --region $AWS_DEFAULT_REGION |
      docker login --username AWS --password-stdin $DOCKER_REGISTRY
    - docker info
    - docker --version
  script:
    - docker build -t $DOCKER_REGISTRY/$APP_NAME:$CI_PIPELINE_IID .
    - docker push $DOCKER_REGISTRY/$APP_NAME:$CI_PIPELINE_IID

🧠 Important Variable Breakdown
Variable	Purpose
DOCKER_REGISTRY	AWS ECR registry (account + region only)
APP_NAME	ECR repository name
CI_PIPELINE_IID	Auto-generated image version
DOCKER_HOST	Docker-in-Docker daemon
DOCKER_TLS_CERTDIR	Disables TLS for DinD
🔐 GitLab CI/CD Variables (MANDATORY)

Add these in:

GitLab → Project → Settings → CI/CD → Variables

Variable Name	Value
AWS_ACCESS_KEY_ID	IAM access key
AWS_SECRET_ACCESS_KEY	IAM secret key
AWS_DEFAULT_REGION	us-east-1

⚠️ Do NOT store credentials in YAML

🔑 AWS IAM Requirements

Create an IAM user with programmatic access and attach:

AmazonEC2ContainerRegistryPowerUser

(AdministratorAccess is NOT recommended for production.)

📦 Image Build & Push Flow

Developer pushes code to GitLab

GitLab pipeline starts automatically

Docker image is built

Image is tagged as:

<registry>/<repo>:<pipeline_id>


Image is pushed to AWS ECR

Example:

881490088323.dkr.ecr.us-east-1.amazonaws.com/gitlab-ci-aws-ecr-docker-pipeline:5

⬇️ Pull Image Locally
Login to ECR
aws ecr get-login-password --region us-east-1 \
| docker login --username AWS --password-stdin \
881490088323.dkr.ecr.us-east-1.amazonaws.com

Pull Image
docker pull 881490088323.dkr.ecr.us-east-1.amazonaws.com/gitlab-ci-aws-ecr-docker-pipeline:5

🛠 Technologies Used

GitLab CI/CD

Docker & Docker-in-Docker

AWS ECR

AWS IAM

Nginx

Alpine Linux

🎯 Use Cases

DevOps CI/CD practice

Docker & AWS ECR learning

Cloud automation demo

Interview / portfolio project

🚨 Security Notice

❌ Never share IAM passwords or keys

❌ Never commit secrets to Git

✅ Rotate keys regularly

✅ Use least-privilege IAM policies

👨‍💻 Author

DevOps CI/CD Project
GitLab → Docker → AWS ECR Automation
