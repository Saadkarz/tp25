# Stage 1: Build - Compile le projet avec Maven
FROM maven:3.8.4-openjdk-17 AS builder
WORKDIR /app

# Copie les fichiers du projet
COPY ./pom.xml .
COPY ./src ./src

# Compile le projet et génère le JAR
RUN mvn clean package -DskipTests

# Stage 2: Runtime - Image légère pour exécuter le JAR
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

# Crée un volume pour les fichiers temporaires
VOLUME /tmp

# Copie le JAR compilé depuis le stage builder
COPY --from=builder /app/target/*.jar voiture-service.jar

# Expose le port du service
EXPOSE 8089

# Commande de démarrage
ENTRYPOINT ["java", "-jar", "/app/voiture-service.jar"]
