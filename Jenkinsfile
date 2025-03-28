pipeline {
    agent any

    tools {
        maven 'maven-3.6.0' // Ensure this is configured in Jenkins
    }

    environment {
        SNYK_TOKEN = credentials('4ec3242d-7bf8-4c01-b668-db2aa9ab0d91')
    }

    stages {

        stage('Initialize & Cleanup Workspace') {
            steps {
               echo 'Cleaning workspace...'
               sh 'ls -la'
               sh 'rm -rf *'
               sh 'ls -la'
            }
        }

        stage('Git Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/anitkurianesec/juicesnyk.git'
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
                    curl -Lo snyk "https://github.com/snyk/snyk/releases/download/v1.1240.0/snyk-linux"
                    chmod +x snyk
                    export PATH=$PWD:$PATH
                    ./snyk -v
                '''
            }
        }

        stage('Build') {
            steps {
              sh 'mvn package'
            }
        }

        stage('Snyk Test using Snyk CLI') {
            steps {
                sh 'export PATH=$PWD:$PATH && ./snyk test'
            }
        }

        stage('Snyk Monitor using Snyk CLI') {
            steps {
                sh 'export PATH=$PWD:$PATH && ./snyk monitor --org=your-org-name'
            }
        }

        stage('Snyk Code Scan') {
            steps {
                sh 'export PATH=$PWD:$PATH && ./snyk code test'
            }
        }
    }
}
