Branch-Based CI/CD Pipeline using Jenkins, Docker & GitHub Webhooks 📌 Project Overview

This project demonstrates a complete CI/CD pipeline that automatically builds, pushes, and deploys an application based on Git branch changes.

The pipeline is designed to:

Trigger automatically on GitHub push events

Build Docker images using Jenkins

Push images to Docker Hub

Deploy containers based on branch (dev / main)

🏗️ Architecture

GitHub (push) ↓

Webhook (ngrok) ↓

Jenkins Pipeline ↓

Docker Build ↓

Docker Hub Push ↓

Auto Deployment (Containers)

⚙️ Tech Stack

Version Control: Git, GitHub

CI/CD Tool: Jenkins

Containerization: Docker

Registry: Docker Hub

Webhook Exposure: ngrok

OS: Ubuntu VM

🌿 Branch-Based Deployment Strategy

Branch Environment Port Container dev Testing 8081 test-container main Production 8082 prod-container

🔄 Workflow

Step 1: Developer pushes code

git push origin dev

Step 2: GitHub Webhook triggers Jenkins

Webhook URL:

https:///github-webhook/

Step 3: Jenkins Pipeline executes

Stages: 🔹 1. Build

Builds Docker image from Dockerfile

docker build -t swedaelangovan/my-app .

🔹 2. Docker Login

Uses Jenkins credentials securely

🔹 3. Push to Docker Hub

docker push swedaelangovan/my-app

🔹 4. Deploy

For DEV:

docker rm -f test-container || true

docker run -d -p 8081:80 --name test-container swedaelangovan/my-app

For MAIN:

docker rm -f prod-container || true

docker run -d -p 8082:80 --name prod-container swedaelangovan/my-app

📄 Jenkinsfile

pipeline { agent any

environment {
    DOCKER_IMAGE = "swedaelangovan/my-app"
}

stages {

    stage('Build') {
        steps {
            sh "docker build -t $DOCKER_IMAGE ."
        }
    }

    stage('Deploy Dev') {
        when {
            expression { env.GIT_BRANCH == 'origin/dev' }
        }
        steps {
            sh '''
            docker rm -f test-container || true
            docker run -d -p 8081:80 --name test-container swedaelangovan/my-app
            '''
        }
    }

    stage('Deploy Prod') {
        when {
            expression { env.GIT_BRANCH == 'origin/main' }
        }
        steps {
            sh '''
            docker rm -f prod-container || true
            docker run -d -p 8082:80 --name prod-container swedaelangovan/my-app
            '''
        }
    }
}
}

🔐 Credentials Setup

In Jenkins:

Go to:

Manage Jenkins → Credentials

Add:

Kind: Username & Password

ID: docker-creds

Username: Docker Hub username

Password: Docker Hub access token

🌐 Webhook Setup

GitHub:

Go to: Repo → Settings → Webhooks

Add webhook:

Payload URL: https:///github-webhook/

Content type: application/json

Events: Just push event

🔗 ngrok Setup

Expose Jenkins to GitHub:

ngrok http 8080

Example output:

https://abc123.ngrok-free.app → http://localhost:8080

🐳 Docker Hub

Repository:

swedaelangovan/my-app

Tags:

latest Build-specific tags 

▶️ How to Run
1. Start Jenkins
sudo systemctl start jenkins
2. Start ngrok
ngrok http 8080
3. Push code
git commit -m "update"
git push origin dev
4. Access applications

Access applications
Dev:

(http://192.168.102.128:8081/)

Prod:

(http://192.168.102.128:8082/)
