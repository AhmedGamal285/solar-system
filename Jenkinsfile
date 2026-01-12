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
        stage('SonarQube Scan - SAST') {
            def scannerHome = tool 'sonarqube-8';
            steps {
                sh '''
                    "${scannerHome}/bin/sonar-scanner" \
                        -Dsonar.projectKey=AhmedGamal285_solar-system_85de6be7-3586-4481-9e68-9fa50893a563 \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.login=sqp_5252c7140b0b567383f0051e4f6ffddadb04893a
                '''
            }
        }
    }
}