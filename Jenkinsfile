pipeline {

```
agent any

stages {

    stage('Clone') {
        steps {
            echo 'Infrastructure Pipeline Started'
        }
    }

    stage('Stop Existing Containers') {
        steps {
            sh 'docker compose down || true'
        }
    }

    stage('Start Infrastructure') {
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
```

}
