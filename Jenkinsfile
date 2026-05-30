pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/saichaitanya10/devops-api.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t devops-api:latest .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker stop devops-api || true'
                sh 'docker rm devops-api || true'
                sh 'docker run -d -p 3000:3000 --name devops-api devops-api:latest'
            }
        }

        stage('Health Check') {
            steps {
                sh 'sleep 5'
                sh 'curl -f http://localhost:3000 || exit 1'
            }
        }
    }
}