# Jour 13 : Edge Cases & Error Handling

## 🤖 Règles d'Interaction avec l'IA

**Pour ce module, l'IA est autorisée à :**

- ✅ **Répondre à tes questions conceptuelles** : "Qu'est-ce qu'un edge case ?", "Comment gérer les erreurs réseau ?"
- ✅ **Guider ta réflexion** : "Quels edge cases considérer pour cette feature ?", "Comment protéger contre cette erreur ?"
- ✅ **Expliquer les patterns** : "Comment utiliser AbortController ?", "Comment implémenter le retry logic ?"
- ✅ **Suggérer des améliorations** : "Quelles autres protections ajouter ?", "Comment améliorer cette gestion d'erreur ?"

**L'IA n'est PAS autorisée à :**

- ❌ **Lister tous les edge cases** pour toi (tu dois les identifier toi-même)
- ❌ **Implémenter directement** les protections (tu dois coder)
- ❌ **Donner des solutions complètes** sans que tu aies réfléchi

**Si tu es vraiment bloqué (>20min) :**
- L'IA peut te donner des hints sur quels edge cases considérer ou comment implémenter une protection spécifique.

---

## 🎯 Objectifs du Module

À la fin de cette journée, tu seras capable de :

- Identifier et gérer tous les edge cases
- Implémenter des protections robustes
- Gérer les erreurs de manière élégante
- Observer les patterns de code review professionnels

---

## 📋 Toute la Journée (5h) - Exercice "Break Everything"

**📚 Documentation utile :**
- [Error Handling Best Practices](https://www.toptal.com/javascript/error-handling-javascript) - Bonnes pratiques
- [AbortController](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) - Annulation de requêtes
- [Debouncing](https://davidwalsh.name/javascript-debounce-function) - Debouncing en JavaScript

### 1. Backend Edge Cases

**À tester systématiquement :**

#### Données manquantes

```typescript
// Test: POST /patients sans body
// Attendu: 400 Bad Request

// Test: POST /patients avec body vide {}
// Attendu: 400 avec erreurs de validation

// Protection:
if (!req.body || Object.keys(req.body).length === 0) {
  return res.status(400).json({ error: "Request body is required" });
}
```

#### Types de données incorrects

```typescript
// Test: POST /patients avec name = 123 (number au lieu de string)
// Attendu: 400 avec erreur de validation

// Protection:
const validatePatient = [
  body("name").isString().withMessage("Name must be a string"),
  body("email").isEmail().withMessage("Email must be valid"),
];
```

#### IDs inexistants

```typescript
// Test: GET /patients/99999 (ID qui n'existe pas)
// Attendu: 404 Not Found

// Protection:
const patient = await repository.findById(id);
if (!patient) {
  return res.status(404).json({ error: "Patient not found" });
}
```

#### Doublons

```typescript
// Test: POST /patients avec email déjà existant
// Attendu: 409 Conflict

// Protection:
try {
  await repository.create(patient);
} catch (error: any) {
  if (error.code === "23505") {
    // Unique violation
    return res.status(409).json({ error: "Email already exists" });
  }
  throw error;
}
```

#### Strings vides, null, undefined

```typescript
// Test: POST /patients avec name = ""
// Attendu: 400 avec erreur de validation

// Protection:
body("name").trim().notEmpty().withMessage("Name cannot be empty"),

// Test: POST /patients avec name = null
// Protection: express-validator gère ça automatiquement
```

#### Requêtes malformées

```typescript
// Test: GET /patients/abc (ID non numérique)
// Attendu: 400 Bad Request

// Protection:
const id = parseInt(req.params.id);
if (isNaN(id)) {
  return res.status(400).json({ error: "Invalid patient ID" });
}
```

#### Timeouts de DB / Connexion perdue

```typescript
// Protection: Timeout sur les queries
const result = await pool.query({
  text: "SELECT * FROM patients",
  rowMode: "array",
}).timeout(5000); // 5 secondes max

// Ou avec pg directement:
pool.query("SELECT * FROM patients")
  .timeout(5000)
  .then(result => { /* ... */ })
  .catch(error => {
    if (error.name === "TimeoutError") {
      return res.status(504).json({ error: "Database timeout" });
    }
    throw error;
  });
```

**Checklist de tests backend :**

- [ ] Données manquantes → 400
- [ ] Types incorrects → 400
- [ ] IDs inexistants → 404
- [ ] Doublons → 409
- [ ] Strings vides → 400
- [ ] Null/undefined → 400
- [ ] Requêtes malformées → 400
- [ ] Timeouts DB → 504
- [ ] Connexion DB perdue → 503

**Besoin d'aide ?** Demande à l'IA : "Comment gérer un timeout PostgreSQL ?" ou "Comment valider qu'un ID est numérique ?"

### 2. Frontend Edge Cases

**À tester systématiquement :**

#### API lente/en erreur

```typescript
// Protection: Timeout sur les requêtes
const controller = new AbortController();
const timeoutId = setTimeout(() => controller.abort(), 10000); // 10s

try {
  const response = await fetch("/api/patients", {
    signal: controller.signal,
  });
  clearTimeout(timeoutId);
} catch (error: any) {
  if (error.name === "AbortError") {
    setError("Request timeout. Please try again.");
  } else {
    setError("Failed to load patients.");
  }
}
```

#### Clics multiples rapides (double submit)

```typescript
// Protection: Désactiver le bouton pendant la soumission
const [isSubmitting, setIsSubmitting] = useState(false);

const handleSubmit = async (e: React.FormEvent) => {
  e.preventDefault();
  if (isSubmitting) return; // Déjà en cours
  
  setIsSubmitting(true);
  try {
    await submitForm();
  } finally {
    setIsSubmitting(false);
  }
};

<button type="submit" disabled={isSubmitting}>
  {isSubmitting ? "Submitting..." : "Submit"}
</button>
```

#### Formulaires avec données invalides

```typescript
// Protection: Validation avant soumission
const [errors, setErrors] = useState<Record<string, string>>({});

const validate = (): boolean => {
  const newErrors: Record<string, string> = {};
  
  if (!formData.name.trim()) {
    newErrors.name = "Name is required";
  }
  
  if (!formData.email.includes("@")) {
    newErrors.email = "Invalid email format";
  }
  
  setErrors(newErrors);
  return Object.keys(newErrors).length === 0;
};

const handleSubmit = (e: React.FormEvent) => {
  e.preventDefault();
  if (!validate()) return;
  // Soumettre...
};
```

#### Navigation rapide (unmount pendant fetch)

```typescript
// Protection: AbortController dans useEffect
useEffect(() => {
  const controller = new AbortController();
  
  const fetchData = async () => {
    try {
      const data = await fetch("/api/patients", {
        signal: controller.signal,
      });
      if (!controller.signal.aborted) {
        setPatients(data);
      }
    } catch (error: any) {
      if (error.name !== "AbortError") {
        setError("Failed to load");
      }
    }
  };
  
  fetchData();
  
  return () => {
    controller.abort();
  };
}, []);
```

#### Connexion internet perdue

```typescript
// Protection: Détecter la perte de connexion
useEffect(() => {
  const handleOnline = () => {
    setError(null);
    fetchData(); // Réessayer
  };
  
  const handleOffline = () => {
    setError("No internet connection");
  };
  
  window.addEventListener("online", handleOnline);
  window.addEventListener("offline", handleOffline);
  
  return () => {
    window.removeEventListener("online", handleOnline);
    window.removeEventListener("offline", handleOffline);
  };
}, []);
```

#### Timeout de requête

```typescript
// Protection: Timeout avec AbortController
const fetchWithTimeout = async (url: string, timeout = 10000) => {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), timeout);
  
  try {
    const response = await fetch(url, { signal: controller.signal });
    clearTimeout(timeoutId);
    return response;
  } catch (error: any) {
    clearTimeout(timeoutId);
    if (error.name === "AbortError") {
      throw new Error("Request timeout");
    }
    throw error;
  }
};
```

#### Réponses inattendues du backend

```typescript
// Protection: Valider la structure de la réponse
const fetchPatients = async () => {
  try {
    const response = await fetch("/api/patients");
    const data = await response.json();
    
    // Valider la structure
    if (!Array.isArray(data.patients)) {
      throw new Error("Invalid response format");
    }
    
    setPatients(data.patients);
  } catch (error) {
    setError("Invalid data received from server");
  }
};
```

**Checklist de tests frontend :**

- [ ] API lente → Loading state + timeout
- [ ] API erreur → Error message claire
- [ ] Double submit → Bouton désactivé
- [ ] Données invalides → Validation + erreurs
- [ ] Navigation rapide → Pas de state update après unmount
- [ ] Pas d'internet → Message d'erreur
- [ ] Timeout → Message + retry option
- [ ] Réponse inattendue → Validation + fallback

**Besoin d'aide ?** Demande à l'IA : "Comment utiliser AbortController dans React ?" ou "Comment détecter la perte de connexion ?"

### 3. Patterns de Protection

**Backend :**

```typescript
// Validation stricte
const validateId = (id: string): number => {
  const numId = parseInt(id);
  if (isNaN(numId) || numId <= 0) {
    throw new AppError(400, "Invalid ID format");
  }
  return numId;
};

// Gestion des erreurs DB
try {
  await repository.create(data);
} catch (error: any) {
  if (error.code === "23505") {
    throw new AppError(409, "Duplicate entry");
  }
  if (error.code === "23503") {
    throw new AppError(400, "Foreign key violation");
  }
  throw error;
}

// Timeout protection
const queryWithTimeout = async (query: string, params: any[], timeout = 5000) => {
  return Promise.race([
    pool.query(query, params),
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error("Query timeout")), timeout)
    ),
  ]);
};
```

**Frontend :**

```typescript
// AbortController pour annuler les requêtes
useEffect(() => {
  const controller = new AbortController();
  
  fetchData({ signal: controller.signal });
  
  return () => controller.abort();
}, []);

// Debouncing pour la recherche
const useDebounce = (value: string, delay: number) => {
  const [debouncedValue, setDebouncedValue] = useState(value);
  
  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);
    
    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);
  
  return debouncedValue;
};

// Retry logic
const fetchWithRetry = async (url: string, retries = 3) => {
  for (let i = 0; i < retries; i++) {
    try {
      const response = await fetch(url);
      if (response.ok) return response;
    } catch (error) {
      if (i === retries - 1) throw error;
      await new Promise((resolve) => setTimeout(resolve, 1000 * (i + 1)));
    }
  }
};
```

---

## 📋 Soir (2h) - Code Review Professionnel

**📚 Documentation utile :**
- [GitHub Pull Requests](https://github.com/pulls) - Explorer les PRs
- [Code Review Best Practices](https://github.com/google/eng-practices/blob/master/review/README.md) - Google's guide
- [React PRs](https://github.com/facebook/react/pulls) - PRs React

### Lire des vraies PRs sur GitHub

**Repos à explorer :**

1. **React** (facebook/react)
   - Regarder comment ils structurent les composants
   - Comment ils gèrent les edge cases
   - Comment ils écrivent les tests

2. **Express** (expressjs/express)
   - Comment ils structurent les middlewares
   - Comment ils gèrent les erreurs
   - Comment ils documentent

3. **Projets open-source populaires**
   - Next.js, Vue.js, etc.
   - Regarder les patterns récurrents

**Observer :**

- **Structure du code** : Comment organisent-ils les fichiers ?
- **Naming conventions** : Comment nomment-ils les variables/fonctions ?
- **Error handling** : Comment gèrent-ils les erreurs ?
- **Tests** : Comment écrivent-ils les tests ?
- **Documentation** : Comment documentent-ils le code ?
- **Commits** : Comment structurent-ils les commits ?

**Prendre des notes :**

```markdown
# Patterns Observés dans les PRs

## Structure
- [Pattern observé]
- [Pattern observé]

## Error Handling
- [Pattern observé]
- [Pattern observé]

## Tests
- [Pattern observé]
- [Pattern observé]

## À Appliquer
- [Chose à appliquer dans mon code]
- [Chose à appliquer dans mon code]
```

**Questions à te poser :**

- Pourquoi ont-ils fait ce choix ?
- Comment aurais-je fait différemment ?
- Qu'est-ce que je peux apprendre de cette approche ?

**Besoin d'aide ?** Demande à l'IA : "Comment lire efficacement une PR GitHub ?" ou "Quels patterns chercher dans les code reviews ?"

---

## ✅ Checklist de Validation

À la fin du Jour 13, vérifie que tu as :

- [ ] Testé tous les edge cases backend
- [ ] Testé tous les edge cases frontend
- [ ] Implémenté des protections robustes
- [ ] Lu et analysé au moins 3 PRs GitHub
- [ ] Pris des notes sur les patterns observés
- [ ] Créé une liste d'améliorations pour ton code

---

## 🎯 Points Clés à Retenir

1. **Edge cases = Bugs en production** : Les gérer maintenant évite les problèmes plus tard
2. **Validation = Sécurité** : Toujours valider côté backend
3. **UX = Gestion d'erreurs** : Messages clairs, états de loading
4. **Code review = Apprentissage** : Observer les pros améliore ton code
5. **Protection = Robustesse** : Plus de protections = code plus robuste

---

## 🚀 Prochaines Étapes

Jour 14 : Dernière simulation et préparation mentale pour l'entretien réel. Tu es presque prêt !
