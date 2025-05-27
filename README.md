# Static Website CI/CD Pipeline with Jenkins & Docker

## Project Description
This project demonstrates how to automate the deployment of a static website using Jenkins CI/CD pipeline, Docker, and AWS EC2.

📌 **Project Goal**
Create and deploy a static website using Jenkins pipeline. Automate:
GitHub code pull
Docker build
Docker run on EC2

🧱 **Tech Stack**
Git & GitHub
Jenkins (Installed on AWS EC2)
Docker
AWS EC2 Ubuntu Instance

## How It Works
1. Jenkins pulls code from GitHub
2. Builds Docker image
3. Runs it on EC2 using nginx


✅ Step-by-Step Guide

**Step 1: Create a Static Website**
Create a folder named static-website and add a simple index.html:

**index.html**

<!DOCTYPE html>
<html>
<head>
  <title>My DevOps Portfolio</title>
</head>
<body>
  <h1>Welcome to My Portfolio</h1>
  <p>This is deployed using Jenkins CI/CD pipeline.</p>
</body>
</html>


**Step 2: Add a Dockerfile**
This file will containerize your website using nginx.

**Dockerfile**

FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html


**Step 3: Push Code to GitHub**
Create a GitHub repo (e.g., devops-static-site)

Push your project:

```
git init
git remote add origin https://github.com/yourusername/devops-static-site.git
git add .
git commit -m "Initial commit: static site with Dockerfile"
git push -u origin master
[git pull origin master --rebase]
[git push -u origin master]

or

[git push -u origin master --force]


**Step 4: Set Up Jenkins & Docker on AWS EC2**
Install Jenkins & Docker on your EC2 instance:

# Update system
sudo apt update && sudo apt upgrade -y

# Install Java (needed for Jenkins)
sudo apt install openjdk-11-jdk -y

# Install Jenkins
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins -y
sudo systemctl start jenkins
sudo systemctl enable jenkins

# Open port 8080 in EC2 security group

# Install Docker
sudo apt install docker.io -y
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins


**Step 5: Create Jenkins Pipeline**
Open Jenkins at http://<your-ec2-public-ip>:8080

Install required plugins during setup

Create a new Pipeline project

Add this Jenkinsfile to your repo or use it directly in Jenkins:

```
pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/yourusername/devops-static-site.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build('my-static-site')
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    sh 'docker stop static-site || true'
                    sh 'docker rm static-site || true'
                    sh 'docker run -d --name static-site -p 80:80 my-static-site'
                }
            }
        }
    }
}

