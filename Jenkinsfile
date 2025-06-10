pipeline {
    agent any

    environment {
        IMAGE_NAME = "candy-store"
        IMAGE_TAG = "01"
        DOCKER_USERNAME = 'francisreddy'
        DOCKER_PASSWORD = 'Nani@1997'
        DOCKER_HUB_USER = "francisreddy"
        FULL_IMAGE_NAME = "${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${FULL_IMAGE_NAME} .'
            }
        }

        stage('Login to Docker') {
            steps {
                script {
                    sh '''
                    echo "${DOCKER_PASSWORD}" | docker login -u "${DOCKER_USERNAME}" --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push ${FULL_IMAGE_NAME}'
            }
        }

        stage('Deploy on EC2') {
            steps {
                sh '''
                    docker pull ${FULL_IMAGE_NAME}
                    docker rmi ${FULL_IMAGE_NAME} || true
                    docker run -d -p 3000:3000 --name frontend-container --restart=always ${FULL_IMAGE_NAME}
                '''
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
