pipeline {
    agent any

    environment {
        // Docker Hub repo adı
        DOCKER_REPO = "okanaltinbas/blog"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/okanaltinbas/jenkins.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Docker imajını oluştur
                    def dockerImage = docker.build("${DOCKER_REPO}:${env.BUILD_ID}")
                    env.IMAGE_NAME = "${DOCKER_REPO}:${env.BUILD_ID}"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', 'docker-credentials-id') {
                        sh "docker push ${env.IMAGE_NAME}"
                    }
                }
            }
        }

        stage('Deploy to Jenkins Server') {
            steps {
                script {
                    // Docker container'ı çalıştır
                    sh """
                    docker stop my-sample-app || true && docker rm my-sample-app || true
                    docker run -d --name my-sample-app -p 80:80 ${env.IMAGE_NAME}
                    """
                }
            }
        }
    }
}
