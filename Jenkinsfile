pipeline {
    agent any

    triggers {
        // Poll SCM every 2 minutes. Essential fallback for LAN-based systems
        // where public GitHub webhooks cannot directly reach the local Jenkins server.
        pollSCM('H/2 * * * *')
    }

    environment {
        WORKSPACE_DIR = "${env.WORKSPACE}"
        // Restrict Gradle JVM memory to prevent container OOM (Out Of Memory) crashes in parallel builds.
        GRADLE_JVM_OPTS = "-Dorg.gradle.jvmargs=\"-Xmx512m -XX:MaxMetaspaceSize=256m\""
    }

    stages {
        stage('Start Pipeline') {
            steps {
                echo "🚀 CI/CD Pipeline Started for Build #${env.BUILD_NUMBER}"
            }
        }

        stage('Checkout All Services') {
            parallel {
                stage('Checkout Discovery Server') {
                    steps {
                        dir("${WORKSPACE_DIR}/discovery-server") {
                            git branch: 'main',
                            url: 'https://github.com/hussainvali2003/discovery-server.git'
                        }
                    }
                }

                stage('Checkout API Gateway') {
                    steps {
                        dir("${WORKSPACE_DIR}/api-gateway") {
                            git branch: 'main',
                            url: 'https://github.com/hussainvali2003/api-gateway.git'
                        }
                    }
                }

                stage('Checkout Order Service') {
                    steps {
                        // CRITICAL: Clone into order.management folder to match ../order.management path in docker-compose.yml
                        dir("${WORKSPACE_DIR}/order.management") {
                            git branch: 'main',
                            url: 'https://github.com/hussainvali2003/order-service.git'
                        }
                    }
                }

                stage('Checkout Restaurant Service') {
                    steps {
                        // CRITICAL: Clone into restaurent.management folder to match ../restaurent.management path in docker-compose.yml
                        dir("${WORKSPACE_DIR}/restaurent.management") {
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
                            sh "chmod +x gradlew"
                            sh "./gradlew clean build -x test --no-daemon ${env.GRADLE_JVM_OPTS}"
                        }
                    }
                }

                stage('Build Gateway') {
                    steps {
                        dir("${WORKSPACE_DIR}/api-gateway") {
                            sh "chmod +x gradlew"
                            sh "./gradlew clean build -x test --no-daemon ${env.GRADLE_JVM_OPTS}"
                        }
                    }
                }

                stage('Build Order') {
                    steps {
                        dir("${WORKSPACE_DIR}/order.management") {
                            sh "chmod +x gradlew"
                            sh "./gradlew clean build -x test --no-daemon ${env.GRADLE_JVM_OPTS}"
                        }
                    }
                }

                stage('Build Restaurant') {
                    steps {
                        dir("${WORKSPACE_DIR}/restaurent.management") {
                            sh "chmod +x gradlew"
                            sh "./gradlew clean build -x test --no-daemon ${env.GRADLE_JVM_OPTS}"
                        }
                    }
                }
            }
        }

        stage('Docker Compose Deploy') {
            steps {
                dir("${WORKSPACE_DIR}/infrastructure") {
                    echo "🚢 Rebuilding and redeploying updated services..."
                    // We run 'docker compose up -d --build --remove-orphans' with the unique IMAGE_TAG environment variable.
                    // This rebuilds only services with code changes and recreates their containers without stopping MySQL, Zookeeper,
                    // or Kafka, avoiding data loss and service registration drops.
                    sh "IMAGE_TAG=${env.BUILD_NUMBER} docker compose up -d --build --remove-orphans"
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
        always {
            // Clean up dangling Docker images built during the compilation process
            // to prevent the host's disk from filling up over time.
            echo "🧹 Cleaning up dangling Docker images..."
            sh 'docker image prune -f'
        }
        success {
            echo "✅ Pipeline Success for Build #${env.BUILD_NUMBER}"
        }
        failure {
            echo "❌ Pipeline Failed for Build #${env.BUILD_NUMBER}"
        }
    }
}