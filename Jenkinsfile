pipeline {
    agent {
        node {
            label 'robomart'
        }
    }
    environment {
        Name = "Azhar"
        appVersion = ""
    }
    options{
        timeout(time: 120, unit: 'SECONDS')
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
                    sh  """
                        docker build -t catalogue:${appVersion} .
                """
                }
            }
        }
        stage ('Testing') {
            steps {
                script {
                    sh  """
                    echo "Testing is Success"
                    sleep 5
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