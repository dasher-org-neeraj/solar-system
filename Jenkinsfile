pipeline {

    agent any

    options {
      buildDiscarder logRotator(artifactNumToKeepStr: '3',numToKeepStr: '3')
      disableConcurrentBuilds(abortPrevious: true)
    }

    tools {
      nodejs 'node-24-0-0'
    }

    environment {
      MONGO_URI = "mongodb://mongodb:27017/mydb"
    }

    stages {
        stage("Installing Dependencies"){

            options(
                timestamps()
                timeout(time: 1, unit: 'HOURS')
            )
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
        stage("Unit Test") {
            steps {

//                 options {
//                     retry(2)
//                 }

                echo "Running Unit Tests..."

                withCredentials([usernamePassword(credentialsId: 'mongodb-creds', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
//                     echo "Seeding database..."
//                     sh 'npm run db:seed'

                    echo "Running Unit Tests..."
                    sh 'npm test'
                }

                junit allowEmptyResults: true, keepProperties: true, keepTestNames: true, stdioRetention: 'ALL', testResults: 'test-results.xml'
            }
        }
    }
}