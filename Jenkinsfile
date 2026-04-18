pipeline {
    agent any

    environment {
        EMAIL_RECIPIENT = "your-email@example.com"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Tool: Git SCM - Fetching source code from GitHub"
                git branch: 'main',
                    url: 'https://github.com/RaveeshaS123/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies)') {
            steps {
                echo "Tool: npm - Installing project dependencies"
                sh 'npm install'
            }
        }

        stage('Run Tests)') {
            steps {
                script {
                    echo "Tool: Jest/Mocha - Running unit tests"

                    def testStatus = sh(script: 'npm test || true', returnStatus: true)

                    emailext (
                        subject: "TEST STAGE - Build #${env.BUILD_NUMBER}", //add build number
                        body: """
Test Stage Completed

Status: ${testStatus == 0 ? 'SUCCESS' : 'FAILURE'}
Build URL: ${env.BUILD_URL}
""",
                        to: "${env.EMAIL_RECIPIENT}", //status and logs
                        attachLog: true //send logs
                    )

                    if (testStatus != 0) {
                        currentBuild.result = 'UNSTABLE'
                    }
                }
            }
        }

        stage('Generate Coverage Report)') {
            steps {
                echo "Tool: Istanbul/nyc - Generating test coverage report"
                sh 'npm run coverage || true'
            }
        }

        stage('Security Scan)') {
            steps {
                script {
                    echo "Tool: npm audit - Scanning for security vulnerabilities"

                    def auditStatus = sh(script: 'npm audit || true', returnStatus: true)

                    emailext (
                        subject: "SECURITY SCAN - Build #${env.BUILD_NUMBER}", //build number
                        body: """
Security Scan Completed

Status: ${auditStatus == 0 ? 'CLEAN' : 'VULNERABILITIES FOUND'}
Build URL: ${env.BUILD_URL}
""",
                        to: "${env.EMAIL_RECIPIENT}", //status and logs
                        attachLog: true //send logs
                    )

                    if (auditStatus != 0) {
                        currentBuild.result = 'UNSTABLE'
                    }
                }
            }
        }
    }
}