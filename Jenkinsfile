pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                echo 'Building Docker images...'
                dir('demodocker') {
                    sh 'docker-compose build'
                }
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests...'
                dir('demodocker') {
                    sh 'docker-compose run my-app npm test'
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                dir('demodocker') {
                    sh 'docker-compose down'
                    sh 'docker-compose up -d'
                }
            }
        }
        
        stage('Verify') {
            steps {
                echo 'Verifying deployment...'
                sh 'sleep 10' // Wait for containers to start
                sh 'curl -f http://localhost:3000 || exit 1'
                sh 'curl -f http://localhost:8081 || exit 1'
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up...'
            dir('demodocker') {
                sh 'docker-compose down'
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