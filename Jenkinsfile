pipeline {
    agent any

    environment {
        IMAGE_NAME = 'flask-app'
        IMAGE_TAG  = "latest"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'master', url: 'https://github.com/khadijasayed/two-tier-flask-app/'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build locally first without hardcoding the Docker Hub username
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                        set -e
                        echo "${DOCKER_PASS}" | docker login -u "${DOCKER_USER}" --password-stdin
                        docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                        docker push ${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                        docker logout
                        docker rmi ${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }
        stage('Code Deployed') {
            steps {
                // Build locally first without hardcoding the Docker Hub username
                sh "docker compose up -d --build"
            }
        }
    }
}
