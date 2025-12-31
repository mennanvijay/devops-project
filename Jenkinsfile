pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                // Pulls code from your repository using the 'main' branch
                git branch: 'main', 
                    credentialsId: 'github-cred', 
                    url: 'https://github.com/mennanvijay/devops-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Builds the Docker image from the Dockerfile in your root directory
                sh 'docker build -t mennanvijay/devops-app:latest .'
            }
        }

        stage('Push Image') {
            steps {
                // Securely logs into Docker Hub using your saved credentials
                withCredentials([usernamePassword(credentialsId: 'dockerhub-pass', 
                                                  passwordVariable: 'PASS', 
                                                  usernameVariable: 'USER')]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push mennanvijay/devops-app:latest
                    '''
                }
            }
        }

        stage('Deploy using Ansible') {
            steps {
                // Uses the SSH Agent plugin to provide your private key to Ansible
                // Make sure 'azure-vm-ssh-key' matches your Credential ID exactly
                sshagent(['azure-vm-ssh-key']) { 
                    sh 'ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook ansible/deploy.yml -i ansible/inventory'
                }
            }
        }
    }

    post {
        always {
            // Logs out of Docker to clear credentials from the Jenkins runner
            sh 'docker logout || true'
        }
    }
}
