pipeline {
    agent any

    environment {
        DOCKER_USER = 'sybew'
        IMAGE_NAME = 'mmtnc-web'
        CONTAINER_NAME = 'mmtnc-web-server'
        TAG = "v${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'jenkins-dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    
                    sh "docker build -t ${DOCKER_USER}/${IMAGE_NAME}:${TAG} ."
                    
                    sh "docker tag ${DOCKER_USER}/${IMAGE_NAME}:${TAG} ${DOCKER_USER}/${IMAGE_NAME}:latest"
                    
                    sh "docker push ${DOCKER_USER}/${IMAGE_NAME}:${TAG}"
                    sh "docker push ${DOCKER_USER}/${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Deploy') {
            steps {
                sh "docker stop ${CONTAINER_NAME} || true"
                sh "docker rm ${CONTAINER_NAME} || true"
                
                sh "docker run -d -p 8000:3000 --name ${CONTAINER_NAME} ${DOCKER_USER}/${IMAGE_NAME}:${TAG}"
            }
        }
    }
    
    post {
        always {
            sh "docker logout"
            cleanWs()
        }
    }
}
