# Jenkins CI/CD Pipeline on AWS EC2

A fully automated CI/CD pipeline built with **Jenkins** hosted on **AWS EC2**, that automatically clones, builds, containerizes, and deploys a Node.js REST API using **Docker**.

## 🏗️ Architecture

```
Developer pushes code to GitHub
        ↓
   Jenkins (on AWS EC2)
   detects/triggers build
        ↓
  Clone Repository from GitHub
        ↓
  Build Docker Image
        ↓
  Stop & Remove old container
        ↓
  Run new Docker container
        ↓
  Health Check via curl
        ↓
  API Live on port 3000 ✅
```

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| Jenkins | CI/CD Automation Server |
| AWS EC2 | Cloud Server (Ubuntu 24.04) |
| Docker | Containerization |
| Node.js + Express | REST API |
| GitHub | Source Code Repository |
| Linux (Ubuntu) | Server OS |

## ⚙️ Pipeline Stages

| Stage | Description |
|-------|-------------|
| Clone Repository | Pulls latest code from GitHub main branch |
| Build Docker Image | Builds Docker image from Dockerfile |
| Run Container | Stops old container and runs new one |
| Health Check | Verifies API is live via curl |

## 📋 Jenkinsfile

```groovy
pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/saichaitanya10/devops-api.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t devops-api:latest .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker stop devops-api || true'
                sh 'docker rm devops-api || true'
                sh 'docker run -d -p 3000:3000 --name devops-api devops-api:latest'
            }
        }

        stage('Health Check') {
            steps {
                sh 'sleep 5'
                sh 'curl -f http://localhost:3000 || exit 1'
            }
        }
    }
}
```

## 🚀 Setup Instructions

### 1. Launch EC2 Instance
- AMI: Ubuntu Server 24.04 LTS
- Instance type: t2.micro
- Security group: Allow ports 22 (SSH) and 8080 (Jenkins)

### 2. Install Java & Jenkins
```bash
sudo apt update -y
sudo apt install fontconfig openjdk-21-jre -y
sudo apt install jenkins -y
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

### 3. Install Docker
```bash
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker jenkins
sudo chmod 666 /var/run/docker.sock
```

### 4. Create Pipeline Job
- New Item → Pipeline
- Paste Jenkinsfile content in Pipeline Script
- Click Save → Build Now

## 📁 Project Structure

```
jenkins-cicd/
├── Jenkinsfile        # Jenkins pipeline definition
└── README.md
```

## ✅ Build Results

- Successfully cloned repository from GitHub
- Built Docker image with Node.js 18 Alpine
- Deployed container on port 3000
- Health check passed with API response:
```json
{"status":"OK","message":"DevOps API is running!","version":"1.0.0"}
```