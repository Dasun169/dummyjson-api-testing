pipeline {
    agent any

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
                sh 'npm install -g newman newman-reporter-htmlextra'
            }
        }

        stage('Run API Tests') {
            steps {
                sh '''
                newman run collections/DummyJSON.postman_collection.json \
                -e environments/DummyJSON.postman_environment.json \
                --env-var "access_token=$GITHUB_TOKEN" \
                --reporters cli,htmlextra \
                --reporter-htmlextra-export reports/api-test-report.html
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'reports/*.html', fingerprint: true
        }
        failure {
            mail to: 'youremail@gmail.com',
                 subject: "❌ API Tests Failed — Jenkins Build #${BUILD_NUMBER}",
                 body: "Check the report in Jenkins -> Artifacts"
        }
        success {
            mail to: 'youremail@gmail.com',
                 subject: "✔ API Tests Passed Successfully",
                 body: "Great job! Automated API testing is working fine 🚀"
        }
    }
}
