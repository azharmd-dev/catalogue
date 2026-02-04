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
        timeout(time: 30, unit: 'SECONDS')
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
            when {
                expression {
                    params.ENV == 'dev'
                }
            }
            steps {
                script {
                    sh  """
                    echo "Deploying is Success"
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