pipeline {
    agent any

    environment {
        IMAGE_NAME = 'haeyoun/cicd'
        REGISTRY_CREDENTIALS = 'dockerhub-creds'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:latest")
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', REGISTRY_CREDENTIALS) {
                        docker.image("${IMAGE_NAME}:latest").push()
                    }
                }
            }
        }

        stage('ArgoCD Deploy') {
            steps {
                sh "argocd app sync my-app"
            }
        }
    }
}

