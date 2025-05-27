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
```
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
```

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
```

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


**Login to jenkins again**

Install the required plugins
✅ Minimum Required Jenkins Plugins

```
| Plugin Name             | Purpose                                                      |
| ----------------------- | ------------------------------------------------------------ |
| **Git plugin**          | Allows Jenkins to pull code from GitHub                      |
| **Pipeline**            | Enables Jenkins to use Declarative and Scripted pipelines    |
| **Docker Pipeline**     | Required to run Docker commands in the pipeline              |
| **Docker Commons**      | Shares Docker configuration between Jenkins and jobs         |
| **Credentials Binding** | (Optional) Securely pass GitHub tokens or Docker credentials |
| **AnsiColor**           | (Optional) Improves color display in console logs            |
```


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
```

**🧪 Test It!**
Visit http://<your-ec2-public-ip>

You should see your static website!


**📁 Final GitHub Structure**

```
static-website/
├── index.html
├── Dockerfile
├── Jenkinsfile
└── README.md
```


**#"Why" and "Where"**


🎯 1. What is the Project About (in Simple Terms)?
This project helps you:

Build a simple website.

Automatically deploy it using Jenkins whenever you update the code on GitHub.

Host it on a server (EC2) using Docker.

📌 In short: You're learning how DevOps engineers automate deployments.


🧠 2. Why is This Project Useful?

| 🔧 Skill         | What You'll Learn                                     |
| ---------------- | ----------------------------------------------------- |
| **Git & GitHub** | Version control + remote repo handling                |
| **Jenkins**      | Automating builds & deployments (CI/CD pipeline)      |
| **Docker**       | Packaging the app for consistency across environments |
| **AWS EC2**      | Hosting the application on a cloud server             |



🏢 3. Where Is This Used in Real Life?
Here’s how companies use similar setups:

Company	What They Might Do
A Startup	Deploy a landing page automatically when devs push code
An E-commerce Site	Show offers, blogs, or static FAQs that are updated via GitHub
DevOps Teams	Test automation pipelines for frontend changes

📍Use Cases:

Personal portfolios

Company landing pages

Static documentation sites (like GitBook or Jekyll)

Any site that doesn't need a backend or database


📌 4. How This Fits in DevOps Lifecycle
Phase	What You Do in This Project
Plan	Create a simple static website
Develop	Push code to GitHub
Build	Jenkins builds a Docker image
Test	Not applicable (optional for static)
Release	Jenkins runs the container on EC2
Deploy	Website is live on the cloud
Monitor	(Optional for static, but possible with Prometheus/Grafana later)



