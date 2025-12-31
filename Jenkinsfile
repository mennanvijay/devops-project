pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', 
                    credentialsId: 'github-cred', 
                    url: 'https://github.com/mennanvijay/devops-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t mennanvijay/devops-app:latest .'
            }
        }

        stage('Push Image') {
            steps {
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
                // This ID MUST match the one in your Screenshot (35)
                sshagent(['azure-vm-key']) { 
                    sh 'ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook ansible/deploy.yml -i ansible/inventory'
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}
