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

        stage('Build & Test') {
            steps {
                dir('backend') {
                    bat "mvn clean verify"
                }
            }
        }

        stage('Package App') {
            steps {
                dir('backend') {
                    bat "mvn clean package -DskipTests"
                }
            }
        }

        stage('SonarQube Analysis') {
    steps {
        dir('backend') {
            withSonarQubeEnv('sonarqube') {
                bat """
                    mvn sonar:sonar ^
                    -Dsonar.projectKey=employee-management ^
                    -Dsonar.projectName=employee-management ^
                    -Dsonar.java.binaries=target/classes ^
                    -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml
                """
            }
        }
    }
}

        }
    }
}


        // Décommenter ceci plus tard si tu veux ajouter Docker :
        /*
        stage('Build Docker Image') {
            steps {
                dir('backend') {
                    bat "docker build -t farahbf/employee-app:1.0 ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-token', variable: 'TOKEN')]) {
                    bat "docker login -u farahbf -p %TOKEN%"
                    bat "docker push farahbf/employee-app:1.0"
                }
            }
        }
        */
    }
}

