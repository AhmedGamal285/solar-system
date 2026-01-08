pipeline {
    agent any
    tools {
        nodejs 'nodejs-22-21-1'
    }
    environment {
        MONGO_URI = "mongodc+svc://supercluster.d83jj.mongodb.net/superData"
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

                        // Archive dependency-check JUnit report; skip Checks API noise when no publisher is configured
                        junit allowEmptyResults: true, keepProperties: true, keepLongStdio: true, skipPublishingChecks: true, testResults: '**/dependency-check-junit.xml'

                        publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, icon: '', keepAll: true, reportDir: './', reportFiles: 'dependency-check-report.html', reportName: 'Dependency HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                    }
                }
            }
        }
        stage('Unit Tests') {
            steps {
                withCredentials([usernamePassword(credentialsId: '30a7abb1-429a-4880-a079-2ca81a6dcdfa', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                    sh '''
                        npm test
                    '''
                }
                // Archive JUnit-formatted results so Jenkins can show them in the build
                junit allowEmptyResults: true, keepLongStdio: true, testResults: '**/test-results.xml'
            }
        }
    }
}