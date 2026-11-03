stage('Test EC2 SSH Connection') {
    steps {
        withCredentials([sshUserPrivateKey(
            credentialsId: 'ec2-ssh-key',
            keyFileVariable: 'SSH_KEY',
            usernameVariable: 'SSH_USER'
        )]) {
            bat '''
                C:\\Windows\\System32\\OpenSSH\\ssh.exe -i "%SSH_KEY%" -o BatchMode=yes -o StrictHostKeyChecking=accept-new %SSH_USER%@3.109.124.42 "echo EC2 SSH connection successful"
            '''
        }
    }
}
