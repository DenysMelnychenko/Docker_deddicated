pipeline {
    agent any
    environment {
        IMAGE_NAME = 'mydevopsway/my_nginx_app' // Повне ім'я Docker образу
        IMAGE_TAG = 'latest'
        EC2_HOST = 'ec2-user@3.9.144.180' // Замініть на вашу реальну публічну IP адресу EC2 інстансу
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
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Here we can integrate testing commands if necessary'
            }
        }
        stage('Push to Registry') {
            steps {
                withDockerRegistry(credentialsId: 'docer_ssh', url: 'https://index.docker.io/v1/') {
                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
        stage('Deploy to EC2') {
            steps {
                sshagent(['AWS_CD']) { // Упевніться, що 'AWS_CD' це правильний ID для SSH креденціалів
                    script {
                        // Видаляємо старий контейнер перед створенням нового
                        sh "ssh -o StrictHostKeyChecking=no ${EC2_HOST} 'docker rm -f nginx_container || true'"
                        // Тепер створюємо новий контейнер
                        sh "ssh -o StrictHostKeyChecking=no ${EC2_HOST} 'docker run -d -p 80:80 --name nginx_container ${IMAGE_NAME}:${IMAGE_TAG}'"
                    }
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
