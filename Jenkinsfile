pipeline {
    agent {
        node {
            label 'robomart'
        }
    }
    environment {
        ACCOUNT_ID = "522534289017"
        appVersion = ""
        PROJECT = "robomart"
        COMPONENT = "catalogue"
        // AWS_ID = "b6c11948-340e-4c43-a12d-1e75652ee3ae"

    }
    options{
        timeout(time: 180, unit: 'SECONDS')
        disableConcurrentBuilds()
    }
    stages {
        stage ('Read Version') {
            steps {
                script {
                    def pkg = readJSON file: 'package.json'
                    appVersion = pkg.version
                    echo "app version is : ${appVersion}"
                    echo "App Name    : ${pkg.name}"

                }
            }
        }

        stage ('Install dependencies') {
            steps {
                script {
                    sh  """
                      npm install
                """
                }
            }
        }
        stage ('Unit Testing') {
            steps {
                script {
                    sh  """
                      npm test
                """
                }
            }
        }
        // stage ('Sonar Scan'){
        //     environment {
        //         def scannerHome = tool 'sonar-8.0'
        //     }
        //     steps {
        //         script {
        //             withSonarQubeEnv('sonar-server') {
        //                 sh "${scannerHome}/bin/sonar-scanner"       
        //             }
        //         }
        //     }
        // }
        // stage("Quality Gate Check") {
        //     steps {
        //         script {
        //             // Wait for SonarQube quality gate result
        //             def qualityGateStatus = waitForQualityGate abortPipeline: false

        //             echo "=================================="
        //             echo "SonarQube Quality Gate Status: ${qualityGateStatus.status}"
        //             echo "=================================="

        //             if (qualityGateStatus.status == 'OK') {
        //                 echo "✅ Sonar Scan PASSED"
        //             } else {
        //                 echo "❌ Sonar Scan FAILED"
        //                 error "Pipeline aborted due to quality gate failure: ${qualityGateStatus.status}"
        //             }
        //         }
        //     }
        // }

        stage('Dependabot Security Check') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {

                        def response = sh(
                            script: """
                                curl -s -L \
                                -H "Accept: application/vnd.github+json" \
                                -H "Authorization: Bearer $GITHUB_TOKEN" \
                                https://api.github.com/repos/azharmd-dev/catalogue/dependabot/alerts
                            """,
                            returnStdout: true
                        ).trim()

                        def alerts = readJSON text: response

                        if (alerts.size() == 0) {
                            echo "✅ No Dependabot vulnerabilities found"
                        } else {
                            echo "⚠️ Found ${alerts.size()} Dependabot alert(s)"

                            def highRisk = alerts.findAll {
                                it.security_advisory.severity in ['high', 'critical']
                            }

                            if (highRisk.size() > 0) {
                                echo "❌ High/Critical vulnerabilities detected!"
                                highRisk.each {
                                    echo "Package: ${it.dependency.package.name} | CVE: ${it.security_advisory.cve_id}"
                                }
                                error("Pipeline failed due to security vulnerabilities")
                            } else {
                                echo "✅ Only low/medium vulnerabilities found"
                            }
                        }
                    }
                }
            }
        }
        stage ('Build Docker image') {
            steps {
                script {
                    withAWS(region:'us-east-1', credentials: 'aws-creds') {
                        sh """
                            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com
                            docker build -t ${ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} .
                            docker images
                            docker push ${ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}

                        """    
                    }
                }
            }
        }
        stage ('Testing') {
            steps {
                script {
                    sh  """
                    echo "Testing is Success"
                    sleep 1
                """
                }
            }
        }

        stage ('Deploying') {
            steps {
                script {
                    sh  """
                    echo "Deploying is Success"
                    sleep 1
                """
                }
            }
        }
    }
    post {
        always {
            echo "Post Message after stages"
            cleanWs()
        }
        success {
            echo "Build is success"
        }
        failure {
            echo "Build is failure"
        }
    }
    
}
