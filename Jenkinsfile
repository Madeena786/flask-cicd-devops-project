pipeline {
    agent any

    stages {

        stage('Clone') {
            steps {
                echo 'Source code is available from GitHub'
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t flask-app .'
            }
        }

        stage('Run') {
            steps {
                sh '''
                docker stop flask-container || true
                docker rm flask-container || true
                docker run -d -p 5000:5000 --name flask-container flask-app
                '''
            }
        }
    }
}
