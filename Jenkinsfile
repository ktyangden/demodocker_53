pipeline {
    agent any
    
    environment {
        DOCKER_COMPOSE_DIR = 'demodocker'
    }
    
    stages {
        stage('Build') {
            steps {
                echo 'Building Docker images...'
                dir("${DOCKER_COMPOSE_DIR}") {
                    sh 'docker-compose build my-app mongo mongo-express'
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                dir("${DOCKER_COMPOSE_DIR}") {
                    sh 'docker-compose down || true'
                    sh 'docker-compose up -d'
                }
            }
        }
        
        stage('Verify') {
            steps {
                echo 'Verifying deployment...'
                sh 'sleep 15' // Wait for containers to start
                sh '''
                    echo "Checking Node.js application..."
                    curl -f http://localhost:3000 || exit 1
                    echo "Checking Mongo Express..."
                    curl -f http://localhost:8081 || exit 1
                '''
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up...'
            dir("${DOCKER_COMPOSE_DIR}") {
                sh 'docker-compose down || true'
            }
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
} 