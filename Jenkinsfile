pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK17'
    }

    stages {

        stage('Build with Maven') {
            steps {
                dir('backend') {
                    bat 'mvn -B clean compile'
                }
            }
        }

        stage('Run tests') {
            steps {
                dir('backend') {
                    bat 'mvn -B test'
                }
            }
        }

        stage('Package JAR') {
            steps {
                dir('backend') {
                    bat 'mvn -B package'
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


