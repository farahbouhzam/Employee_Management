pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK17'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/farahbouhzam/Employee_Management.git'
            }
        }

        stage('Build + Test (backend)') {
            steps {
                dir('backend') {
                    bat "mvn clean verify"
                }
            }
        }

        stage('Package') {
            steps {
                dir('backend') {
                    bat "mvn clean package"
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('backend') {
                    withSonarQubeEnv('sonarqube') {
                        bat "mvn sonar:sonar -Dsonar.projectKey=employee-management -Dsonar.projectName='employee-management'"
                    }
                }
            }
        }
    }
}

