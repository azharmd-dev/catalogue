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
        AWS_ID = "b6c11948-340e-4c43-a12d-1e75652ee3ae"

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
        stage ('Build Docker image') {
            steps {
                script {
                    withAWS(region:'us-east-1', credentials: env.AWS_ID) {
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
                    sleep 2
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