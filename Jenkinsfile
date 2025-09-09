pipeline {

    agent any

    tools {
        nodejs "nodejs-24-4-1"
    }

    stages {
        stage("Install Dependencies") {
            steps {
                echo "Installing Dependencies..."

                sh '''
                    set -ex
                    npm install --no-audit
                '''
            }
        }
        stage('NPM Dependency Scanning') {
            steps {
                echo "NPM Dependency Scanning..."

                sh '''
                    set -ex
                    npm audit --audit-level=critical
                '''
            }
        }
    }
}