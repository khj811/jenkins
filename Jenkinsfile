pipeline {
    agent any

    environment {
        NHN_DEFAULT_REGION = 'kr1'  // NCR Region
        NHN_REGISTRY_URL = '847f310f-kr1-registry.container.nhncloud.com/web-intro'  // NCR Registry URL
        NCR_IMAGE_NAME = '${BUILD_NUMBER}'
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKERFILE_PATH = 'Dockerfile'
        DOCKER_IMAGE_NAME = 'web-intro'
        NHN_ACCESS_KEY = '7rqKD7FlGMJGVRF1CXUa' // NHN Cloud Access Key
        NHN_SECRET_KEY = 'mrWm3aENfoxvEDuT' // NHN Cloud Secret Key
    }

    stages {
        stage('Build') {
            steps {
                script {
                    // Docker 이미지 빌드 with --no-cache=true
                    def customImage = docker.build("${DOCKER_IMAGE_NAME}:${IMAGE_TAG}", "--no-cache=true -f ${DOCKERFILE_PATH} .")

                    // NHN Cloud NCR에 로그인 및 이미지 푸시
                    sh "docker login ${NHN_REGISTRY_URL} -u ${NHN_ACCESS_KEY} -p ${NHN_SECRET_KEY}"
                    sh "docker tag ${DOCKER_IMAGE_NAME}:${IMAGE_TAG} ${NHN_REGISTRY_URL}/${NCR_IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker push ${NHN_REGISTRY_URL}/${NCR_IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }

    post {
        success {
            echo "빌드 및 NCR 푸시 성공, 이미지 버전: ${IMAGE_TAG}"
        }
        failure {
            echo '빌드 또는 NCR 푸시 실패'
        }
    }
}
