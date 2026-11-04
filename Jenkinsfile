pipeline {
    options { timestamps() }
    agent any
    stages {
        stage('Check scm') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                echo "Building ...${BUILD_NUMBER}"
                echo "Build completed"
            }
        }
        stage('Test') {
            steps {
                bat 'docker run --rm -v "%WORKSPACE%":/app -w /app python:3.9-alpine sh -c "pip install Flask unittest-xml-reporting && python app_tests.py"'
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
