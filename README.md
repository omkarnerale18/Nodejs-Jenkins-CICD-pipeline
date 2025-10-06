# Jenkins CI/CD Pipeline for Node.js Application with Docker  

This repository demonstrates a robust **CI/CD pipeline** built with Jenkins to automate the build, test, and deployment of a Node.js application inside a Docker container.  

The pipeline is designed to streamline application delivery by performing the following steps automatically:  

- Pull code from a Git repository  
- Install Node.js dependencies  
- Execute tests  
- Build a Docker image  
- Deploy the application in a container  
- Cleanup resources after deployment  

---

## 🚀 Prerequisites  

Before running the pipeline, ensure you have the following installed and configured:  

- **Docker Desktop** (with Docker daemon exposed on `tcp://localhost:2375`)  
- **Jenkins running in Docker** with:
  - Docker CLI installed
  - Node.js installed (via Dockerfile or plugin)  
- Required Jenkins plugins:
  - Pipeline
  - Git
  - Docker Pipeline
  - NodeJS  

---

## ⚙️ Jenkins Setup  

To set up Jenkins for this pipeline:  

1. **Enable Docker Daemon Exposure**  
   Open Docker Desktop → Settings → General → enable:  
   ✅ *"Expose daemon on tcp://localhost:2375 without TLS"*  
   Restart Docker Desktop to apply changes.  

2. **Build and Run Jenkins Docker Image**  
```powershell
docker build -t jenkins-node "C:\Users\Asus\OneDrive\Desktop\Jenkins-Docker-setup"
docker run -d --name jenkins -p 8055:8080 -p 50000:50000 -v "C:\jenkins_home:/var/jenkins_home" -e DOCKER_HOST=tcp://host.docker.internal:2375 jenkins-node

```
 ** Verify Jenkins is Running**
   ```powershell
   http://localhost:8055
   ```

## 🐳 Dockerfile (Used to Build Jenkins Image)
```powershell
FROM jenkins/jenkins:lts

USER root

# Install Docker CLI, curl, gnupg and Node.js
RUN apt-get update && \
    apt-get install -y docker.io curl gnupg && \
    curl -fsSL https://deb.nodesource.com/setup_18.x | bash - && \
    apt-get install -y nodejs

USER jenkins
```
This Dockerfile ensures Jenkins has the ability to:
- Run Docker commands
- Install and run Node.js applications
- Manage pipeline automation tasks

## 📝 Pipeline Logic
 ```powershell
pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS-24'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/omkarnerale18/Nodejs-Jenkins-CICD-pipeline.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                // Optional if you have no tests; comment out if not needed
                sh 'npm test || echo "No tests found"'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t node-jenkins-email-app .'
            }
        }

        stage('Run Docker Container') {
            steps {
                // Stop and remove any existing container to avoid conflicts
                sh 'docker rm -f node-jenkins-email-app || true'

                // Run container on a different port (to avoid conflict with local runs)
                sh 'docker run -d -p 3001:3000 --name node-jenkins-email-app node-jenkins-email-app'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up Docker...'
            sh 'docker rm -f node-jenkins-email-app || true'
        }
    }
}
```

## 📸 Screenshots
## Jenkins Dashboard
![Jenkins Dashboard](./Screenshots/)

## Jenkins Pipeline Overview
![Pipeline Overview](./Screenshots)

## Jenkins Build status
![Build Status](./Screenshots)

## Jenkins Docker Container and NodeJS App Container
![Docker Container](./Screenshots)

## Jenkins NodeJS app in Browser
![App in Browser](./Screenshots)


## 👨‍💻 Author  

**Omkar Nerale**  
💼 DevOps & Cloud Enthusiast  

📧 **Email:** [omkarnerale2003@gmail.com](mailto:omkarnerale2003@gmail.com)  
🔗 **LinkedIn:** [https://www.linkedin.com/in/omkar-nerale-85730b317/](https://www.linkedin.com/in/omkar-nerale-85730b317/)  






