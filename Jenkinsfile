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
        stage('Dependency Vulnerability Scan') {
            parallel {
                stage('NPM Dependencies Audit') {
                    steps {
                        sh '''
                            npm audit --audit-level=critical
                            echo $? > audit-result.txt
                            '''
                    }
                }
                stage('OWASP Dependency-Check') {
                    steps {
                        dependencyCheck additionalArguments: '''
                        --scan ./
                        --prettyPrint
                        --format ALL
                        --out ./''', odcInstallation: 'OWASP-DepCheck-v12'

                        dependencyCheckPublisher pattern: 'dependency-check-report.xml'

                        junit allowEmptyResults: true, keepProperties: true, testResults: '**/dependency-check-junit.xml'

                        publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, icon: '', keepAll: true, reportDir: './', reportFiles: 'dependency-check-report.html', reportName: 'Dependency HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                    }
                }
            }
        }
    }
}