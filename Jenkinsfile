pipeline {
    agent any

    environment {
        IMAGE_NAME = 'dhanushkodirengaiya/devops-deployment-portal'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Source code checked out from GitHub'
                checkout scm
            }
        }

        stage('Install and Test') {
            steps {
                bat 'docker run --rm -v "%WORKSPACE%:/app" -w /app node:22-alpine sh -c "npm install && npm test"'
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:latest .'
            }
        }

        stage('Docker Hub Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-token',
                    usernameVariable: 'DOCKERHUB_USER',
                    passwordVariable: 'DOCKERHUB_TOKEN'
                )]) {
                    bat 'echo %DOCKERHUB_TOKEN%| docker login -u %DOCKERHUB_USER% --password-stdin'
                    bat 'docker push %IMAGE_NAME%:latest'
                    bat 'docker logout'
                }
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Build, Test and Docker Hub Push completed!'
        }
        failure {
            echo 'FAILED: Check the Console Output.'
        }
    }
}
