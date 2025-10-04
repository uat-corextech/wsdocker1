pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub'  // Jenkins credential ID
        DOCKER_IMAGE = 'username/myapp'      // Change to your Docker Hub repo
        CONTAINER_NAME = 'myapp-container'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:latest ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    }
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    // Stop and remove existing container if running
                    sh "docker rm -f ${CONTAINER_NAME} || true"

                    // Run new container
                    sh "docker run -d --name ${CONTAINER_NAME} -p 8080:80 ${DOCKER_IMAGE}:latest"
                }
            }
        }
    }

    post {
        always {
            sh "docker logout"
        }
    }
}

