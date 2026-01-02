pipeline {
    agent any
    environment {
        PATH = "${tool 'nodejs-22-21-1'}/bin:$PATH"
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
    }
}