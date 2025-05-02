pipeline {
    agent any

    tools {
        maven 'jenkins-maven'
    }

    environment {
        SNYK_TOKEN = credentials('Snyk_token')
    }

    stages {
        stage('Initialize & Cleanup Workspace') {
            steps {
                echo 'Initialize & Cleanup Workspace'
                sh 'rm -rf * .git .gitignore'
                sh 'ls -la'
            }
        }

        stage('Git Clone') {
            steps {
                git url: 'https://github.com/jeff-snyk-demo/testproject-java-maven.git'
                sh 'ls -la'
            }
        }

        stage('Test Build Requirements') {
            steps {
                sh 'java -version'
                sh 'mvn -v'
            }
        }

        stage('Download Snyk CLI') {
            steps {
                sh '''
                    echo "Downloading Snyk CLI..."
                    curl -Lo snyk https://github.com/snyk/cli/releases/download/v1.1296.2/snyk-linux
                    chmod +x snyk
                    ./snyk -v
                '''
            }
        }

        stage('Snyk Code Test using CLI') {
            steps {
                sh './snyk code test'
            }
        }

        stage('Snyk Open Source Test (Allow Failures)') {
            steps {
                script {
                    def snykStatus = sh(script: './snyk test || true', returnStatus: true)
                    if (snykStatus != 0) {
                        echo "Snyk found vulnerabilities but continuing pipeline"
                        currentBuild.result = 'UNSTABLE'
                    }
                }
            }
        }

        stage('Snyk Monitor using CLI') {
            steps {
                sh './snyk monitor'
            }
        }
    }
}
