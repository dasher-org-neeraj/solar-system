pipeline {

    agent {
      kubernetes {
            yaml '''
                apiVersion: v1
                kind: Pod
                metadata:
                  labels:
                    run: test-pod
                  name: test-pod
                spec:
                  containers:
                  - command:
                    - sleep
                    - "99999"
                    image: node:24.8.0-alpine
                    name: test-pod
                    resources: {}
                  restartPolicy: Never
            '''
      }
    }


//     agent {
//         label 'solar-system-agent'
//     }

//     tools {
//         nodejs "nodejs-24-4-1"
//     }

    environment {
      MONGO_URI = "mongodb://mongodb-svc:27017/mydb"
    }

    options {
      buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '1', daysToKeepStr: '', numToKeepStr: '3')
      disableConcurrentBuilds abortPrevious: true
    }

    stages {
        stage('Debug') {
            steps {
                sh '''
                    set -ex
                    echo "--- DEBUGGING AGENT ENVIRONMENT ---"
                    echo "User: $(whoami)"
                    echo "Working Directory: $(pwd)"
                    echo "PATH: $PATH"
                    ls -l
                    uname -n
                    node -v
                    npm version
                    sleep 7200
                    echo "--- END DEBUG ---"
                '''
            }
        }
        stage("Install Dependencies") {

            options {
              timestamps()
            }

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
                        echo "Scanning Dependencies using npm audit..."

                        sh '''
                            set -ex
                            npm audit --audit-level=critical
                        '''
                    }
                }
                stage('OWASP Dependency Scanning') {
                    steps {
                        echo "Scanning Dependencies using owasp..."

                        sh '''
                            set -ex
                            dependency-check.sh \
                            --scan \'./\' \
                            --out \'./\' \
                            --format \'ALL\' \
                            --prettyPrint \
                            --nvdApiKey b3e7726d-3647-4fc6-a293-e2db6482208f \
                            --disableYarnAudit
                        '''

//                         dependencyCheck additionalArguments: '''
//                             --scan \'./\'
//                             --out \'./\'
//                             --format \'ALL\'
//                             --prettyPrint
//                             --nvdApiKey b3e7726d-3647-4fc6-a293-e2db6482208f
//                             --disableYarnAudit'''
// //                             odcInstallation: 'dependency-check-12-1-3'

                        sh 'sleep 7200'

                        dependencyCheckPublisher failedTotalCritical: 1, pattern: 'dependency-check-report.xml', stopBuild: true
                    }
                }
            }
        }
        stage('Unit Testing') {
            steps {

                withCredentials([usernamePassword(credentialsId: 'Mongodb-creds', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {

                    echo "Seeding Planets Data For Unit Testing..."
                    sh 'npm run db:seed'

                    echo "Unit Testing In Progress..."
                    sh 'npm run test'
                }
            }
        }
        stage("Coverage Testing") {
            steps {

                withCredentials([usernamePassword(credentialsId: 'Mongodb-creds', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {

                    catchError(buildResult: 'SUCCESS', message: 'Total Coverage is less than 90%', stageResult: 'UNSTABLE') {

                        echo "Coverage Testing In Progress..."

                        sh 'npm run coverage'
                    }
                }
            }
        }
    }
    post {
        always {
            junit allowEmptyResults: true, stdioRetention: 'FAILED',skipMarkingBuildUnstable: true, testResults: 'dependency-check-junit.xml'

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

            junit allowEmptyResults: true, keepProperties: true, keepTestNames: true,skipMarkingBuildUnstable: true, stdioRetention: 'ALL', testResults: 'test-results.xml'
        }
    }
}