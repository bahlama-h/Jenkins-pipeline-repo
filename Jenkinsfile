pipeline {
    agent any
    environment {
        DOCKER_USERNAME = 'bahmah2024' // Replace with your Docker Hub username
        DOCKER_PASSWORD = 'LAMAH@ba2024' // Replace with your Docker Hub password
        TELEGRAM_BOT_TOKEN = '7315603130:AAH9xVOg4KqzhniCdRkMSh38wPN3wFZQNBg'
        TELEGRAM_CHAT_ID = '5584973934'
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/bahlama-h/Jenkins-pipeline-repo.git'
            }
        }
        stage('Login to Docker Hub') {
            steps {
                script {
                    // Debugging: Print environment variables
                    sh 'echo "DOCKER_USERNAME: $DOCKER_USERNAME"'
                    sh 'echo "DOCKER_PASSWORD: $DOCKER_PASSWORD"'
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                }
            }
        }
        stage('Build and Push app1') {
            steps {
                script {
                    // Debugging: Print current stage
                    echo 'Building and pushing app1'
                    try {
                        dockerImage1 = docker.build("bahmah2024/app1:1.0.0-${env.BUILD_ID}", "app1/")
                        sh 'docker push bahmah2024/app1:1.0.0-${env.BUILD_ID}'
                    } catch (Exception e) {
                        echo "Error during Build and Push app1: ${e.message}"
                        throw e
                    }
                }
            }
        }
        stage('Build and Push app2') {
            steps {
                script {
                    // Debugging: Print current stage
                    echo 'Building and pushing app2'
                    try {
                        dockerImage2 = docker.build("bahmah2024/app2:1.0.0-${env.BUILD_ID}", "app2/")
                        sh 'docker push bahmah2024/app2:1.0.0-${env.BUILD_ID}'
                    } catch (Exception e) {
                        echo "Error during Build and Push app2: ${e.message}"
                        throw e
                    }
                }
            }
        }
        stage('Build and Push app3') {
            steps {
                script {
                    // Debugging: Print current stage
                    echo 'Building and pushing app3'
                    try {
                        dockerImage3 = docker.build("bahmah2024/app3:1.0.0-${env.BUILD_ID}", "app3/")
                        sh 'docker push bahmah2024/app3:1.0.0-${env.BUILD_ID}'
                    } catch (Exception e) {
                        echo "Error during Build and Push app3: ${e.message}"
                        throw e
                    }
                }
            }
        }
        stage('Deploy to Swarm') {
            steps {
                script {
                    // Debugging: Print current stage
                    echo 'Deploying to Swarm'
                    try {
                        sshagent(['1c8c36bd-3b50-4ff1-af32-2706c0ea375b']) {
                            sh 'ssh -o StrictHostKeyChecking=no ubuntu@54.175.225.185 "bash /opt/ms-demo/ms-deployment/start.sh"'
                        }
                    } catch (Exception e) {
                        echo "Error during Deploy to Swarm: ${e.message}"
                        throw e
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
