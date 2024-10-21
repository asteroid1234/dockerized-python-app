pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "your-dockerhub-username/dockerized-python-app"
    }
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/yourusername/dockerized-python-app.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        dockerImage.push("${BUILD_NUMBER}")
                    }
                }
            }
        }
        stage('Run Docker Compose') {
            steps {
                script {
                    sh 'echo IMAGE_TAG=${BUILD_NUMBER} > .env'
                    sh 'docker-compose up -d'
                }
            }
        }
        stage('Run Tests') {
            steps {
                script {
                    sh 'python3 backend_testing.py'
                    sh 'python3 docker_backend_testing.py'
                }
            }
        }
        stage('Clean Up') {
            steps {
                script {
                    sh 'docker-compose down'
                    sh 'docker rmi ${DOCKER_IMAGE}:${BUILD_NUMBER}'
                }
            }
        }
    }
    post {
        failure {
            mail to: 'youremail@example.com',
                 subject: "Build Failed",
                 body: "The Jenkins pipeline has failed."
        }
    }
}
