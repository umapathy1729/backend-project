pipeline {
    agent any
    environment {
        EC2_IP = "13.127.17.124"
    }
    stages {
        stage('Git checkout code') {
            steps {
                git branch: 'main', 
                credentialsId: 'Github-credentials', 
                url: 'https://github.com/umapathy1729/backend-project.git'
            }
        }
        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2_key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@${EC2_IP} '
                        set -e
                        mkdir -p /home/ubuntu/apps
                        cd /home/ubuntu/apps

                        rm -rf backend-project

                        git clone git@github.com:umapathy1729/backend-project.git

                        cd backend-project
                        git checkout main
                        git pull origin main

                        docker stop myap || true
                        docker rm myapp || true
                        docker rmi backend-image || true

                        docker build -t backend-image .
                        docker run -d -p 5000:5000 --name myapp backend-image
                    '
                    '''
                }
            }
        }
    }

    post {
        success {
            echo " Deplyment successful"
        }
        failure {
            echo "Deployment failed"
        }
    }
}

