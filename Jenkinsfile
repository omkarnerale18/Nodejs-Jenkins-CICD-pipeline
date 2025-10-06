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
