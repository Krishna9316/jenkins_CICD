pipeline {
    agent any
    environment {
        IMAGE_NAME = "krishna9316/jenkins_cicd"
        TAG = "${BUILD_NUMBER}"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Krishna9316/jenkins_CICD.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$TAG .'
                sh 'docker tag $IMAGE_NAME:$TAG $IMAGE_NAME:latest'
            }
        }
        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh 'docker push $IMAGE_NAME:$TAG'
                    sh 'docker push $IMAGE_NAME:latest'
                    sh 'docker logout'
                }
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker stop jenkins_cicd || true'
                sh 'docker rm jenkins_cicd || true'
                sh 'docker pull $IMAGE_NAME:latest'
                sh 'docker run -d --name jenkins_cicd -p 80:80 --restart unless-stopped $IMAGE_NAME:latest'
            }
        }
    }
}