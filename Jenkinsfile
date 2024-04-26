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
                sshagent(['AWS_CD']) { // Замініть 'your-ssh-credential-id' на ID креденшіалів, які ви створили
                    script {
                        // Команди для розгортання на EC2
                        sh "scp -o StrictHostKeyChecking=no ./deploy/docker-compose.yml ${EC2_HOST}:/home/ec2-user/"
                        sh "ssh -o StrictHostKeyChecking=no ${EC2_HOST} 'docker-compose -f /home/ec2-user/docker-compose.yml pull && docker-compose -f /home/ec2-user/docker-compose.yml up -d'"
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
