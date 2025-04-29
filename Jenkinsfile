pipeline {
    agent any

    environment {
        SNYK_TOKEN = credentials('snyk')
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
                sh '''
                    export PATH=$PWD:$PATH
                    ./snyk test
                '''
            }
        }

        stage('Snyk Monitor using Snyk CLI') {
            steps {
                sh '''
                    export PATH=$PWD:$PATH
                    ./snyk monitor --org=your-org-name
                '''
            }
        }

        stage('Snyk Code Scan') {
            steps {
                script {
                    echo 'Running Snyk Code Test...'
                    sh '''
                        export PATH=$PWD:$PATH
                        ./snyk auth $SNYK_TOKEN
                        ls -la
                        ./snyk code test --org=your-org-name --debug
                    '''
                }
            }
        }
    }
}
