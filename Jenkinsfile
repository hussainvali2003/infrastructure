pipeline {

    agent any

    stages {

        stage('Start Pipeline') {
            steps {
                echo 'Infrastructure Pipeline Started'
            }
        }

        stage('Clone All Microservices') {
            steps {

                dir('../discovery-server') {
                    git 'https://github.com/hussainvali2003/discovery-server.git'
                }

                dir('../api-gateway') {
                    git 'https://github.com/hussainvali2003/api-gateway.git'
                }

                dir('../order.management') {
                    git 'https://github.com/hussainvali2003/order-service.git'
                }

                dir('../restaurent.management') {
                    git 'https://github.com/hussainvali2003/restaurant-service.git'
                }
            }
        }

        stage('Stop Existing Containers') {
            steps {
                sh 'docker compose down || true'
            }
        }

        stage('Build and Start Containers') {
            steps {
                sh 'docker compose up -d --build'
            }
        }

        stage('Verify Running Containers') {
            steps {
                sh 'docker ps'
            }
        }
    }
}