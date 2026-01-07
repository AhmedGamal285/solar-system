pipeline {
    agent any
    tools {
        nodejs 'nodejs-22-21-1'
    }

    stages {
        stage('Check Node and NPM Versions') {
            steps {
                sh '''
                    node -v
                    npm -v
                '''
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install --no-audit'
            }
        }
        stage('NPM Dependencies Audit') {
            steps {
                sh 'npm audit --audit-level=critical'
            }
        }
    }
}