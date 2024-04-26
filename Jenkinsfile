pipeline {
    agent any
    environment {
        IMAGE_NAME = 'mydevopsway/my_nginx_app'
        IMAGE_TAG = 'latest'
        EC2_HOST = 'ec2-user@3.9.144.180' // Реальна IP адреса вашого EC2 інстансу
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
                sshagent(['AWS_CD']) {
                    script {
                        sh "ssh -o StrictHostKeyChecking=no ${EC2_HOST} 'docker pull ${IMAGE_NAME}:${IMAGE_TAG}'"
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
