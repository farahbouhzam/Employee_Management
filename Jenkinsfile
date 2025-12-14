pipeline {
    agent any

    tools {
        maven 'Maven3'      
        jdk 'JDK17'         
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 1, unit: 'HOURS')
        timestamps()
    }

    stages {

        /* ------------------------- CHECKOUT ------------------------- */
        stage('Checkout') {
            steps {
                echo " Checking out repository..."
                checkout scm
            }
        }

        /* ---------------------- BACKEND BUILD ------------------------ */
        stage('Build Backend (no tests)') {
            steps {
                dir('backend') {
                    bat """
                        echo 🔨 Building backend JAR...
                        mvn clean package -DskipTests -U

                        echo Checking JAR:
                        dir target\\*.jar
                    """
                }
            }
        }

        /* --------------------- BACKEND TESTS ------------------------- */
        stage('Run Tests + Jacoco') {
            steps {
                dir('backend') {
                    bat """
                        echo  Running backend tests with JaCoCo...
                        mvn clean verify
                    """
                }
            }
        }

        /* -------------------- SONARQUBE ANALYSIS --------------------- */
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') { 
                    dir('backend') {
                        bat """
                            echo Running SonarQube analysis...

                            mvn sonar:sonar ^
                                -Dsonar.projectKey=employee-management ^
                                -Dsonar.projectName=employee-management ^
                                -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml ^
                                -Dsonar.java.binaries=target/classes
                        """
                    }
                }
            }
        }

        /* ---------------------- FRONTEND BUILD ----------------------- */
        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    bat """
                        echo  Installing frontend dependencies...
                        npm install

                        echo Building frontend...
                        npm run build

                        if exist build (
                            echo Build folder found:
                            dir build
                        ) else if exist dist (
                            echo Dist folder found:
                            dir dist
                        ) else (
                            echo  No build/dist folder created!
                            exit /b 1
                        )
                    """
                }
            }
        }

        /* ---------------------- ARCHIVE ARTIFACTS -------------------- */
        stage('Archive Artifacts') {
            steps {
                echo " Archiving artifacts..."
                archiveArtifacts artifacts: 
                    'backend/target/*.jar,frontend/build/**,frontend/dist/**',
                    allowEmptyArchive: true,
                    fingerprint: true
            }
        }
    }
}

