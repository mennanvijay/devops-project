pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                // Specified branch 'main' and used your existing credential ID
                git branch: 'main', 
                    credentialsId: 'github-cred', 
                    url: 'https://github.com/mennanvijay/devops-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Ensure there is a 'Dockerfile' in the root of your repo
                sh 'docker build -t mennanvijay/devops-app:latest .'
            }
        }

        stage('Push Image') {
            steps {
                // Using DockerHub credentials
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
                // Ensure your inventory file and playbook exist at these paths
                sh 'ansible-playbook ansible/deploy.yml -i ansible/inventory'
            }
        }
    }
    
    post {
        always {
            // Good practice: Log out of Docker to clean up credentials on the runner
            sh 'docker logout'
        }
    }
}

