# CI/CD és Deployment

Ez a dokumentum a build folyamatot és a deployment lehetőségeket írja le.

## Build

### Backend

A Spring Boot alkalmazás JAR-ba buildelődik:

```bash
cd StudentSpaceAPI
./mvnw clean package -DskipTests
```

A kész JAR: `target/main-0.0.1-SNAPSHOT.jar`

Futtatás:
```bash
java -jar target/main-0.0.1-SNAPSHOT.jar
```

### Frontend

```bash
cd StudentSpace
npm run build
```

A build outputja a `dist/StudentSpace/` mappába kerül. Van benne browser és server mappa (SSR miatt).

## Docker

Mindkét alkalmazáshoz készíthető Docker image.

### Backend Dockerfile

`StudentSpaceAPI/Dockerfile`:

```dockerfile
FROM eclipse-temurin:21-jdk-alpine AS build
WORKDIR /app
COPY mvnw .
COPY .mvn .mvn
COPY pom.xml .
RUN chmod +x mvnw && ./mvnw dependency:go-offline -B
COPY src src
RUN ./mvnw package -DskipTests

FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Frontend Dockerfile

`StudentSpace/Dockerfile`:

```dockerfile
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=build /app/dist/StudentSpace ./dist/StudentSpace
COPY --from=build /app/package*.json ./
RUN npm ci --only=production
EXPOSE 4000
CMD ["node", "dist/StudentSpace/server/server.mjs"]
```

### Docker Compose

Az egész stack egy docker-compose.yml-lel indítható:

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: StudentSpace
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres123
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  backend:
    build: ./StudentSpaceAPI
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/StudentSpace
      SPRING_DATASOURCE_USERNAME: postgres
      SPRING_DATASOURCE_PASSWORD: postgres123
    ports:
      - "8080:8080"
    depends_on:
      - postgres

  frontend:
    build: ./StudentSpace
    ports:
      - "4000:4000"
    depends_on:
      - backend

volumes:
  postgres_data:
```

Indítás:
```bash
docker-compose up --build
```

## Deployment opciók

### VPS / VM

1. SSH a szerverre
2. Git clone
3. docker-compose up -d

### Cloud

Bármelyik cloud provider működik ahol futtathatók konténerek:
- AWS: ECS, Fargate
- GCP: Cloud Run
- Azure: Container Apps

### Fontos a produkcióhoz

- Cseréld le az alapértelmezett jelszavakat
- Generálj új JWT secret-et: `openssl rand -base64 64`
- Használj HTTPS-t (pl. nginx reverse proxy-val)
- Állítsd be a CORS-t a tényleges domain-re

## GitHub Actions

Példa workflow ami buildet és tesztet futtat minden push-ra:

```yaml
name: CI

on: [push, pull_request]

jobs:
  backend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          java-version: '21'
          distribution: 'temurin'
      - run: cd StudentSpaceAPI && ./mvnw test

  frontend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: cd StudentSpace && npm ci && npm run build
```

A fájl helye: `.github/workflows/ci.yml`
