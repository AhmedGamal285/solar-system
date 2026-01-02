pipeline {
    agent any

    stages {
        stage('Checkout SCM') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }
        stage('Check Node and NPM Versions') {
            steps {
                sh ' node --version '
                sh ' npm --version '
            }
        }
    }
}