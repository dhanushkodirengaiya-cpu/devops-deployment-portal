pipeline {
    agent any

    environment {
        DOCKER = 'C:\\Users\\dkodi\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install and Test') {
            steps {
                bat '"%DOCKER%" run --rm -v "%WORKSPACE%:/app:ro" -w /app node:22-alpine npm test'
            }
        }

        stage('Docker Build') {
            steps {
                bat '"%DOCKER%" build -t dhanushkodirengaiya/devops-deployment-portal:latest .'
            }
        }

        stage('Docker Hub Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-token',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat '''
                        "%DOCKER%" login -u "%DOCKER_USER%" -p "%DOCKER_PASS%"
                        "%DOCKER%" push dhanushkodirengaiya/devops-deployment-portal:latest
                        "%DOCKER%" logout
                    '''
                }
            }
        }

        stage('Test EC2 SSH Connection') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'ec2-ssh-key',
                    keyFileVariable: 'SSH_KEY',
                    usernameVariable: 'SSH_USER'
                )]) {
                    bat '''
                        icacls "%SSH_KEY%" /inheritance:r
                        icacls "%SSH_KEY%" /grant:r "*S-1-5-18:(R)"
                        C:\\Windows\\System32\\OpenSSH\\ssh.exe -i "%SSH_KEY%" -o BatchMode=yes -o StrictHostKeyChecking=accept-new %SSH_USER%@3.109.124.42 "echo EC2 SSH connection successful"
                    '''
                }
            }
        }

        stage('Deploy to AWS EC2') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'ec2-ssh-key',
                    keyFileVariable: 'SSH_KEY',
                    usernameVariable: 'SSH_USER'
                )]) {
                    bat '''
                        icacls "%SSH_KEY%" /inheritance:r
                        icacls "%SSH_KEY%" /grant:r "*S-1-5-18:(R)"
                        C:\\Windows\\System32\\OpenSSH\\ssh.exe -i "%SSH_KEY%" -o BatchMode=yes -o StrictHostKeyChecking=accept-new %SSH_USER%@3.109.124.42 "sudo docker pull dhanushkodirengaiya/devops-deployment-portal:latest && (sudo docker rm -f devops-portal || true) && sudo docker run -d --name devops-portal --restart unless-stopped -p 3000:3000 dhanushkodirengaiya/devops-deployment-portal:latest"
                    '''
                }
            }
        }
    }
}
