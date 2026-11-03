
stage('Deploy to AWS EC2') {
    steps {
        withCredentials([sshUserPrivateKey(
            credentialsId: 'ec2-ssh-key',
            keyFileVariable: 'SSH_KEY',
            usernameVariable: 'SSH_USER'
        )]) {
            bat '''
                C:\\Windows\\System32\\OpenSSH\\ssh.exe -i "%SSH_KEY%" -o BatchMode=yes -o StrictHostKeyChecking=accept-new %SSH_USER%@3.109.124.42 "sudo docker pull dhanushkodirengaiya/devops-deployment-portal:latest && (sudo docker rm -f devops-portal || true) && sudo docker run -d --name devops-portal --restart unless-stopped -p 3000:3000 dhanushkodirengaiya/devops-deployment-portal:latest"
            '''
        }
    }
}


