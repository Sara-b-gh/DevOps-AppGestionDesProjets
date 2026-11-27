pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Recuperation du projet...'
                checkout scm
            }
        }

        stage('Build Backend') {
            steps {
                echo 'Compilation du Backend Spring Boot...'
                dir('backend/backend') {
                    sh 'chmod +x mvnw'
                    sh './mvnw clean package -DskipTests'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                echo 'Construction du Frontend Angular...'
                dir('frontend') {
                    sh 'docker run --rm -v "$PWD":/app -w /app node:24 npm ci --retry 5 --fetch-timeout=300000'
                    sh 'docker run --rm -v "$PWD":/app -w /app node:24 npm run build'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                echo 'Construction des images Docker...'
                sh 'docker build -t devops-backend:1.0 ./backend/backend'
                sh 'docker build -t devops-frontend:1.0 ./frontend'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploiement de l application...'
                sh 'docker compose up -d'
                sh 'docker compose ps'
            }
        }
    }
}
