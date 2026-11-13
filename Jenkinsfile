pipeline {
    options { timestamps() }
    agent any
    
    environment {
        DOCKER_ID = 'sofiatrr'
        IMAGE_NAME = 'flask-app'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

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
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    // 1. Авторизація в Docker Hub
                    bat 'docker login -u "%DOCKER_USERNAME%" -p "%DOCKER_PASSWORD%"'
                    
                    // 2. Створення Docker-образу з двома тегами (номер збірки та latest)
                    bat 'docker build -t %DOCKER_ID%/%IMAGE_NAME%:%IMAGE_TAG% -t %DOCKER_ID%/%IMAGE_NAME%:latest .'
                    
                    // 3. Відправка образу на Docker Hub
                    bat 'docker push %DOCKER_ID%/%IMAGE_NAME%:%IMAGE_TAG%'
                    bat 'docker push %DOCKER_ID%/%IMAGE_NAME%:latest'
                }
            }
        }
    }
}
