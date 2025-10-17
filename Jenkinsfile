pipeline {
    agent any

    environment {
        IMAGE_NAME = 'haeyoun/cicd'
        REGISTRY_CREDENTIALS = 'dockerhub-creds'
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

        stage('Get ArgoCD Server Port') {
            steps {
                script {
                    // ArgoCD 서버 포트 동적으로 가져오기
                    def port = sh(script: 'kubectl get svc argocd-server -n argocd -o=jsonpath="{.spec.ports[0].nodePort}"', returnStdout: true).trim()
                    // ARGOCD_SERVER 환경 변수에 ArgoCD 서버 IP와 포트 설정
                    env.ARGOCD_SERVER = "172.16.104.40:${port}"
                    echo "ArgoCD Server is available at ${env.ARGOCD_SERVER}"
                }
            }
        }

        stage('ArgoCD Login') {
            steps {
                script {
                    // ArgoCD 로그인
                    sh '''
                    set -e
                    argocd login ${ARGOCD_SERVER} --username ${ARGOCD_USERNAME} --password ${ARGOCD_PASSWORD} --insecure
                    '''
                }
            }
        }

        stage('ArgoCD Deploy') {
            steps {
                script {
                    // 애플리케이션 동기화 후 상태 확인
                    sh '''
                    argocd app sync my-app --server ${ARGOCD_SERVER}
                    argocd app get my-app --server ${ARGOCD_SERVER}
                    '''
                }
            }
        }
    }
}

