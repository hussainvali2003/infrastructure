pipeline {
    agent any

    environment {
        WORKSPACE_DIR = "${env.WORKSPACE}"
    }

    stages {

        stage('Start Pipeline') {
            steps {
                echo "🚀 CI/CD Pipeline Started"
            }
        }

        stage('Checkout All Services') {
            parallel {

                stage('Discovery Server') {
                    steps {
                        dir("${WORKSPACE_DIR}/discovery-server") {
                            git branch: 'main',
                            url: 'https://github.com/hussainvali2003/discovery-server.git'
                        }
                    }
                }

                stage('API Gateway') {
                    steps {
                        dir("${WORKSPACE_DIR}/api-gateway") {
                            git branch: 'main',
                            url: 'https://github.com/hussainvali2003/api-gateway.git'
                        }
                    }
                }

                stage('Order Service') {
                    steps {
                        dir("${WORKSPACE_DIR}/order-service") {
                            git branch: 'main',
                            url: 'https://github.com/hussainvali2003/order-service.git'
                        }
                    }
                }

                stage('Restaurant Service') {
                    steps {
                        dir("${WORKSPACE_DIR}/restaurant-service") {
                            git branch: 'main',
                            url: 'https://github.com/hussainvali2003/restaurant-service.git'
                        }
                    }
                }
            }
        }

        stage('Build All Services') {
            parallel {

                stage('Build Discovery') {
                    steps {
                        dir("${WORKSPACE_DIR}/discovery-server") {
                            sh './gradlew clean build -x test'
                        }
                    }
                }

                stage('Build Gateway') {
                    steps {
                        dir("${WORKSPACE_DIR}/api-gateway") {
                            sh './gradlew clean build -x test'
                        }
                    }
                }

                stage('Build Order') {
                    steps {
                        dir("${WORKSPACE_DIR}/order-service") {
                            sh './gradlew clean build -x test'
                        }
                    }
                }

                stage('Build Restaurant') {
                    steps {
                        dir("${WORKSPACE_DIR}/restaurant-service") {
                            sh './gradlew clean build -x test'
                        }
                    }
                }
            }
        }

        stage('Docker Compose Deploy') {
            steps {
                dir("${WORKSPACE_DIR}/infrastructure") {
                    sh 'docker compose down || true'
                    sh 'docker compose up -d --build'
                }
            }
        }

        stage('Verify') {
            steps {
                sh 'docker ps'
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline Success"
        }
        failure {
            echo "❌ Pipeline Failed"
        }
    }
}