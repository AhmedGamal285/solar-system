pipeline {
    agent any
    tools {
        nodejs 'nodejs-22-21-1'
    }
    environment {
        MONGO_URI = "localhost:27017/testdb"
        MONGO_DB_Cred = credentials('mongo_cred')
        MONGO_USERNAME = credentials('mongo-db-user')
        MONGO_PASSWORD = credentials('mongo-db-password')
    }

    options {
        disableConcurrentBuilds abortPrevious: true
        disableResume()
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
            options {
                timestamps()
            }
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
                    }
                }
            }
        }
        stage('Unit Tests') {
            options {
                retry(2)
            }
            steps {
                catchError(buildResult: 'SUCCESS', message: 'Oops! It will be fixed next release', stageResult: 'UNSTABLE') {
                sh '''
                    npm test
                ''' }
            }
        }
        stage('Code Coverage') {
            name: 'Generate Code Coverage Report'
            steps {
                catchError(buildResult: 'SUCCESS', message: 'Oops! It will be fixed next release', stageResult: 'UNSTABLE') {
                    sh '''
                        echo URL: $MONGO_URI
                        echo "URL: ${MONGO_URI}"
                        echo username: $MONGO_DB_Cred_USR
                        echo "username: ${MONGO_DB_Cred_USR}"
                        echo password: $MONGO_DB_Cred_PSW
                        echo creditional: $MONGO_DB_Cred
                        echo username: $MONGO_USERNAME
                        echo password: $MONGO_PASSWORD
                        npm run coverage
                    ''' }
            }
        }
    }
    post {
        always {
           // Archive dependency-check JUnit report; skip Checks API noise when no publisher is configured
            junit allowEmptyResults: true, keepProperties: true, keepLongStdio: true, skipPublishingChecks: true, testResults: '**/dependency-check-junit.xml'
            publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, icon: '', keepAll: true, reportDir: './', reportFiles: 'dependency-check-report.html', reportName: 'Dependency HTML Report', reportTitles: '', useWrapperFileDirectly: true])
            // Archive JUnit-formatted results so Jenkins can show them in the build
            junit allowEmptyResults: true, keepLongStdio: true, testResults: '**/test-results.xml'
            // Publish code coverage HTML report
            publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, icon: '', keepAll: true, reportDir: 'coverage/lcov-report', reportFiles: 'index.html', reportName: 'Code Coverage HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}