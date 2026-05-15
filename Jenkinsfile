pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKERHUB_USERNAME    = 'rbens047'
        IMAGE_NAME            = "${DOCKERHUB_USERNAME}/redabensalah-app"
        IMAGE_TAG             = "${BUILD_NUMBER}"

        // 🔥 FIX: isolate docker config to avoid mac credential helper issue
        DOCKER_CONFIG         = "/tmp/docker-config"
        DOCKER                = "/usr/local/bin/docker"
    }

    stages {

        stage('Reda Bensalah - Build Docker Image') {
            steps {
                echo 'Preparing safe Docker environment...'
                sh '''
                    mkdir -p /tmp/docker-config
                    echo "{}" > /tmp/docker-config/config.json
                '''

                echo 'Building Docker image...'
                sh '''
                    DOCKER_CONFIG=/tmp/docker-config \
                    /usr/local/bin/docker build -t $IMAGE_NAME:$IMAGE_TAG .

                    DOCKER_CONFIG=/tmp/docker-config \
                    /usr/local/bin/docker tag $IMAGE_NAME:$IMAGE_TAG $IMAGE_NAME:latest
                '''
            }
        }

        stage('Reda Bensalah - Login to Dockerhub') {
            steps {
                echo 'Logging in to Docker Hub...'
                sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | \
                    DOCKER_CONFIG=/tmp/docker-config \
                    /usr/local/bin/docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                '''
            }
        }

        stage('Reda Bensalah - Push image to Dockerhub') {
            steps {
                echo 'Pushing image to Docker Hub...'
                sh '''
                    DOCKER_CONFIG=/tmp/docker-config \
                    /usr/local/bin/docker push $IMAGE_NAME:$IMAGE_TAG

                    DOCKER_CONFIG=/tmp/docker-config \
                    /usr/local/bin/docker push $IMAGE_NAME:latest
                '''
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            sh '''
                DOCKER_CONFIG=/tmp/docker-config \
                /usr/local/bin/docker rmi $IMAGE_NAME:$IMAGE_TAG || true

                DOCKER_CONFIG=/tmp/docker-config \
                /usr/local/bin/docker rmi $IMAGE_NAME:latest || true

                DOCKER_CONFIG=/tmp/docker-config \
                /usr/local/bin/docker logout || true
            '''
        }

        success {
            echo 'Pipeline SUCCESS 🎉'
        }

        failure {
            echo 'Pipeline FAILED ❌ Check logs'
        }
    }
}
