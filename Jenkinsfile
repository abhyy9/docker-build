pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-cred')
        IMAGE_NAME = "abhyy9/docker-build"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                """
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh """
                    trivy image \
                    --scanners vuln \
                    --severity HIGH,CRITICAL \
                    ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('Login to Docker Hub') {
            steps {
                sh """
                    echo "${DOCKERHUB_CREDENTIALS_PSW}" | docker login \
                    -u "${DOCKERHUB_CREDENTIALS_USR}" \
                    --password-stdin
                """
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                sh """
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
