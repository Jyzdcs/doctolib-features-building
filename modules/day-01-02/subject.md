# Jour 1-2 : Fondamentaux Node.js/Express + PostgreSQL

## 🤖 Règles d'Interaction avec l'IA

**Pour ce module, l'IA est autorisée à :**

- ✅ **Répondre à tes questions conceptuelles** : "Comment fonctionne Express routing ?", "Pourquoi utiliser parameterized queries ?"
- ✅ **Guider ta réflexion** : "Par où commencer ?", "Quelle approche pour la pagination ?"
- ✅ **Expliquer les erreurs** : "Pourquoi j'ai cette erreur PostgreSQL ?", "Comment débugger ce problème ?"
- ✅ **Suggérer des améliorations** : "Comment améliorer cette validation ?", "Quels edge cases considérer ?"

**L'IA n'est PAS autorisée à :**

- ❌ **Écrire le code complet** pour toi (pas de copier-coller de routes complètes)
- ❌ **Résoudre directement** les exercices pratiques (tu dois coder toi-même)
- ❌ **Donner des solutions toutes faites** sans que tu aies essayé d'abord

**Si tu es vraiment bloqué (>20min) :**

- L'IA peut te donner des hints ou un exemple minimal, mais tu dois comprendre et adapter toi-même.

---

## 🎯 Objectifs du Module

À la fin de ces 2 jours, tu seras capable de :

- Créer un serveur Express from scratch sans tutoriel
- Implémenter des routes REST complètes (CRUD)
- Connecter et interagir avec PostgreSQL via SQL brut
- Valider les données entrantes
- Gérer les erreurs proprement
- Tester ton API manuellement

---

## 📋 Matin (3h) - Setup & Routes REST

### 1. Créer un serveur Express from scratch

**Sans regarder de tutoriel**, crée un nouveau projet :

```bash
mkdir doctolib-practice
cd doctolib-practice
npm init -y
npm install express pg dotenv
npm install -D @types/express @types/node @types/pg nodemon typescript
```

**Structure de base à créer :**

```
doctolib-practice/
├── src/
│   ├── server.ts          # Point d'entrée Express
│   ├── config/
│   │   └── database.ts    # Configuration PostgreSQL
│   ├── routes/
│   │   └── patients.ts    # Routes patients
│   └── types/
│       └── patient.ts     # Types TypeScript
├── .env                   # Variables d'environnement
├── package.json
└── tsconfig.json
```

**Exercice mental :**

- Comment démarres-tu un serveur Express ?
- Quelle est la différence entre `app.listen()` et `app.get()` ?
- Pourquoi utiliser `dotenv` pour les variables d'environnement ?

### 2. Implémenter 5 routes REST

**📚 Documentation utile :**

- [Express Routing Guide](https://expressjs.com/en/guide/routing.html) - Comment définir les routes
- [Express Request/Response](https://expressjs.com/en/api.html#req) - Objets `req` et `res`
- [HTTP Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) - Codes à utiliser

**Exercice pratique : API de gestion de patients**

Créer les endpoints suivants :

1. **POST /patients** - Créer un patient

   - Body: `{ name: string, dateOfBirth: string, email: string }`
   - Retourne: `201 Created` + patient créé

2. **GET /patients/:id** - Récupérer un patient

   - Retourne: `200 OK` + patient ou `404 Not Found`

3. **GET /patients** - Liste des patients

   - Query params: `?page=1&limit=10` (pagination)
   - Retourne: `200 OK` + `{ patients: [], total: number, page: number }`

4. **PUT /patients/:id** - Mettre à jour un patient

   - Body: `{ name?: string, email?: string }` (partiel)
   - Retourne: `200 OK` + patient mis à jour ou `404 Not Found`

5. **DELETE /patients/:id** - Supprimer un patient
   - Retourne: `204 No Content` ou `404 Not Found`

**Questions à te poser :**

- Quel verbe HTTP pour chaque action ?
- Quels status codes utiliser ?
- Comment gérer les IDs invalides ?
- Comment structurer les réponses JSON ?

**Besoin d'aide ?** Demande à l'IA : "Comment structurer une route Express avec paramètres ?" ou "Quel status code pour une création réussie ?"

### 3. Connecter PostgreSQL avec `pg` library

**📚 Documentation utile :**

- [node-postgres Documentation](https://node-postgres.com/) - Guide complet de la librairie `pg`
- [PostgreSQL Tutorial](https://www.postgresql.org/docs/current/tutorial.html) - Bases de PostgreSQL
- [PostgreSQL Data Types](https://www.postgresql.org/docs/current/datatype.html) - Types de données disponibles

**Setup de la base de données :**

```sql
-- Créer la table patients
CREATE TABLE patients (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  date_of_birth DATE NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Index pour les recherches fréquentes
CREATE INDEX idx_patients_email ON patients(email);
CREATE INDEX idx_patients_created_at ON patients(created_at);
```

**Connexion PostgreSQL dans Node.js :**

```typescript
// src/config/database.ts
import { Pool } from "pg";
import dotenv from "dotenv";

dotenv.config();

export const pool = new Pool({
  host: process.env.DB_HOST || "localhost",
  port: parseInt(process.env.DB_PORT || "5432"),
  database: process.env.DB_NAME || "doctolib",
  user: process.env.DB_USER || "postgres",
  password: process.env.DB_PASSWORD || "postgres",
});
```

### 4. Écrire des queries SQL brutes (pas d'ORM)

**Pattern à suivre :**

```typescript
// Exemple : GET /patients/:id
const result = await pool.query(
  "SELECT id, name, date_of_birth, email, created_at FROM patients WHERE id = $1",
  [patientId]
);

if (result.rows.length === 0) {
  return res.status(404).json({ error: "Patient not found" });
}

res.json(result.rows[0]);
```

**Pourquoi SQL brut ?**

- Contrôle total sur les queries
- Performance optimale
- Compréhension profonde de PostgreSQL
- Pas de "magie" cachée

**Concepts à maîtriser :**

- Parameterized queries (`$1`, `$2`) pour éviter SQL injection
- `result.rows` pour récupérer les données
- Gestion des transactions si nécessaire

**Besoin d'aide ?** Demande à l'IA : "Comment utiliser `pool.query()` avec des paramètres ?" ou "Comment gérer les erreurs PostgreSQL dans Node.js ?"

---

## 📋 Après-midi (2h) - Validation & Error Handling

### 1. Implémenter validation des données (express-validator)

**📚 Documentation utile :**

- [express-validator Documentation](https://express-validator.github.io/docs/) - Guide complet de validation
- [express-validator API Reference](https://express-validator.github.io/docs/api/validation-chain/) - Toutes les méthodes disponibles
- [Express Middleware Guide](https://expressjs.com/en/guide/using-middleware.html) - Comprendre les middlewares

**Pourquoi valider ?**

- Sécurité : éviter les données malformées
- Cohérence : garantir la structure attendue
- UX : erreurs claires pour l'utilisateur

**Exemple de validation :**

```typescript
import { body, validationResult } from "express-validator";

// Middleware de validation
const validatePatient = [
  body("name")
    .trim()
    .isLength({ min: 2, max: 255 })
    .withMessage("Name must be between 2 and 255 characters"),
  body("dateOfBirth")
    .isISO8601()
    .withMessage("Date must be in ISO format (YYYY-MM-DD)"),
  body("email").isEmail().normalizeEmail().withMessage("Must be a valid email"),
];

// Utilisation dans la route
app.post("/patients", validatePatient, async (req, res) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({ errors: errors.array() });
  }
  // ... logique de création
});
```

**Exercice :**

- Valide tous les endpoints (POST, PUT)
- Messages d'erreur clairs et actionnables
- Teste avec des données invalides

**Besoin d'aide ?** Demande à l'IA : "Comment valider un email avec express-validator ?" ou "Comment formater les messages d'erreur de validation ?"

### 2. Gestion d'erreurs propre (try/catch, error middleware)

**📚 Documentation utile :**

- [Express Error Handling](https://expressjs.com/en/guide/error-handling.html) - Guide officiel de gestion d'erreurs
- [Express Middleware Guide](https://expressjs.com/en/guide/using-middleware.html) - Section sur les error-handling middlewares
- [PostgreSQL Error Codes](https://www.postgresql.org/docs/current/errcodes-appendix.html) - Codes d'erreur PostgreSQL (ex: `23505` pour unique violation)

**Pattern recommandé :**

```typescript
// Middleware d'erreur centralisé
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  console.error("Error:", err);

  // Erreur de validation
  if (err.name === "ValidationError") {
    return res.status(400).json({ error: err.message });
  }

  // Erreur de base de données
  if (err.code === "23505") {
    // Unique violation
    return res.status(409).json({ error: "Email already exists" });
  }

  // Erreur générique
  res.status(500).json({ error: "Internal server error" });
});

// Utilisation dans les routes
app.get("/patients/:id", async (req, res, next) => {
  try {
    const result = await pool.query("SELECT ...", [req.params.id]);
    // ...
  } catch (error) {
    next(error); // Passe l'erreur au middleware
  }
});
```

**Status codes à connaître :**

- `200 OK` - Succès GET/PUT
- `201 Created` - Succès POST
- `204 No Content` - Succès DELETE
- `400 Bad Request` - Données invalides
- `404 Not Found` - Ressource inexistante
- `409 Conflict` - Conflit (ex: email déjà utilisé)
- `500 Internal Server Error` - Erreur serveur

**Besoin d'aide ?** Demande à l'IA : "Comment créer un middleware d'erreur Express ?" ou "Comment gérer les erreurs PostgreSQL dans Express ?"

### 3. Tester avec Postman/Thunder Client MANUELLEMENT

**Checklist de tests :**

- [ ] POST /patients avec données valides → 201
- [ ] POST /patients avec email existant → 409
- [ ] POST /patients avec données invalides → 400
- [ ] GET /patients/:id avec ID valide → 200
- [ ] GET /patients/:id avec ID invalide → 404
- [ ] GET /patients avec pagination → 200
- [ ] PUT /patients/:id avec données valides → 200
- [ ] PUT /patients/:id avec ID inexistant → 404
- [ ] DELETE /patients/:id → 204
- [ ] DELETE /patients/:id inexistant → 404

**Pourquoi tester manuellement ?**

- Comprendre le flux complet
- Voir les vraies réponses HTTP
- Débugger plus facilement
- Développer l'intuition

### 4. Documenter ton API (README simple)

**Template de README :**

````markdown
# Patients API

## Endpoints

### POST /patients

Créer un nouveau patient.

**Body:**

```json
{
  "name": "John Doe",
  "dateOfBirth": "1990-01-15",
  "email": "john@example.com"
}
```
````

**Response:** `201 Created`

### GET /patients/:id

Récupérer un patient par ID.

**Response:** `200 OK`

```json
{
  "id": 1,
  "name": "John Doe",
  "dateOfBirth": "1990-01-15",
  "email": "john@example.com",
  "createdAt": "2024-01-01T00:00:00Z"
}
```

[... autres endpoints ...]

---

## 📋 Soir (1h) - Documentation & Réflexion

### Réflexion sur ce que tu as appris

**Prends des notes sur :**

- Patterns que tu ne connaissais pas
- Différences entre `app.use()` et `app.get()`
- Comment fonctionnent les middlewares
- Gestion d'erreurs avec `next()`
- Ce qui t'a posé le plus de difficultés
- Ce que tu veux approfondir demain

**Ressources complémentaires (optionnel) :**

- [Express Best Practices](https://expressjs.com/en/advanced/best-practice-performance.html) - Bonnes pratiques Express
- [PostgreSQL Performance Tips](https://www.postgresql.org/docs/current/performance-tips.html) - Optimisation PostgreSQL

---

## ✅ Checklist de Validation

À la fin du Jour 1-2, vérifie que tu peux :

- [ ] Créer un serveur Express from scratch sans aide
- [ ] Implémenter les 5 routes REST (CRUD complet)
- [ ] Connecter PostgreSQL et exécuter des queries
- [ ] Valider les données entrantes
- [ ] Gérer les erreurs proprement
- [ ] Tester ton API avec Postman
- [ ] Documenter ton API

---

## 🎯 Points Clés à Retenir

1. **Express = HTTP layer** : Routes, middlewares, error handling
2. **PostgreSQL = Data layer** : SQL brut, parameterized queries, indexes
3. **Validation = Security** : Toujours valider les inputs
4. **Error handling = UX** : Messages clairs, status codes appropriés
5. **Testing = Confiance** : Teste manuellement avant d'automatiser

---

## 🚀 Prochaines Étapes

Le Jour 3-4, tu connecteras ce backend à un frontend React. Garde ce projet propre et bien structuré !
