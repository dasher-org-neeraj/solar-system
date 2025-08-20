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
        stage('Dependency Scanning') {
            parallel {
                stage("Dependency Scanning using npm") {
                    steps {
                        echo "scanning dependencies using npm audit..."

                        sh 'npm audit --audit-level=critical'
                    }
                }
                stage('Dependency Scanning Using Owasp') {
                    steps {
                        echo "Scanning dependencies using owasp"

                        dependencyCheck additionalArguments: '''
                            --scan \'./\'
                            --out \'./\'
                            --format \'ALL\'
                            --prettyPrint
                            --nvdCredentialsId b3e7726d-3647-4fc6-a293-e2db6482208f
                            --stopBuild true''',
                            odcInstallation: 'dependency-check-12-1-3'
                    }
                }
            }
        }
    }
}