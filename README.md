# 🔧 E-Commerce Config Server

Spring Cloud Config Server for centralized configuration management.

## Overview

This service provides centralized configuration for all microservices. It reads configuration files from a Git repository (`ecom-config-repo`) and serves them via REST API.

## How It Works

1. Config Server starts and connects to `ecom-config-repo` (Git repository)
2. Microservices connect to Config Server on startup
3. Config Server serves appropriate configuration based on service name and profile

## Running Locally

### Prerequisites
- Java 25
- Maven 3.9+
- Access to `ecom-config-repo` repository

### Configuration

Set the config repository URI:

**Option 1: Local file path (for development)**
```yaml
# application.yml
spring:
  cloud:
    config:
      server:
        git:
          uri: file:///path/to/ecom-config-repo
```

**Option 2: Git repository URL**
```yaml
# application.yml
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/subhm4dev/ecom-config-repo.git
```

**Option 3: Environment variable**
```bash
export CONFIG_REPO_URI=https://github.com/subhm4dev/ecom-config-repo.git
```

### Run

```bash
mvn spring-boot:run
```

The server will start on port **8888**.

## Endpoints

- **Health check:** `http://localhost:8888/actuator/health`
- **Service config:** `http://localhost:8888/{service-name}/{profile}`
  - Example: `http://localhost:8888/gateway/default`
  - Example: `http://localhost:8888/identity-service/dev`

## Microservice Integration

To use this Config Server in your microservices:

1. Add dependency:
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```

2. Add to `bootstrap.yml`:
```yaml
spring:
  config:
    import: optional:configserver:http://localhost:8888
  application:
    name: gateway  # Service name (used to find config)
```

## Docker Deployment

```dockerfile
FROM openjdk:25-jdk-slim
WORKDIR /app
COPY target/config-server-*.jar app.jar
EXPOSE 8888
ENTRYPOINT ["java", "-jar", "app.jar"]
```

## Notes

- Config Server pulls from Git on startup and on refresh
- Use `@RefreshScope` in microservices to reload config without restart
- For production, use HTTPS and authentication for Git repository access

