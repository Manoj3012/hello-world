pipeline {
    agent any
    
    environment {
        DOCKER_PASSWORD = credentials('docker-cred')
        DOCKER_USERNAME = "manombamm"
        DOCKER_REGISTRY = "docker.io"
        DOCKER_IMAGE_NAME = "manombamm/task"
        DOCKER_IMAGE_TAG = "${env.BUILD_NUMBER}" // Set Docker image tag to Jenkins build number
        GITHUB_REPO_URL = "https://github.com/Manoj3012/devOpsWeb.git"
        GIT_CREDENTIALS_ID = "Git-cred"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:v0.${DOCKER_IMAGE_TAG}", ".")
                }
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        def dockerImageTag = "${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:v0.${BUILD_NUMBER}"
                        sh "docker login -u ${env.DOCKER_USERNAME} -p ${env.DOCKER_PASSWORD} ${DOCKER_REGISTRY}"
                        sh "docker push ${dockerImageTag}"
                    }
                }
            }
        }

    }
}
