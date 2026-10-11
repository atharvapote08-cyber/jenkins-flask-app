pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t jenkins-flask-app:latest .'
            }
        }

        stage('Test Application') {
            steps {
                sh '''
                    docker rm -f flask-test 2>/dev/null || true
                    docker run -d --name flask-test -p 5001:5000 jenkins-flask-app:latest
                    sleep 5
                    curl --fail http://localhost:5001/health
                    docker rm -f flask-test
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f flask-app 2>/dev/null || true
                    docker run -d --restart unless-stopped --name flask-app -p 5000:5000 jenkins-flask-app:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }
        failure {
            echo 'CI/CD Pipeline failed. Check the console output.'
        }
    }
}
