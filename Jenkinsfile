pipeline {
    agent any
    
    environment {
        MAVEN_HOME = tool name: 'Maven', type: 'maven'
        JAVA_HOME = tool name: 'JDK17', type: 'jdk'
        PATH = "${MAVEN_HOME}\\bin;${JAVA_HOME}\\bin;${PATH}"
        DOCKERHUB_USERNAME = 'misswolf4'
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 2, unit: 'HOURS')
        timestamps()
    }

    stages {

        stage('Verify Tools') {
            steps {
                echo "🔍 Checking tools..."
                bat """
                    echo ===== JAVA VERSION =====
                    java -version

                    echo ===== MAVEN VERSION =====
                    mvn -version

                    echo ===== NODE VERSION =====
                    node -v

                    echo ===== NPM VERSION =====
                    npm -v

                    echo ===== DOCKER VERSION =====
                    docker --version
                """
            }
        }

        stage('Checkout') {
            steps {
                echo '📥 Cloning repository...'
                checkout scm
                echo '✅ Repository cloned'
            }
        }

        stage('Validate Structure') {
            steps {
                bat """
                    echo Current directory:
                    cd

                    echo === Root contents ===
                    dir

                    echo === Checking backend ===
                    if exist backend (
                        echo Backend exists
                        if exist backend\\pom.xml (
                            echo backend\\pom.xml OK
                        ) else (
                            echo backend\\pom.xml MISSING
                            exit /b 1
                        )
                    ) else (
                        echo backend folder MISSING
                        exit /b 1
                    )

                    echo === Checking frontend ===
                    if exist frontend (
                        echo Frontend exists
                        if exist frontend\\package.json (
                            echo package.json OK
                        ) else (
                            echo package.json MISSING
                            exit /b 1
                        )
                    ) else (
                        echo frontend folder MISSING
                        exit /b 1
                    )
                """
            }
        }

        stage('Build Backend (JAR no tests)') {
            steps {
                dir('backend') {
                    bat """
                        echo Running Maven clean package...
                        mvn clean package -DskipTests -U

                        echo Checking for JAR file...
                        dir target\\*.jar
                    """
                }
            }
        }

        stage('Run Backend Tests (Jacoco)') {
            steps {
                dir('backend') {
                    bat """
                        echo Running Maven tests...
                        mvn clean verify
                    """
                }
            }
        }

        stage('SonarQube Analysis Backend') {
            steps {
                withSonarQubeEnv('sonar') {
                    dir('backend') {
                        bat """
                            mvn sonar:sonar ^
                            -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml
                        """
                    }
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    bat """
                        echo Installing dependencies...
                        npm install

                        echo Building frontend...
                        npm run build

                        echo Checking build/dist folder...
                        if exist build (
                            echo Build folder OK
                            dir build
                        ) else if exist dist (
                            echo Dist folder OK
                            dir dist
                        ) else (
                            echo ❌ No build/dist folder created!
                            exit /b 1
                        )
                    """
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 
                    'backend/target/*.jar,frontend/build/**,frontend/dist/**',
                    allowEmptyArchive: true,
                    fingerprint: true
            }
        }
    }
}

