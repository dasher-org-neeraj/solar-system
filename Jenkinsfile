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

                        sh 'npm install -g yarn'

                        dependencyCheck additionalArguments: '''
                            --scan \'./\'
                            --out \'./\'
                            --format \'ALL\'
                            --prettyPrint
                            --nvdApiKey b3e7726d-3647-4fc6-a293-e2db6482208f''',
                            odcInstallation: 'dependency-check-12-1-3'

                        dependencyCheckPublisher failedTotalCritical: 1, pattern: 'dependency-check-report.xml', stopBuild: true

                        junit allowEmptyResults: true, stdioRetention: 'FAILED', testResults: 'dependency-check-junit.xml'

                        publishHTML([
                            allowMissing: true,
                            alwaysLinkToLastBuild: true,
                            icon: '',
                            keepAll: true,
                            reportDir: '.',
                            reportFiles: 'dependency-check-report.html',
                            reportName: 'Dependency Check HTML Report',
                            reportTitles: 'Dependency Check HTML Report',
                            useWrapperFileDirectly: false
                        ])
                    }
                }
            }
        }
    }
}