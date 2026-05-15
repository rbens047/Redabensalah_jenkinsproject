pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKERHUB_USERNAME    = 'rbens047'
        IMAGE_NAME            = "${DOCKERHUB_USERNAME}/redabensalah-app"
        IMAGE_TAG             = "${BUILD_NUMBER}"
    }

    stages {

        stage('Reda Bensalah - Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh """
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                    docker tag  ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest
                """
                echo "Image built: ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }

        stage('Reda Bensalah - Login to Dockerhub') {
            steps {
                echo 'Logging in to Docker Hub...'
                sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | \
                    docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                '''
                echo 'Successfully logged in to Docker Hub'
            }
        }

        stage('Reda Bensalah - Push image to Dockerhub') {
            steps {
                echo 'Pushing image to Docker Hub...'
                sh """
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    docker push ${IMAGE_NAME}:latest
                """
                echo "Image pushed: ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
    }

    post {
        always {
            echo 'Cleaning up local Docker images...'
            sh """
                docker rmi ${IMAGE_NAME}:${IMAGE_TAG} || true
                docker rmi ${IMAGE_NAME}:latest       || true
                docker logout                          || true
            """
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs above.'
        }
    }
}
