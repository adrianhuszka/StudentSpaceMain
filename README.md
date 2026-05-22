# StudentSpace

Középiskolai oktatási platform - szakdolgozat projekt a Szegedi Tudományegyetem Informatikai Intézetéhez.

## Mi ez?

A StudentSpace egy oktatási platform, amit középiskoláknak fejlesztettem. A rendszer lehetővé teszi tananyagok kezelését, feladatok készítését és tantárgyankénti fórum kommunikációt.

Főbb funkciók:

- Szakmák és tantárgyak struktúrált rendszere
- Modulok és tananyagok (szöveg, PDF)
- Interaktív feladatok (feleletválasztós, igaz/hamis)
- Tantárgyankénti fórumok
- Szerepkör alapú jogosultságkezelés

## Tech stack

**Frontend:** Angular 20, TailwindCSS, ng-zorro-antd, SSR támogatással

**Backend:** Spring Boot 4 (Java 21), Spring Security, JWT autentikáció

**Adatbázis:** PostgreSQL

## Előfeltételek

- Docker + Docker Compose (ajánlott futtatás)
- vagy lokális fejlesztéshez:
  - Java 21+
  - Node.js 20+ és npm
  - PostgreSQL 15+

## Gyors indítás (Docker Compose)

### 1) `.env` létrehozása

Másold a mintát:

```bash
cp .env.example .env
```

Windows PowerShell alatt:

```powershell
Copy-Item .env.example .env
```

Minimum ezeket érdemes ellenőrizni a `.env` fájlban:

- `API_URL=https://api-student-space.pollak.info/api/v1` (vagy saját API URL)
- `FRONTEND_URL=https://student-space.pollak.info/` (vagy saját frontend URL)
- `POSTGRES_*` változók

### 2) Stack indítása

```bash
docker compose up -d
```

### 3) Elérés

- Frontend: `http://localhost:4000` (vagy a `.env` szerinti `FRONTEND_PORT`)
- Backend API: `http://localhost:8080/api/v1` (vagy a `.env` szerinti `BACKEND_PORT`)
- Swagger: `http://localhost:8080/swagger-ui.html`

### 4) Hasznos parancsok

```bash
docker compose ps
docker compose logs -f backend
docker compose logs -f frontend
docker compose down
```

Ha adatbázis-jelszó eltérés miatt indulási hiba van:

```bash
docker compose down -v
docker compose up -d
```

## Lokális fejlesztői indítás

### Adatbázis

```bash
psql -U postgres
CREATE DATABASE StudentSpace;
\q
```

### Backend

```bash
cd StudentSpaceAPI
./mvnw spring-boot:run
```

Fut a `http://localhost:8080` címen. API dokumentáció (Swagger): http://localhost:8080/swagger-ui.html

### Frontend

```bash
cd StudentSpace
npm install
npm start
```

Elérhető: `http://localhost:4200`

Backend lokálisan: `http://localhost:8080`.

## Projekt struktúra

```
StudentSpaceApp/
├── StudentSpace/           # Angular frontend
│   └── src/app/
│       ├── pages/      # Oldalak (home, login, admin, stb.)
│       ├── services/   # API hívások
│       └── components/ # Közös komponensek
│
├── StudentSpaceAPI/        # Spring Boot backend
│   └── src/main/java/hu/StudentSpace/main/
│       ├── auth/       # JWT autentikáció
│       ├── users/      # Felhasználókezelés
│       ├── professions/# Szakmák
│       ├── subject/    # Tantárgyak
│       ├── module/     # Modulok
│       └── forum/      # Fórum
│
└── docs/               # Dokumentáció
```

## Dokumentáció

- [Szakdolgozat](https://docs.google.com/document/d/1Lz7IhR00iQkg6FGXcu5oifvF2TkUQprc/edit?usp=sharing&ouid=106284385341877567494&rtpof=true&sd=true) - szakdolgozat

## Szerző

Huszka Adrián Gábor
