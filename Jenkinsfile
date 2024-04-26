pipeline {
    agent any
    environment {
        IMAGE_NAME = 'mydevopsway/my_nginx_app' // Змінено на повне ім'я образу з урахуванням реєстру
        IMAGE_TAG = 'latest'
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
                    // Використовуйте змінні середовища для забезпечення консистентності
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
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
                withDockerRegistry(credentialsId: 'docer_ssh', url: 'https://index.docker.io/v1/') { // Переконайтеся, що credentialsId вказано правильно
                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
        stage('Cleanup') {
            steps {
                sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
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
