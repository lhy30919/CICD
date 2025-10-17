pipeline {
    agent any

    environment {
        IMAGE_NAME = 'haeyoun/cicd'
        REGISTRY_CREDENTIALS = 'dockerhub-creds'
        ARGOCD_SERVER = '172.16.104.40:31664'  // 외부 ArgoCD 서버 IP와 포트
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
                    sh """
                    set -e
                    argocd login ${ARGOCD_SERVER} --username ${ARGOCD_USERNAME} --password ${ARGOCD_PASSWORD} --insecure
                    """
                }
            }
        }

        stage('ArgoCD Deploy') {
            steps {
                script {
                    // 애플리케이션 동기화 후 상태 확인
                    sh """
                    argocd app sync my-app --server ${ARGOCD_SERVER}
                    argocd app get my-app --server ${ARGOCD_SERVER}
                    """
                }
            }
        }
    }
}

