pipeline {
    options { timestamps() }
    agent none
    stages {
        stage('Check scm') {
            agent any
            steps {
                checkout scm
            }
        }
        stage('Build') {
            agent any
            steps {
                echo "Building ...${BUILD_NUMBER}"
                echo "Build completed"
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'alpine'
                    args '-u="root"'
                }
            }
            steps {
                sh 'apk add --update python3 py-pip'
                sh 'pip install Flask unittest-xml-reporting'
                sh 'python3 app_tests.py'
            }
            post {
                always {
                    junit 'test-reports/*.xml'
                }
                success {
                    echo "Application testing successfully completed"
                }
                failure {
                    echo "Oooppss!!! Tests failed!"
                }
            }
        }
    }
}