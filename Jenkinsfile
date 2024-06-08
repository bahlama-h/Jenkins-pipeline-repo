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
                    sh '''
                        #!/bin/bash
                        echo "DOCKER_USERNAME: ${DOCKER_USERNAME}"
                        echo "DOCKER_PASSWORD: ${DOCKER_PASSWORD}"
                        echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin
                    '''
                }
            }
        }
        stage('Build and Push app1') {
            steps {
                script {
                    // Debugging: Print current stage
                    echo 'Building and pushing app1'
                    try {
                        dockerImage1 = docker.build("bahmah2024/app1:${env.BUILD_ID}", "app1/")
                        sh '''
                            #!/bin/bash
                            docker push bahmah2024/app1:${BUILD_ID}
                        '''
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
                        dockerImage2 = docker.build("bahmah2024/app2:${env.BUILD_ID}", "app2/")
                        sh '''
                            #!/bin/bash
                            docker push bahmah2024/app2:${BUILD_ID}
                        '''
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
                        dockerImage3 = docker.build("bahmah2024/app3:${env.BUILD_ID}", "app3/")
                        sh '''
                            #!/bin/bash
                            docker push bahmah2024/app3:${BUILD_ID}
                        '''
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
                        sshagent(['my-ssh-key']) {
                            sh '''
                                #!/bin/bash
                                ssh -o StrictHostKeyChecking=no ubuntu@54.175.225.185 "bash -x /opt/ms-demo/ms-deployment/start.sh"
                            '''
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
    sh '''
        #!/bin/bash
        curl -s -X POST https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendMessage -d chat_id=${TELEGRAM_CHAT_ID} -d text="${message}"
    '''
}
