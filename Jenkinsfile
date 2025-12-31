pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/yourname/devops-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t mennanvijay/devops-app:latest .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'PASS')]) {
                    sh '''
                    echo $PASS | docker login -u mennanvijay --password-stdin
                    docker push mennanvijay/devops-app:latest
                    '''
                }
            }
        }

        stage('Deploy using Ansible') {
            steps {
                sh 'ansible-playbook ansible/deploy.yml -i ansible/inventory'
            }
        }
    }
}

