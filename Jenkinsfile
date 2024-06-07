pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dd273feb-d360-48f6-8452-6899dd9b3550')
        TELEGRAM_BOT_TOKEN = '7315603130:AAH9xVOg4KqzhniCdRkMSh38wPN3wFZQNBg'
        TELEGRAM_CHAT_ID = '5584973934'
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/bahlama-h/Jenkins-pipeline-repo.git'
            }
        }
        stage('Build and Push Docker Images') {
            stage('Build and Push app1') {
                steps {
                    script {
                        dockerImage1 = docker.build("bahmah2024/app1:${env.BUILD_ID}", "app1/")
                        docker.withRegistry('https://registry.hub.docker.com', 'DOCKER_HUB_CREDENTIALS') {
                            dockerImage1.push()
                        }
                    }
                }
            }
            stage('Build and Push app2') {
                steps {
                    script {
                        dockerImage2 = docker.build("bahmah2024/app2:${env.BUILD_ID}", "app2/")
                        docker.withRegistry('https://registry.hub.docker.com', 'DOCKER_HUB_CREDENTIALS') {
                            dockerImage2.push()
                        }
                    }
                }
            }
            stage('Build and Push app3') {
                steps {
                    script {
                        dockerImage3 = docker.build("bahmah2024/app3:${env.BUILD_ID}", "app3/")
                        docker.withRegistry('https://registry.hub.docker.com', 'DOCKER_HUB_CREDENTIALS') {
                            dockerImage3.push()
                        }
                    }
                }
            }
        }
        stage('Deploy to Swarm') {
            steps {
                script {
                    sshagent(['1c8c36bd-3b50-4ff1-af32-2706c0ea375b']) {
                        sh 'ssh -o StrictHostKeyChecking=no ubuntu@54.175.225.185 "bash /opt/ms-demo/ms-deployment/start.sh"'
                    }
                }
            }
        }
    }
    post {
        success {
            script {
                sendTelegramNotification("Build ${env.BUILD_ID} succeeded")
            }
        }
        failure {
            script {
                sendTelegramNotification("Build ${env.BUILD_ID} failed")
            }
        }
    }
}

def sendTelegramNotification(String message) {
    sh """
        curl -s -X POST https://api.telegram.org/bot${env.TELEGRAM_BOT_TOKEN}/sendMessage \
        -d chat_id=${env.TELEGRAM_CHAT_ID} \
        -d text="${message}"
    """
}
