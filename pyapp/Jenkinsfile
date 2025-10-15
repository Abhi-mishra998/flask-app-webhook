pipeline {
    agent any

    environment {
        LOCAL_IMAGE = "pyapp"
        IMAGE_NAME = "abhishek8056/react-app-latest"
        IMAGE_TAG = "latest"
        DOCKER_CREDENTIALS = 'dockerhub'
    }

    stages {
        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh "docker build -t ${LOCAL_IMAGE}:latest ."
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker tag ${LOCAL_IMAGE}:latest ${IMAGE_NAME}:${IMAGE_TAG}
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                        docker logout
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Docker image pushed successfully! Cleaning up local images..."
            sh """
                docker rmi ${IMAGE_NAME}:${IMAGE_TAG} || true
                docker rmi ${LOCAL_IMAGE}:latest || true
                docker system prune -f
            """
        }
        failure {
            echo "Failed to push Docker image. Check logs."
        }
    }
}
