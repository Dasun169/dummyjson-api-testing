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
            archiveArtifacts artifacts: 'reports/**', fingerprint: true
        }
        success {
            emailext(
                subject: "✔ API Test Passed - Build #${BUILD_NUMBER}",
                body: """
                <h2>🚀 API Test Execution Successful!</h2>
                <p>Your DummyJSON API test suite completed successfully.</p>
                <p><strong>Collection:</strong> 17 API Endpoints</p>
                <p><strong>Test Cases:</strong> 87 Validations</p>
                <p>✔ Quality Verified & Ready for Deployment</p>
                <br/>
                <p>📎 Full HTML test report is attached.</p>
                """,
                to: "www.dasun544@gmail.com",
                mimeType: "text/html",
                attachLog: false,
                attachmentsPattern: "reports/api-test-report.html"
            )
        }
        failure {
            emailext(
                subject: "❌ API Test Failed - Build #${BUILD_NUMBER}",
                body: """
                <h2>⚠ API Tests Failed</h2>
                <p>One or more tests failed during execution.</p>
                <p>Check the attached HTML report for details.</p>
                """,
                to: "www.dasun544@gmail.com",
                mimeType: "text/html",
                attachLog: false,
                attachmentsPattern: "reports/api-test-report.html"
            )
        }
    }
}
