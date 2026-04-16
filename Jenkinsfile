pipeline {
    agent any
    
    tools {
        nodejs 'Node-7.8.0'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm test -- --watchAll=false || true'
            }
        }
        
        stage('Set Environment Variables') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        env.HOST_PORT = '3000'
                        env.DOCKER_IMAGE = 'nodemain:v1.0'
                        env.CONTAINER_NAME = 'app-main'
                    } else {
                        env.HOST_PORT = '3001'
                        env.DOCKER_IMAGE = 'nodedev:v1.0'
                        env.CONTAINER_NAME = 'app-dev'
                    }
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${env.DOCKER_IMAGE} ."
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    sh "docker stop ${env.CONTAINER_NAME} 2>/dev/null || true"
                    sh "docker rm ${env.CONTAINER_NAME} 2>/dev/null || true"
                    sh "docker run -d --name ${env.CONTAINER_NAME} -p ${env.HOST_PORT}:3000 -e PORT=3000 ${env.DOCKER_IMAGE}"
                }
            }
        }
    }
}
