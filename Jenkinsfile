pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "jenkins-simple-test"
        CONTAINER_NAME = "jenkins-simple-container"
        APP_PORT = "8081"
        IMAGE_TAG = "v.${BUILD_NUMBER}.0"
    }

    stages {
        stage('Environment Variables') {
            steps {
                echo "Build number: ${env.BUILD_NUMBER}"
                echo "Docker image: ${env.DOCKER_IMAGE}"
                echo "Image tag: ${env.IMAGE_TAG}"
                echo "Application port: ${env.APP_PORT}"
            }
        }

        stage('Checkout') {
            steps {
                echo 'Repository was downloaded successfully'
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build \
                      -t $DOCKER_IMAGE:$IMAGE_TAG \
                      .
                '''
            }
        }

        stage('Docker Run') {
            steps {
                sh '''
                    docker rm -f $CONTAINER_NAME || true

                    docker run -d \
                      --name $CONTAINER_NAME \
                      -p $APP_PORT:80 \
                      $DOCKER_IMAGE:$IMAGE_TAG
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    sleep 3
                    curl -f http://localhost:$APP_PORT
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully"
        }

        failure {
            echo "Pipeline failed"
        }
    }
}