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
                    bat 'docker-compose build my-app mongo mongo-express'
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                dir("${DOCKER_COMPOSE_DIR}") {
                    bat 'docker-compose down || exit 0'
                    bat 'docker-compose up -d'
                }
            }
        }
        
        stage('Verify') {
            steps {
                echo 'Verifying deployment...'
                bat 'timeout /t 15 /nobreak' // Wait for containers to start
                bat '''
                    echo Checking Node.js application...
                    curl -f http://localhost:3000 || exit 1
                    echo Checking Mongo Express...
                    curl -f http://localhost:8081 || exit 1
                '''
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up...'
            dir("${DOCKER_COMPOSE_DIR}") {
                bat 'docker-compose down || exit 0'
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