pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "my-ecommerce-backend"
        DOCKER_TAG = "${BUILD_NUMBER}"
        REGISTRY = "santhosh2010/my-ecommerce"
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                script {
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: '*/main']],
                        userRemoteConfigs: [[
                            url: 'https://github.com/Santhosh2010-ramesh/E-commerce-Web-Application.git',branch:"main"
                        ]]
                    ])
                }
            }
        }
        
        stage('Verify Checkout') {
            steps {
                script {
                    sh "ls -l"  // List files to ensure `backend/` exists
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh "cd backend && docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'dockerhub-credentials', url: '']) {
                        sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${REGISTRY}:${DOCKER_TAG}"
                        sh "docker push ${REGISTRY}:${DOCKER_TAG}"
                    }
                }
            }
        }
        
        stage('Deploy to Local Docker') {
            steps {
                script {
                    sh "docker stop ecommerce || true"
                    sh "docker rm ecommerce || true"
                    sh "docker run -d --name ecommerce -p 5000:5000 ${REGISTRY}:${DOCKER_TAG}"
                }
            }
        }
    }
    
    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}
