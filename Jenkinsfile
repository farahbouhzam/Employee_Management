pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK17'
    }

    stages {

        stage('Clone repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/farahbouhzam/Employee_Management.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn -B clean compile'
            }
        }

        stage('Run tests') {
            steps {
                sh 'mvn -B test'
            }
        }

        stage('Package JAR') {
            steps {
                sh 'mvn -B package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('MySonarQubeServer') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
    }

    post {
        success {
            echo "BUILD SUCCESSFUL!"
        }
        failure {
            echo "BUILD FAILED!"
        }
    }
}

