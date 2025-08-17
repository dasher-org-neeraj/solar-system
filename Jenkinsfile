pipeline {

    agent any

    tools {
      nodejs 'node-24-0-0'
    }

    stages {
        stage("node version check"){
            steps {
                sh '''
                    node -v
                    npm -v
                '''
            }
        }
    }
}