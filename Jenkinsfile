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
        stage('Dependency Checking') {
            parallel {
                stage('NPM Dependency Scanning') {
                    steps {
                        echo "NPM Dependency Scanning..."

                        sh '''
                            set -ex
                            npm audit --audit-level=critical
                        '''
                    }
                }
                stage('OWASP Dependency Scanning') {
                    steps {
                        echo "Scanning Dependencies using owasp..."

                        dependencyCheck additionalArguments: '''
                            --scan \'./\'
                            --out \'./\'
                            --format \'ALL\'
                            --prettyPrint
                            --nvdApiKey b3e7726d-3647-4fc6-a293-e2db6482208f
                            --disableYarnAudit''',
                            odcInstallation: 'dependency-check-12-1-3'
                    }
                }
            }
        }
    }
}