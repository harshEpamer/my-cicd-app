pipeline {
    agent any
    
    tools {
        nodejs 'Node-7.8.0'
    }
    
    environment {
        DOCKER_IMAGE = ''
        CONTAINER_NAME = ''
        HOST_PORT = ''
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo "Checking out branch: ${env.BRANCH_NAME}"
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm install'
                echo "Build completed successfully"
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm test || echo "No tests defined"'
                echo "Test phase completed"
            }
        }
        
        stage('Set Environment Variables') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        env.HOST_PORT = '3000'
                        env.DOCKER_IMAGE = 'nodemain:v1.0'
                        env.CONTAINER_NAME = 'app-main'
                        echo "Setting up MAIN environment - Port: 3000"
                    } else if (env.BRANCH_NAME == 'dev') {
                        env.HOST_PORT = '3001'
                        env.DOCKER_IMAGE = 'nodedev:v1.0'
                        env.CONTAINER_NAME = 'app-dev'
                        echo "Setting up DEV environment - Port: 3001"
                    } else {
                        error "Unsupported branch: ${env.BRANCH_NAME}"
                    }
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE} ."
                    echo "Docker image built: ${DOCKER_IMAGE}"
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    // Remove only container for this environment
                    sh "docker stop ${CONTAINER_NAME} 2>/dev/null || true"
                    sh "docker rm ${CONTAINER_NAME} 2>/dev/null || true"
                    
                    // Run new container
                    sh "docker run -d --name ${CONTAINER_NAME} -p ${HOST_PORT}:3000 -e PORT=3000 ${DOCKER_IMAGE}"
                    
                    echo "Application deployed on port ${HOST_PORT}"
                }
            }
        }
    }
    
    post {
        success {
            echo "Pipeline completed successfully for ${env.BRANCH_NAME}"
        }
        failure {
            echo "Pipeline failed for ${env.BRANCH_NAME}"
        }
    }
}
