# 1) Build stage : compilation avec Maven + Java 17
FROM maven:3.9.6-eclipse-temurin-17 AS builder

WORKDIR /app

# Copier pom.xml et télécharger les dépendances
COPY pom.xml .
RUN mvn dependency:go-offline

# Copier le projet
COPY src ./src

# Compiler le projet
RUN mvn package -DskipTests

# 2) Run stage : image légère pour exécuter le jar
FROM eclipse-temurin:17-jre

WORKDIR /app

# Copier le bon jar
COPY --from=builder /app/target/employee-management-app-0.0.1-SNAPSHOT.jar app.jar

# Exposer le port
EXPOSE 8080

# Lancer l'application
ENTRYPOINT ["java", "-jar", "app.jar"]
