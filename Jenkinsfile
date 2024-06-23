pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'ap-northeast-2'
        AWS_ACCOUNT_ID = '471112853004'
        ECR_REPOSITORY = 'web-intro'
        DOCKERFILE_PATH = 'Dockerfile'
        DOCKER_IMAGE_NAME = 'web-intro'
        GIT_REPO_URL = 'https://github.com/khj811/jenkins.git'
        GIT_CREDENTIALS_ID = 'github-token'  // Jenkins에서 설정한 HTTPS credentials ID
    }

    stages {
        stage('Build') {
            steps {
                script {
                    // Configure Git user
                    sh "git config --global user.name 'khj811'"
                    sh "git config --global user.email 'hajinkim811@gmail.com'"

                    sh "git pull origin main"
                    sh "sed -i 's/imageTag: .*/imageTag: ${BUILD_NUMBER}/g' values.yaml"
                    sh "git add values.yaml"
                    sh "git commit -m 'Update imageTag to ${BUILD_NUMBER}'"
                    sh "git push origin main"
                    
                }
            }
        }
    }

    post {
        success {
            echo "Build and ECR push successful, image version: ${BUILD_NUMBER}"
        }
        failure {
            echo 'Build or ECR push failed'
        }
    }
}
