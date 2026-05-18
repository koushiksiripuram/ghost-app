pipeline {

    agent any

    environment {
        IMAGE_NAME = "koushiksiripuram/ghost-app"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Build Docker Image') {
            steps {

                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'

            }
        }

        stage('Docker Hub Login') {
            steps {

                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {

                sh 'docker push $IMAGE_NAME:$IMAGE_TAG'

            }
        }

        stage('Redeploy Containers') {
            steps {

                sh 'docker compose pull'

                sh 'docker compose up -d --build'

            }
        }

        stage('Cleanup') {
            steps {

                sh 'docker image prune -f'

            }
        }
    }

    post {

        success {

            echo 'Deployment completed successfully!'

        }

        failure {

            echo 'Pipeline failed!'

        }
    }
}