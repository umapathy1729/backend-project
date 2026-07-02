pipeline {
    agent any
    environment {
        EC2_IP = "35.154.21.230"
    }
    stages {
        stage('Git clone') {
            steps {
                git branch: 'main',
		credentialsId: 'Github-credentials',
		url: 'https://github.com/umapathy1729/backend-project.git'
            }
        }
        stage('Deploy to ec2') {
            steps {
	        sshagent(['ec2_key_new']) {
		    sh '''
		    ssh -o StrictHostKeyChecking=no ubuntu@${EC2_IP} '
		    set -e
		    rm -rf backend-project
		    git clone git@github.com:umapathy1729/backend-project.git
		    
		    cd backend-project
		    git checkout main
		    git pull origin main
		    
		    docker stop backend_container || true
		    docker rm -rf backend_container || true
		    docker rmi backend_image || true

		    docker build -t backend_image .
		    docker run -d -p 5000:5000 --name backend_container backend_image
		    '
		    '''
		}
            }
	}
    }
}
