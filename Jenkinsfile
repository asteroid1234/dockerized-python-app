pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "taras.shpetko@gmail.com/dockerized-python-app"  // Define the Docker image name
    }
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/asteroid1234/dockerized-python-app.git'  // Clone your GitHub repo
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")  // Build and tag the Docker image
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        dockerImage.push("${BUILD_NUMBER}")  // Push the image to DockerHub with the build number as the tag
                    }
                }
            }
        }
        stage('Run Docker Compose') {
            steps {
                script {
                    sh 'echo IMAGE_TAG=${BUILD_NUMBER} > .env'  // Set the image tag in an .env file for Docker Compose
                    sh 'docker-compose up -d'  // Start containers using Docker Compose
                }
            }
        }
        stage('Run Tests') {
            steps {
                script {
                    sh 'python3 backend_testing.py'  // Run backend tests
                    sh 'python3 docker_backend_testing.py'  // Run additional Docker-specific tests
                }
            }
        }
        stage('Clean Up') {
            steps {
                script {
                    sh 'docker-compose down'  // Stop containers and remove networks
                    sh 'docker rmi ${DOCKER_IMAGE}:${BUILD_NUMBER}'  // Remove the Docker image to free up space
                }
            }
        }
    }
    post {
        failure {
            mail to: 'taras.shpetko@gmail.com',
                 subject: "Build Failed",
                 body: "The Jenkins pipeline has failed."  // Send an email if the pipeline fails
        }
    }
}
