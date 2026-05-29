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

            dir('/var/jenkins_home/workspace/discovery-server') {
                git branch: 'main',
                url: 'https://github.com/hussainvali2003/discovery-server.git'
            }

            dir('/var/jenkins_home/workspace/api-gateway') {
                git branch: 'main',
                url: 'https://github.com/hussainvali2003/api-gateway.git'
            }

            dir('/var/jenkins_home/workspace/order.management') {
                git branch: 'main',
                url: 'https://github.com/hussainvali2003/order-service.git'
            }

            dir('/var/jenkins_home/workspace/restaurent.management') {
                git branch: 'main',
                url: 'https://github.com/hussainvali2003/restaurant-service.git'
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
