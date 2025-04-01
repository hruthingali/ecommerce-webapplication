pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'my-ecommerce-backend'
        DOCKER_TAG = "${BUILD_NUMBER}"
        REGISTRY = 'barathkumar29/my-ecommerce'
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/jkbarathkumar/ecommerce-webapplication.git'            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'cd backend && docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .'
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub-credentials', url: '']) {
                    sh 'docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${REGISTRY}:${DOCKER_TAG}'
                    sh 'docker push ${REGISTRY}:${DOCKER_TAG}'
                }
            }
        }
        
        stage('Deploy to Local Docker') {
            steps {
                script {
                    sh '''
                    docker stop ecommerce || true
                    docker rm ecommerce || true
                    docker run -d -p 5000:5000 --name ecommerce ${REGISTRY}:${DOCKER_TAG}
                    '''
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
