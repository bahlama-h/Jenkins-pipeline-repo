pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('a3e4c020-b83b-43ec-ac21-19c3cb471e90')
        TELEGRAM_BOT_TOKEN = '7315603130:AAH9xVOg4KqzhniCdRkMSh38wPN3wFZQNBg' // Replace with your actual token
        TELEGRAM_CHAT_ID = '5584973934'     // Replace with your actual chat ID
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/bahlama-h/Jenkins-pipeline-repo.git'
            }
        }
        stage('Build Docker Images') {
            parallel {
                stage('Build app1') {
                    steps {
                        script {
                            dockerImage1 = docker.build("bahmah2024/app1:${env.BUILD_ID}", "app1/")
                        }
                    }
                }
                stage('Build app2') {
                    steps {
                        script {
                            dockerImage2 = docker.build("bahmah2024/app2:${env.BUILD_ID}", "app2/")
                        }
                    }
                }
                stage('Build app3') {
                    steps {
                        script {
                            dockerImage3 = docker.build("bahmah2024/app3:${env.BUILD_ID}", "app3/")
                        }
                    }
                }
            }
        }
        stage('Push Docker Images') {
            parallel {
                stage('Push app1') {
                    steps {
                        script {
                            docker.withRegistry('https://registry.hub.docker.com', 'DOCKER_HUB_CREDENTIALS') {
                                dockerImage1.push()
                            }
                        }
                    }
                }
                stage('Push app2') {
                    steps {
                        script {
                            docker.withRegistry('https://registry.hub.docker.com', 'DOCKER_HUB_CREDENTIALS') {
                                dockerImage2.push()
                            }
                        }
                    }
                }
                stage('Push app3') {
                    steps {
                        script {
                            docker.withRegistry('https://registry.hub.docker.com', 'DOCKER_HUB_CREDENTIALS') {
                                dockerImage3.push()
                            }
                        }
                    }
                }
            }
        }
        stage('Deploy to Swarm') {
            steps {
                script {
                    sshagent(['391d335d-2d64-48f0-96ae-f1e52ff2c85f']) {
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
