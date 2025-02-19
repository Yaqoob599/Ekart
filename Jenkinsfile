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
        
        stage('compile') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
        

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${IMAGE_REPO_NAME}:${IMAGE_TAG} -f docker/Dockerfile .
                """
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                        withCredentials([string(credentialsId: 'dockerhub_password', variable: 'DOCKERHUB_PASSWORD')]) {
                        sh 'docker login -u yaqoobali -p ${DOCKERHUB_PASSWORD}'

                        sh 'docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${DOCKER_IMAGE}'
                        sh 'docker push ${DOCKER_IMAGE}'
                    
                }
            }
        }

        stage('Deploy Docker Container') {
    steps {
        script {
            sh 'docker pull ${DOCKER_IMAGE}'
            sh 'docker run -d --name Ekart_container -p 8070:8070 ${DOCKER_IMAGE}'
        }
    }
}

    }
}
