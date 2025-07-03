pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "my-flask-app"
        DOCKER_TAG = "latest"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-repo/flask-app.git'
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .'
            }
        }
        stage('Test') {
            steps {
                sh 'docker run ${DOCKER_IMAGE}:${DOCKER_TAG} python -m pytest'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker run -d -p 5000:5000 --name flask-app ${DOCKER_IMAGE}:${DOCKER_TAG}'
            }
        }
    }
    post {
        always {
            echo 'Pipeline completed - cleaning up'
            sh 'docker stop flask-app || true'
            sh 'docker rm flask-app || true'
        }
        failure {
            slackSend channel: '#devops', message: "❌ Pipeline FAILED: ${env.JOB_NAME} (#${env.BUILD_NUMBER})"
        }
        success {
            slackSend channel: '#devops', message: "✅ Pipeline SUCCESS: ${env.JOB_NAME} (#${env.BUILD_NUMBER})"
        }
    }
}
