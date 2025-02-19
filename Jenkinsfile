pipeline {
    agent any
    environment {
        IMAGE_REPO_NAME = "Ekart"
        IMAGE_TAG = "v1"
        DOCKER_IMAGE = "yaqoobali/artisanclass:Ekart"
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Yaqoob599/Ekart.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${env.IMAGE_REPO_NAME}:${env.IMAGE_TAG} -f docker/Dockerfile ."
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhub_password', variable: 'DOCKERHUB_PASSWORD')]) {
                        sh "echo ${env.DOCKERHUB_PASSWORD} | docker login -u yaqoobali --password-stdin"
                    }
                    sh "docker tag ${env.IMAGE_REPO_NAME}:${env.IMAGE_TAG} ${env.DOCKER_IMAGE}"
                    sh "docker push ${env.DOCKER_IMAGE}"
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                script {
                    sh "docker pull ${env.DOCKER_IMAGE}"

                    // Stop & remove existing container if it exists
                    sh "docker stop Ekart_container || true"
                    sh "docker rm Ekart_container || true"

                    sh "docker run -d --name Ekart_container -p 8070:8070 ${env.DOCKER_IMAGE}"
                }
            }
        }
    }
}
