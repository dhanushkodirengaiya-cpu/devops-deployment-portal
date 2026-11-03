pipeline {
    agent any

    environment {
        IMAGE_NAME = 'dhanushkodirengaiya/devops-deployment-portal'
        DOCKER = 'C:\\Users\\dkodi\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
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
                bat '"%DOCKER%" run --rm -v "%WORKSPACE%:/app" -w /app node:22-alpine sh -c "npm install && npm test"'
            }
        }

        stage('Docker Build') {
            steps {
                bat '"%DOCKER%" build -t %IMAGE_NAME%:latest .'
            }
        }

        stage('Docker Hub Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-token',
                    usernameVariable: 'DOCKERHUB_USER',
                    passwordVariable: 'DOCKERHUB_TOKEN'
                )]) {
                    bat 'echo %DOCKERHUB_TOKEN%| "%DOCKER%" login -u %DOCKERHUB_USER% --password-stdin'
                    bat '"%DOCKER%" push %IMAGE_NAME%:latest'
                    bat '"%DOCKER%" logout'
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
