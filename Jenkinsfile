pipeline {
    agent any

    tools {
        nodejs 'Node18'
    }

    environment {
        GITHUB_TOKEN = credentials('GITHUB_TOKEN')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Dasun169/dummyjson-api-testing.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install -g newman newman-reporter-htmlextra'
            }
        }

        stage('Run API Tests') {
            steps {
                bat '''
                newman run Newman_Test.json ^
                -e dummyJSON.postman_environment.json ^
                --env-var "access_token=%GITHUB_TOKEN%" ^
                --reporters cli,htmlextra ^
                --reporter-htmlextra-export reports\\api-test-report.html
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'reports/*.html', fingerprint: true
        }
        failure {
            echo "❌ API Tests Failed — Check report in Artifacts"
        }
        success {
            echo "✔ API Tests Passed Successfully 🚀"
        }
    }
}
