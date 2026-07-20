pipeline {
    agent any

    environment {
        DOCKER_ID = "shabbyalaei"
        DOCKER_IMAGE = "jenkins-simple-test"
        CONTAINER_NAME = "jenkins-simple-container"
        APP_PORT = "8081"
        IMAGE_TAG = "v.${BUILD_NUMBER}.0"
    }

    stages {
        stage('Environment Variables') {
            steps {
                echo "Build number: ${env.BUILD_NUMBER}"
                echo "Docker repository: ${env.DOCKER_ID}/${env.DOCKER_IMAGE}"
                echo "Image tag: ${env.IMAGE_TAG}"
                echo "Application port: ${env.APP_PORT}"
            }
        }

        stage('Checkout') {
            steps {
                echo 'Repository was downloaded successfully'
            }
        }

        stage('Credential Test') {
            environment {
                MY_SECRET = credentials('DEMO_SECRET')
            }

            steps {
                sh '''
                    if [ -n "$MY_SECRET" ]; then
                        echo "Credential was loaded successfully"
                    else
                        echo "Credential is empty"
                        exit 1
                    fi
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build \
                      -t $DOCKER_ID/$DOCKER_IMAGE:$IMAGE_TAG \
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
                      $DOCKER_ID/$DOCKER_IMAGE:$IMAGE_TAG
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

        stage('Docker Push') {
            environment {
                DOCKER_CREDS = credentials('DOCKER_HUB_CREDENTIALS')
            }

            steps {
                sh '''
                    echo "$DOCKER_CREDS_PSW" | \
                      docker login \
                      -u "$DOCKER_CREDS_USR" \
                      --password-stdin

                    docker push \
                      $DOCKER_ID/$DOCKER_IMAGE:$IMAGE_TAG

                    docker logout
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