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
                    npm install --no-audit
                '''
            }
        }
    }
}