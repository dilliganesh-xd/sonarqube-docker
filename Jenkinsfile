pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        SCANNER_HOME = tool 'SonarScanner'
        IMAGE_NAME = "dilliganeshdevops/sonarqube-docker:latest"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat """
                    %SCANNER_HOME%\\bin\\sonar-scanner.bat ^
                    -Dsonar.projectKey=week12-sonarqube ^
                    -Dsonar.projectName=week12-sonarqube ^
                    -Dsonar.sources=src ^
                    -Dsonar.java.binaries=target/classes
                    """
                }
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                }
            }
        }

        stage('Docker Push') {
            steps {
                bat 'docker push %IMAGE_NAME%'
            }
        }

        stage('Deploy') {
            steps {
                bat 'docker run --rm %IMAGE_NAME%'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}