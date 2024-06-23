pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'ap-northeast-2'
        AWS_ACCOUNT_ID = '471112853004'
        ECR_REPOSITORY = 'web-intro'
        DOCKERFILE_PATH = 'Dockerfile'
        DOCKER_IMAGE_NAME = 'web-intro'
        GIT_REPO_URL = 'https://github.com/khj811/jenkins.git'
        IMAGE_TAG = "${BUILD_NUMBER}"
        GIT_CREDENTIALS_ID = 'github-token'  // Jenkins에서 설정한 HTTPS credentials ID
    }

    stages {
        stage('Build') {
            steps {
                script {
                    // Git 사용자 설정
                    sh "git config --global user.name 'khj811'"
                    sh "git config --global user.email 'hajinkim811@gmail.com'"

                    // GitHub 저장소 클론 (이미 클론된 경우 생략 가능)
                    dir("${WORKSPACE}/jenkins") {
                        sh "git pull origin main --rebase"  // 최신 코드를 가져오기 위해 pull 수행
                    }

                    // values.yaml 파일 수정
                    sh "sed -i 's/imageTag: .*/imageTag: ${IMAGE_TAG}/g' ${WORKSPACE}/jenkins/web-helm/values.yaml"

                    // Git 작업
                    dir("${WORKSPACE}/jenkins") {
                        sh "git add web-helm/values.yaml"
                        sh "git commit -m 'Update imageTag to ${IMAGE_TAG}'"
                        sh "git push origin main"  // main 브랜치로 푸시
                    }
                }
            }
        }
    }

    post {
        success {
            echo "빌드 및 ECR 푸시 성공, 이미지 버전: ${IMAGE_TAG}"
        }
        failure {
            echo '빌드 또는 ECR 푸시 실패'
        }
    }
}
