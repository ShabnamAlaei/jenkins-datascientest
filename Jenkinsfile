pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Repository was downloaded successfully'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t jenkins-simple-test .'
            }
        }

        stage('Docker Run') {
            steps {
                sh '''
                    docker rm -f jenkins-simple-container || true
                    docker run -d \
                      --name jenkins-simple-container \
                      -p 8081:80 \
                      jenkins-simple-test
                '''
            }
        }

        stage('Test') {
            steps {
                sh 'sleep 3'
                sh 'curl -f http://localhost:8081'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }

        failure {
            echo 'Pipeline failed'
        }
    }
}
