pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-2'
        ECR_REPO = '627807502425.dkr.ecr.ap-south-2.amazonaws.com/flask_devops_app'
        EC2_HOST = '40.192.18.137'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Source code is available from GitHub'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t flask-app .'
            }
        }

        stage('Login to Amazon ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin 627807502425.dkr.ecr.ap-south-2.amazonaws.com
                '''
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh 'docker tag flask-app:latest $ECR_REPO:latest'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $ECR_REPO:latest'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                ssh -o StrictHostKeyChecking=no ubuntu@$EC2_HOST << 'EOF'

                aws ecr get-login-password --region ap-south-2 | \
                sudo docker login --username AWS --password-stdin 627807502425.dkr.ecr.ap-south-2.amazonaws.com

                sudo docker pull 627807502425.dkr.ecr.ap-south-2.amazonaws.com/flask_devops_app:latest

                sudo docker stop flask-container || true
                sudo docker rm flask-container || true

                sudo docker run -d \
                  --name flask-container \
                  -p 5000:5000 \
                  627807502425.dkr.ecr.ap-south-2.amazonaws.com/flask_devops_app:latest

                EOF
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully! Application deployed to EC2.'
        }

        failure {
            echo 'Pipeline execution failed. Check the console output.'
        }
    }
}
