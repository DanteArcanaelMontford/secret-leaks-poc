pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Security Gate - Gitleaks') {
            steps {
                sh '''
                docker run --rm \
                  -v "$PWD:/repo" \
                  zricethezav/gitleaks:latest detect \
                  --source=/repo \
                  --log-level=error \
                  --redact \
                  --no-git=false
                '''
            }
        }

        stage('Build WebGoat') {
            steps {
                sh '''
                echo "Build do WebGoat só roda se NÃO houver leaks"
                '''
            }
        }
    }

    post {
        failure {
            echo "❌ Security Gate acionado: secrets detectados"
        }
        success {
            echo "✅ Pipeline limpa: sem secrets"
        }
    }
}
