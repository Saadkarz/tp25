# 🐳 Lab Docker + Microservices avec Consul

<div align="center">

![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-6DB33F?style=for-the-badge&logo=spring-boot&logoColor=white)
![Consul](https://img.shields.io/badge/Consul-F24C53?style=for-the-badge&logo=consul&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)

**Auteur : Karzouz Saad**

</div>

---

## 📋 Description du Projet

Ce projet démontre la mise en place d'une architecture **microservices** complète utilisant **Docker** et **Docker Compose**. L'architecture comprend :

- 🚗 **Service Voiture** - Gestion des voitures
- 👤 **Service Client** - Gestion des clients
- 🌐 **Gateway Service** - API Gateway pour le routage
- 🔍 **Consul** - Service Discovery et Health Checking
- 🗄️ **MySQL** - Base de données relationnelle
- 📊 **phpMyAdmin** - Interface d'administration MySQL

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Docker Network                          │
│                   (microservices-network)                       │
│                                                                 │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐      │
│  │   Gateway    │    │    Client    │    │   Voiture    │      │
│  │   :8888      │    │    :8088     │    │    :8089     │      │
│  └──────┬───────┘    └──────┬───────┘    └──────┬───────┘      │
│         │                   │                   │               │
│         └───────────────────┼───────────────────┘               │
│                             │                                   │
│                    ┌────────▼────────┐                          │
│                    │     Consul      │                          │
│                    │     :8500       │                          │
│                    └─────────────────┘                          │
│                                                                 │
│  ┌──────────────┐              ┌──────────────┐                │
│  │    MySQL     │◄─────────────│  phpMyAdmin  │                │
│  │    :3306     │              │    :8081     │                │
│  └──────────────┘              └──────────────┘                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🚀 Démarrage Rapide

### Prérequis

- Docker installé
- Docker Compose disponible

### Lancer l'architecture

```bash
# Construire et démarrer tous les services
docker compose up -d --build

# Vérifier l'état des conteneurs
docker compose ps

# Voir les logs
docker compose logs -f
```

### Arrêter l'architecture

```bash
docker compose down
```

---

## 🔗 URLs d'Accès

| Service | URL | Description |
|---------|-----|-------------|
| **Consul UI** | http://localhost:8500 | Interface de découverte de services |
| **phpMyAdmin** | http://localhost:8081 | Administration de la base de données |
| **Gateway** | http://localhost:8888 | Point d'entrée API |
| **Client Service** | http://localhost:8088 | API des clients |
| **Voiture Service** | http://localhost:8089 | API des voitures |

---

## 📸 Captures d'Écran

### 1. Interface Consul - Services Discovery

L'interface Consul affiche tous les microservices enregistrés automatiquement :

![Consul Services](Screenshots/Screenshot%202025-12-28%20134526.png)

**Services enregistrés :**
- ✅ `consul` - Service de découverte
- ✅ `gateway-service` - API Gateway
- ✅ `service-client` - Microservice Client
- ✅ `service-voiture` - Microservice Voiture

---

### 2. phpMyAdmin - Bases de Données

L'interface phpMyAdmin montre les bases de données créées automatiquement :

![phpMyAdmin Databases](Screenshots/Screenshot%202025-12-28%20134437.png)

**Bases de données créées :**
- 📦 `Micro_ClientDB` - Base du service Client
- 📦 `Micro_VoitureDB` - Base du service Voiture

---

## 📁 Structure du Projet

```
tp25/
├── 📄 docker-compose.yml        # Orchestration des conteneurs
├── 📄 README.md                 # Documentation
├── 📁 Screenshots/              # Captures d'écran
│   ├── 🖼️ Screenshot 2025-12-28 134437.png
│   └── 🖼️ Screenshot 2025-12-28 134526.png
├── 📁 clientService/
│   ├── 📄 Dockerfile            # Build multi-stage
│   ├── 📄 pom.xml               # Dépendances Maven
│   └── 📁 src/
│       └── 📁 main/resources/
│           └── 📄 application.yml
├── 📁 voitureService/
│   ├── 📄 Dockerfile
│   ├── 📄 pom.xml
│   └── 📁 src/
│       └── 📁 main/resources/
│           └── 📄 application.yml
└── 📁 gatewayService/
    ├── 📄 Dockerfile
    ├── 📄 pom.xml
    └── 📁 src/
        └── 📁 main/resources/
            └── 📄 application.yml
```

---

## 🐳 Docker Compose - Services

```yaml
services:
  mysql:           # Base de données MySQL
  consul:          # Service Discovery
  gateway-service: # API Gateway
  client-service:  # Microservice Client
  voiture-service: # Microservice Voiture
  phpmyadmin:      # Administration MySQL
```

---

## 📝 Dockerfile Multi-Stage

Chaque microservice utilise un **Dockerfile multi-stage** :

```dockerfile
# Stage 1: Build - Compile avec Maven
FROM maven:3.8.4-openjdk-17 AS builder
WORKDIR /app
COPY ./pom.xml .
COPY ./src ./src
RUN mvn clean package -DskipTests

# Stage 2: Runtime - Image légère
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
```

**Avantages :**
- 🔹 Image finale légère (~200MB vs ~600MB)
- 🔹 Pas de Maven dans l'image de production
- 🔹 Séparation claire build/runtime

---

## 🔑 Points Clés

### Pourquoi `mysql` et pas `localhost` ?

Dans Docker Compose, chaque conteneur a son propre réseau. `localhost` dans un conteneur fait référence au conteneur lui-même, pas à la machine hôte.

```yaml
# ✅ Correct - Nom DNS du conteneur
SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/Micro_ClientDB

# ❌ Incorrect - localhost = le conteneur lui-même
SPRING_DATASOURCE_URL: jdbc:mysql://localhost:3306/Micro_ClientDB
```

### Service Discovery avec Consul

Les microservices s'enregistrent automatiquement dans Consul grâce à Spring Cloud Consul :

```yaml
spring:
  cloud:
    consul:
      host: consul
      port: 8500
      discovery:
        enabled: true
```

---

## 🛠️ Commandes Utiles

```bash
# Voir les conteneurs en cours
docker compose ps

# Logs d'un service spécifique
docker compose logs -f client-service

# Reconstruire un service
docker compose up -d --build client-service

# Supprimer tout (volumes inclus)
docker compose down -v

# Entrer dans un conteneur
docker exec -it mysql-container bash
```

---

## 📊 Ports Utilisés

| Port | Service |
|------|---------|
| 3306 | MySQL |
| 8500 | Consul |
| 8081 | phpMyAdmin |
| 8888 | Gateway |
| 8088 | Client Service |
| 8089 | Voiture Service |

---

## 👨‍💻 Auteur

<div align="center">

**Karzouz Saad**

*Lab Docker + Microservices - Décembre 2025*

</div>

---

<div align="center">

Made with ❤️ using Docker & Spring Boot

</div>
