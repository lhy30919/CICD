pipeline {
    agent any

    environment {
        IMAGE_NAME = 'haeyoun/cicd'
        REGISTRY_CREDENTIALS = 'dockerhub-creds'
        ARGOCD_SERVER = 'localhost:8081'  // ArgoCD 서버 주소
        ARGOCD_USERNAME = 'admin'
        ARGOCD_PASSWORD = 'XuKaWn75XMJWKz48'  // 초기 비밀번호
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

        stage('ArgoCD Login') {
            steps {
                script {
                    // ArgoCD 로그인
                    sh "argocd login ${ARGOCD_SERVER} --username ${ARGOCD_USERNAME} --password ${ARGOCD_PASSWORD} --insecure"
                }
            }
        }

        stage('ArgoCD Deploy') {
            steps {
                script {
                    // ArgoCD 서버 주소를 명시적으로 추가하여 애플리케이션 동기화
                    sh "argocd app sync my-app --server ${ARGOCD_SERVER}"
                }
            }
        }
    }
}

