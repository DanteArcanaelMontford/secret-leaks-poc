pipeline {
    agent any

    stages {

        stage('Checkout PoC Repo') {
            steps {
                checkout scm
            }
        }

        stage('Security Gate - Gitleaks') {
            steps {
                sh '''
                echo "🔍 Executando Gitleaks no histórico do repositório da PoC"

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

        stage('Checkout WebGoat') {
            steps {
                sh '''
                echo "📥 Clonando WebGoat (só executa se o gate passou)"
                git clone https://github.com/WebGoat/WebGoat.git
                '''
            }
        }

        stage('Build WebGoat') {
            steps {
                sh '''
                echo "🔨 Buildando WebGoat"
                cd WebGoat
                ./mvnw clean package -DskipTests
                '''
            }
        }
    }

    post {
        failure {
            echo "❌ Pipeline BLOQUEADA por Security Gate"
        }
        success {
            echo "✅ Pipeline liberada — WebGoat buildado"
        }
    }
}
