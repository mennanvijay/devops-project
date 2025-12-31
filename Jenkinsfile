pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                // Using 'main' branch and your 'github-cred'
                git branch: 'main', 
                    credentialsId: 'github-cred', 
                    url: 'https://github.com/mennanvijay/devops-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Builds the image using the Dockerfile in your root directory
                sh 'docker build -t mennanvijay/devops-app:latest .'
            }
        }

        stage('Push Image') {
            steps {
                // Correctly handles 'Username with password' credential type
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
                // Runs the playbook located in your ansible folder
                sshagent(['azure-vm-key']) {
                sh 'ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook ansible/deploy.yml -i ansible/inventory'
            }
        }
    }

    post {
        always {
            // Cleans up login credentials on the Jenkins runner
            sh 'docker logout || true'
        }
    }
}
