pipeline {

    agent any

    tools {
        nodejs "nodejs-24-4-1"
    }

    stages {
        stage("Node Version Check") {
            steps {
                sh '''
                    node -v
                    npm -v
                '''
            }
        }
    }
}