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

- Java 21+
- Node.js 20+ és npm
- PostgreSQL 15+

## Indítás

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

- [Adatbázis terv](docs/DATABASE_DESIGN.md) - séma, relációk
- [API dokumentáció](docs/API_SPECIFICATION.md) - REST végpontok
- [Architektúra](docs/ARCHITECTURE.md) - rendszer felépítés
- [Fejlesztői útmutató](docs/DEVELOPER_GUIDE.md) - lokális fejlesztés
- [CI/CD](docs/CI_CD.md) - build és deployment
- [Szakdolgozat](https://docs.google.com/document/d/1Lz7IhR00iQkg6FGXcu5oifvF2TkUQprc/edit?usp=sharing&ouid=106284385341877567494&rtpof=true&sd=true) - szakdolgozat

## Szerző

Huszka Adrián Gábor  
SZTE Informatikai Intézet, 2025
