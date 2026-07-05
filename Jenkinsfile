pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        SCANNER_HOME = tool 'SonarScanner'
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
                bat 'docker build -t sonarqube-docker .'
            }
        }

        stage('Run Docker') {
            steps {
                bat 'docker run --rm sonarqube-docker'
            }
        }
    }
}