pipeline {
    agent any 
    environment {
        EC2_IP = "13.205.132.196"
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
                    rm -rf backend-project
                    git clone git@github.com:umapathy1729/backend-project.git
                    '
                    '''
                }
            }    
        }
        stage('Git checkout') {
            steps {
                sshagent(['ec2_key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@${EC2_IP} '
                    set -e
                    cd backend-project
                    git checkout main
                    git pull origin main

                    '
                    '''
                }
            }
        }
        stage('Removing old container') {
            steps {
                sshagent(['ec2_key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@${EC2_IP} '
                    set -e
                    docker stop myapp || true
                    docker rm -f myapp
                    docker rmi backend-image ||true 

                    '
                    '''
                }
            }
        }
        stage('Building the container') {
            steps {
                sshagent(['ec2_key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@${EC2_IP} '
                    set -e
                    docker build -t backend-image .

                    '
                    '''
                }
            }
        }
        stage('Running the container') {
            steps {
                sshagent(['ec2_key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@${EC2_IP} '
                    set -e
                    docker run -d -p 5000:5000 --name myapp backend-image

                    '
                    '''
                }
            }
        }
    }
}
