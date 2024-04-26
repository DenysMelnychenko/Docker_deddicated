pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'my-nginx-app'
        DOCKER_TAG = 'latest'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("$DOCKER_IMAGE:$DOCKER_TAG")
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Here we can integrate testing commands if necessary'
                // Додаємо сценарії тестування, якщо вони будуть доступні
            }
        }
        stage('Push to Registry') {
            steps {
                script {
                    docker.withRegistry('https://your-registry-url.com', 'registry-credentials') {
                        docker.image("$DOCKER_IMAGE:$DOCKER_TAG").push()
                    }
                }
            }
        }
        stage('Cleanup') {
            steps {
                sh "docker rmi $DOCKER_IMAGE:$DOCKER_TAG"
            }
        }
    }
    post {
        always {
            echo 'This will always run regardless of the stage results'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}