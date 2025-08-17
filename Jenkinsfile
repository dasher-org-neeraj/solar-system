pipeline {

    agent any

    tools {
      nodejs 'node-24-0-0'
    }

    stages {
        stage("Installing Dependencies"){
            steps {
                sh 'npm install --no-audit'
            }
        }
    }
}