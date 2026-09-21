pipeline {

    agent any

    tools {
        jdk 'JDK21'
        maven 'MAVEN'
    }

    environment {
        WAR_FILE = 'target/tomcat-demo.war'
        TOMCAT_URL = 'http://localhost:8081'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out project from GitHub...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building the application using Maven...'
                bat 'mvn clean package'
            }
        }

        stage('Verify WAR') {
            steps {
                echo 'Checking generated WAR file...'
                bat 'dir "%WAR_FILE%"'
            }
        }

        stage('Deploy to Tomcat') {
            steps {

                echo 'Deploying WAR file to Tomcat...'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'tomcat_credential',
                        usernameVariable: 'TOMCAT_USER',
                        passwordVariable: 'TOMCAT_PASSWORD'
                    )
                ]) {

                    bat '''
                        curl -u "%TOMCAT_USER%:%TOMCAT_PASSWORD%" ^
                        --upload-file "%WAR_FILE%" ^
                        "%TOMCAT_URL%/manager/text/deploy?path=/tomcat-demo&update=true"
                    '''
                }
            }
        }
    }

    post {

        success {
            echo '======================================'
            echo 'BUILD SUCCESSFUL'
            echo 'TOMCAT DEPLOYMENT SUCCESSFUL'
            echo '======================================'
        }

        failure {
            echo '======================================'
            echo 'BUILD OR DEPLOYMENT FAILED'
            echo 'Check the Jenkins Console Output'
            echo '======================================'
        }

        always {
            echo 'Jenkins Pipeline Finished.'
        }
    }
}
