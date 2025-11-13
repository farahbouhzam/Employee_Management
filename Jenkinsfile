pipeline {
    agent any

    tools {
        maven "MAVEN_HOME"
        jdk "JAVA_HOME"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/farahbouhzam/Employee_Management.git'
            }
        }

        stage('Compilation') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build JAR') {
            steps {
                sh 'mvn package -DskipTests=false'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }

    }
}

