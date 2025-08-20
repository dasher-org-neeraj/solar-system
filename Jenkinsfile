pipeline {

    agent any

    tools {
      nodejs 'node-24-0-0'
    }

    stages {
        stage("Installing Dependencies"){
            steps {
                echo "Installing Dependencies..."

                sh 'npm install --no-audit'
            }
        }
        stage("Dependency Scanning") {
            steps {
                echo "scanning dependencies using npm audit..."

                sh 'npm audit --audit-level=critical'
            }
        }
    }
}