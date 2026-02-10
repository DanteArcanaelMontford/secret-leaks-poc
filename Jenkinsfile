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
                echo "🔍 Executando Gitleaks no histórico Git"
        
                docker run --rm \
                  -v "$PWD:/repo" \
                  zricethezav/gitleaks:latest detect \
                  --source=/repo \
                  --log-level=info \
                  --redact \
                  --report-format=json \
                  --report-path=/repo/gitleaks-report.json \
                  --no-git=false
        
                echo ""
                echo "📄 Resumo de leaks encontrados:"
                echo "--------------------------------"
        
                if [ -f gitleaks-report.json ]; then
                  jq -r '
                    .[] |
                    "🔴 [\\(.RuleID)] \\(.Description)
                        📁 Arquivo: \\(.File)
                        🔗 Commit: \\(.Commit)
                        ➖ Linha: \\(.StartLine)-\\(.EndLine)
                    "
                  ' gitleaks-report.json
                fi
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
