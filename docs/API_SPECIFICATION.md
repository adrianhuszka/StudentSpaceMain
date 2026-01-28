# API dokumentáció

Az API a `http://localhost:8080/api/v1` base URL alatt érhető el. JSON formátumot használ, JWT token alapú autentikációval.

**Swagger UI:** http://localhost:8080/swagger-ui.html - interaktív API dokumentáció és tesztelés

## Autentikáció

A legtöbb végpont védett. A tokent az Authorization headerben kell küldeni:

```
Authorization: Bearer <access_token>
```

Az access token 15 percig érvényes, a refresh token 7 napig.

## Auth végpontok

Base: `/api/v1/auth`

### POST /auth/login

Bejelentkezés.

Request:
```json
{
  "username": "felhasznalo",
  "password": "jelszo"
}
```

Response:
```json
{
  "accessToken": "eyJhbG...",
  "refreshToken": "eyJhbG...",
  "expiresIn": 900000
}
```

### POST /auth/register

Új felhasználó regisztrálása.

Request:
```json
{
  "username": "uj_felhasznalo",
  "email": "email@example.com",
  "password": "jelszo123"
}
```

Ugyanazt a token response-t adja vissza mint a login.

### POST /auth/refresh

Token frissítése.

```json
{
  "refreshToken": "eyJhbG..."
}
```

## Users

Base: `/api/v1/users`

| Végpont | Metódus | Leírás |
|---------|---------|--------|
| `/users` | GET | összes felhasználó |
| `/users/:id` | GET | egy felhasználó |
| `/users/me` | GET | aktuális user |
| `/users` | POST | új user |
| `/users` | PUT | módosítás |
| `/users` | DELETE | törlés |

A `/users/me` a bejelentkezett felhasználó adatait adja vissza a token alapján.

## Professions (Szakmák)

Base: `/api/v1/professions`

| Végpont | Metódus | Leírás |
|---------|---------|--------|
| `/professions` | GET | összes szakma |
| `/professions/{id}` | GET | egy szakma |
| `/professions` | POST | létrehozás |
| `/professions` | PUT | módosítás |
| `/professions/{id}` | DELETE | törlés |

GET response példa:
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "name": "Informatika",
  "description": "Informatikai szakma",
  "createdAt": "2025-01-15T10:30:00"
}
```

## Subjects (Tantárgyak)

Base: `/api/v1/subjects`

A standard CRUD műveletek mellett van pár speciális végpont:

**GET /subjects/by-profession/{professionId}** - tantárgyak egy adott szakmához

**PUT /subjects/link-subject-to-profession?subjectId=...&professionId=...** - tantárgy hozzárendelése szakmához

**PUT /subjects/unlink-subject-from-profession** - leválasztás

## Modules (Modulok)

Base: `/api/v1/modules`

A modulok kezelése kicsit bonyolultabb a fájlfeltöltés miatt. Kétféle content-type-ot fogad:

**application/json** - sima JSON payload, PDF nélkül

**multipart/form-data** - fájlfeltöltéssel együtt

Multipart mezők:
- title (string)
- content (string, opcionális)
- moduleType (string: LESSON, EXERCISE, PDF)
- subjectId (string)
- pdfFile (file, opcionális)

**GET /modules/{id}/pdf** - PDF letöltése. Visszaad egy `application/pdf` response-t megfelelő Content-Disposition headerrel.

## Exercises (Feladatok)

Base: `/api/v1/exercises`

| Végpont | Metódus | Leírás |
|---------|---------|--------|
| `/exercises` | GET | összes |
| `/exercises/{id}` | GET | egy feladat |
| `/exercises/by-module/{moduleId}` | GET | modulhoz tartozók |
| `/exercises` | POST | létrehozás |
| `/exercises` | PUT | módosítás |
| `/exercises?id={id}` | DELETE | törlés |

Request példa:
```json
{
  "moduleId": "...",
  "type": "MULTIPLE_CHOICE",
  "question": "Mi a Java?",
  "answer": "{\"options\": [\"Nyelv\", \"Kávé\"], \"correct\": 0}"
}
```

Az answer mező JSON string, bármilyen struktúrát tartalmazhat a feladat típusától függően.

## Forum

Base: `/api/v1/forum`

Standard CRUD. A fórumok automatikusan jönnek létre a tantárgyakkal együtt.

GET response tartalmazza a forumMessages tömböt is (eager fetch).

## Forum Messages

Base: `/api/v1/forum-messages`

| Végpont | Metódus |
|---------|---------|
| `/forum-messages` | GET |
| `/forum-messages/{id}` | GET |
| `/forum-messages` | POST |
| `/forum-messages` | PUT |
| `/forum-messages/{id}` | DELETE |

Új üzenet:
```json
{
  "message": "Üzenet szövege",
  "forumId": "..."
}
```

A szerző automatikusan a bejelentkezett user lesz.

## Hibakódok

- 200 - OK
- 201 - létrehozva
- 204 - törlés sikeres (no content)
- 400 - hibás request
- 401 - nincs token vagy lejárt
- 403 - nincs jogosultság
- 404 - nem található
- 500 - szerver hiba

## Fájl limitek

- Max fájl méret: 10 MB
- Max request méret: 10 MB

Konfigurálva az application.yaml-ben.
