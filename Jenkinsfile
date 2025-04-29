pipeline {
    agent any

    tools {
        maven 'maven-3.6.0' // Ensure this Maven version is configured in Jenkins Global Tool Configuration
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
                echo 'Cloning repository...'
                git branch: 'main', url: 'https://github.com/anitkurianesec/juicesnyk.git'
                sh 'ls -la'
            }
        }

        stage('Verify Build Environment') {
            steps {
                echo 'Checking Java and Maven versions...'
                sh 'java -version'
                sh 'mvn -v'
            }
        }

        stage('Download Snyk CLI') {
            steps {
                echo 'Downloading Snyk CLI...'
                sh '''
                    curl -Lo snyk "https://github.com/snyk/snyk/releases/download/v1.1240.0/snyk-linux"
                    chmod +x snyk
                    export PATH=$PWD:$PATH
                    ./snyk -v
                '''
            }
        }

        stage('Build Project') {
            steps {
                echo 'Building project using Maven...'
                sh 'mvn package'
            }
        }

        stage('Snyk Test using CLI') {
            steps {
                echo 'Running Snyk test...'
                sh 'export PATH=$PWD:$PATH && ./snyk test'
            }
        }

        stage('Snyk Monitor using CLI') {
            steps {
                echo 'Sending project snapshot to Snyk Monitor...'
                sh 'export PATH=$PWD:$PATH && ./snyk monitor --org=your-org-name'
            }
        }

        stage('Snyk Code Scan') {
            steps {
                script {
                    echo 'Running Snyk Code analysis...'
                    sh '''
                        export PATH=$PWD:$PATH
                        ./snyk auth $SNYK_TOKEN
                        ./snyk code test --org=your-org-name --debug
                    '''
                }
            }
        }
    }
}
