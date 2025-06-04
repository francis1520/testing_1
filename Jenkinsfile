pipeline {
    agent any

    environment {
        IMAGE_NAME = "francisreddy/candy-store"
        IMAGE_TAG = "01"
        DOCKER_USERNAME = 'francisreddy'
        DOCKER_PASSWORD = 'Nani@1997'
        DOCKER_HUB_USER = "francisreddy"
        REGISTRY = "${DOCKER_HUB_USER}/${IMAGE_NAME}"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Login to Docker') {
            steps {
                script {
                    sh """
                    echo '${DOCKER_PASSWORD}' | docker login -u '${DOCKER_USERNAME}' --password-stdin
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${REGISTRY}:${IMAGE_TAG}'
                sh 'docker push ${REGISTRY}:${IMAGE_TAG}'
            }
        }

        stage('Deploy on EC2') {
            steps {
                sh """
                    docker pull ${REGISTRY}:${IMAGE_TAG}
                    docker rmi ${REGISTRY}:${IMAGE_TAG} || true
                    docker run -d -p 3000:3000 --name frontend-container --restart=always ${REGISTRY}:${IMAGE_TAG}
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
