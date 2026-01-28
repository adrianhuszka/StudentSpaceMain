# Fejlesztői útmutató

Ez a dokumentum leírja hogyan állítsd be a fejlesztői környezetet és indítsd el a projektet lokálisan.

## Szükséges szoftverek

- Java 21 (ajánlott: Adoptium Temurin)
- Node.js 20+
- PostgreSQL 15+
- Git

Ellenőrzés:
```bash
java -version   # 21.x kell
node -v         # v20.x kell
psql --version  # 15+ kell
```

## Adatbázis

A projekt PostgreSQL-t használ. Hozd létre az adatbázist:

```bash
psql -U postgres
CREATE DATABASE StudentSpace;
\q
```

A kapcsolat beállításai az `StudentSpaceAPI/src/main/resources/application.yaml` fájlban vannak. Alapértelmezetten:

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/StudentSpace
    username: postgres
    password: postgres123
```

Ha más jelszót használsz, módosítsd itt.

## Backend indítása

```bash
cd StudentSpaceAPI
./mvnw spring-boot:run
```

Windows-on: `mvnw.cmd spring-boot:run`

Ha minden jó, látod majd:
```
Started MainApplication in X seconds
```

A backend a 8080-as porton fut.

## Frontend indítása

```bash
cd StudentSpace
npm install
npm start
```

Az első `npm install` eltart pár percig. Utána a dev server a 4200-as porton indul.

Böngészőben: http://localhost:4200

## IDE beállítások

### IntelliJ (backend)

1. Nyisd meg az StudentSpaceAPI mappát
2. Project SDK: Java 21
3. Plugins: Lombok (kötelező!)
4. Settings → Build → Compiler → Annotation Processors → Enable

### VS Code (frontend)

Ajánlott extensionök:
- Angular Language Service
- ESLint
- Prettier
- Tailwind CSS IntelliSense

## Tesztelés

Backend:
```bash
cd StudentSpaceAPI
./mvnw test
```

Frontend:
```bash
cd StudentSpace
npm test
```

## API tesztelés

Regisztráció:
```bash
curl -X POST http://localhost:8080/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username":"test","email":"test@test.com","password":"test123"}'
```

Visszaad egy tokent amit használhatsz a többi végponthoz.

## Gyakori problémák

**Port foglalt (8080)**
```bash
lsof -i :8080
kill -9 <PID>
```

**Connection refused (PostgreSQL)**

Ellenőrizd hogy fut-e a PostgreSQL:
```bash
# macOS
brew services list

# Linux
sudo systemctl status postgresql
```

**Lombok hibák IntelliJ-ben**

1. Telepítsd a Lombok plugint
2. Engedélyezd az annotation processing-et
3. Invalidate Caches / Restart

**node_modules problémák**
```bash
rm -rf node_modules package-lock.json
npm install
```

## Git workflow

Feature branch-ekkel dolgozom:
```bash
git checkout -b feature/uj-dolog
# ... fejlesztés ...
git commit -m "feat: új dolog hozzáadása"
git push origin feature/uj-dolog
```

Commit üzenet formátum:
- `feat:` - új feature
- `fix:` - hibajavítás
- `docs:` - dokumentáció
- `refactor:` - refaktorálás
