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
        BRANCH_NAME = 'main'  // 새로운 브랜치 이름
        GIT_CREDENTIALS_ID = 'github-token'  // Jenkins에서 설정한 HTTPS credentials ID
    }

    stages {
        stage('Build') {
            steps {
                script {
                    // Docker 이미지 빌드 with --no-cache=true
                    def customImage = docker.build("${DOCKER_IMAGE_NAME}:${IMAGE_TAG}", "--no-cache=true -f ${DOCKERFILE_PATH} .")

                    // AWS Credentials로 Docker에 로그인
                    withCredentials([[
                        $class: 'AmazonWebServicesCredentialsBinding',
                        credentialsId: 'd2e00785-2f10-4d6c-b139-beae14ae8072', // AWS Credentials Plugin에서 설정한 credentialsId 입력
                        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                    ]]) {
                        // AWS ECR에 로그인 및 이미지 푸시
                        sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
                        sh "docker tag ${DOCKER_IMAGE_NAME}:${IMAGE_TAG} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}"
                        sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}"
                    }

                    // Git 명령어를 사용하여 values.yaml 파일 내의 이미지 버전 변경
                    checkout([$class: 'GitSCM',
                        branches: [[name: "${NEW_BRANCH_NAME}"]],
                        userRemoteConfigs: [[url: "${GIT_REPO_URL}", credentialsId: "${GIT_CREDENTIALS_ID}"]]
                    ])
                    sh "git pull origin ${BRANCH_NAME}"
                    sh "sed -i 's/imageTag: .*/imageTag: ${IMAGE_TAG}/g' web-helm/values.yaml"
                    sh "git add web-helm/values.yaml"
                    sh "git commit -m 'Update imageTag to ${IMAGE_TAG}'"
                    sh "git push origin ${BRANCH_NAME}"  // 새로운 브랜치로 푸시
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
