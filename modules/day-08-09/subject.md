# Jour 8-9 : Tests & Debugging

## 🤖 Règles d'Interaction avec l'IA

**Pour ce module, l'IA est autorisée à :**

- ✅ **Répondre à tes questions conceptuelles** : "Comment fonctionne Jest ?", "Quelle est la différence entre unit et integration tests ?"
- ✅ **Guider ta réflexion** : "Comment structurer mes tests ?", "Quels cas de test couvrir ?"
- ✅ **Expliquer les erreurs de tests** : "Pourquoi ce test échoue ?", "Comment mock cette dépendance ?"
- ✅ **Suggérer des améliorations** : "Comment améliorer cette assertion ?", "Quels edge cases tester ?"

**L'IA n'est PAS autorisée à :**

- ❌ **Écrire les tests complets** pour toi (tu dois les écrire toi-même)
- ❌ **Résoudre directement** les bugs que tu introduis volontairement (tu dois débugger)
- ❌ **Donner les solutions** des exercices de debugging (hints seulement)

**Si tu es vraiment bloqué (>30min) :**
- L'IA peut te donner des hints sur comment débugger ou comment structurer un test, mais tu dois trouver la solution toi-même.

---

## 🎯 Objectifs du Module

À la fin de ces 2 jours, tu seras capable de :

- Écrire des tests unitaires avec Jest
- Tester les routes API avec Supertest
- Tester les composants React avec React Testing Library
- Débugger efficacement sans AI
- Créer une checklist de debugging personnelle

---

## 📋 Matin (2h) - Tests Backend

**📚 Documentation utile :**
- [Jest Documentation](https://jestjs.io/docs/getting-started) - Guide complet Jest
- [Supertest Documentation](https://github.com/visionmedia/supertest) - Tester les routes Express
- [Testing Best Practices](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library) - Bonnes pratiques

### 1. Apprendre Jest en profondeur

**Concepts fondamentaux :**

```typescript
// Structure de base
describe("Groupe de tests", () => {
  beforeEach(() => {
    // Setup avant chaque test
  });

  afterEach(() => {
    // Cleanup après chaque test
  });

  it("should do something", () => {
    // Arrange
    const input = "test";

    // Act
    const result = myFunction(input);

    // Assert
    expect(result).toBe("expected");
  });
});
```

**Matchers Jest essentiels :**

- `expect(value).toBe(expected)` - Égalité stricte (===)
- `expect(value).toEqual(expected)` - Égalité profonde (objets/arrays)
- `expect(value).toBeTruthy()` - Vérifie que c'est truthy
- `expect(value).toContain(item)` - Vérifie qu'un array contient un item
- `expect(value).toThrow()` - Vérifie qu'une fonction throw
- `expect(value).resolves.toBe()` - Pour les Promises

**Mocks et Stubs :**

```typescript
// Mock une fonction
jest.fn();

// Mock un module
jest.mock("../services/patientService");

// Mock une valeur de retour
const mockFn = jest.fn().mockReturnValue("mocked value");

// Mock une Promise
const mockFn = jest.fn().mockResolvedValue({ id: 1 });
```

**Besoin d'aide ?** Demande à l'IA : "Comment utiliser jest.fn() ?" ou "Comment mock un module dans Jest ?"

### 2. Tests unitaires pour ton API Node.js

**Structure recommandée :**

```
src/
├── services/
│   └── patientsService.ts
├── repositories/
│   └── patientsRepository.ts
└── __tests__/
    ├── services/
    │   └── patientsService.test.ts
    └── repositories/
        └── patientsRepository.test.ts
```

**Exemple : Test d'un service**

```typescript
// src/__tests__/services/patientsService.test.ts
import { PatientsService } from "../../services/patientsService";
import { PatientsRepository } from "../../repositories/patientsRepository";

jest.mock("../../repositories/patientsRepository");

describe("PatientsService", () => {
  let service: PatientsService;
  let mockRepository: jest.Mocked<PatientsRepository>;

  beforeEach(() => {
    mockRepository = {
      findById: jest.fn(),
      create: jest.fn(),
      // ... autres méthodes
    } as any;

    service = new PatientsService(mockRepository);
  });

  describe("getById", () => {
    it("should return a patient when found", async () => {
      // Arrange
      const patientId = 1;
      const mockPatient = { id: 1, name: "John", email: "john@example.com" };
      mockRepository.findById.mockResolvedValue(mockPatient);

      // Act
      const result = await service.getById(patientId);

      // Assert
      expect(result).toEqual(mockPatient);
      expect(mockRepository.findById).toHaveBeenCalledWith(patientId);
    });

    it("should throw error when patient not found", async () => {
      // Arrange
      mockRepository.findById.mockResolvedValue(null);

      // Act & Assert
      await expect(service.getById(999)).rejects.toThrow("Patient not found");
    });
  });
});
```

**Exemple : Test d'un repository**

```typescript
// src/__tests__/repositories/patientsRepository.test.ts
import { Pool } from "pg";
import { PatientsRepository } from "../../repositories/patientsRepository";

// Mock pg
jest.mock("pg");

describe("PatientsRepository", () => {
  let repository: PatientsRepository;
  let mockPool: jest.Mocked<Pool>;

  beforeEach(() => {
    mockPool = {
      query: jest.fn(),
    } as any;

    (Pool as jest.Mock).mockImplementation(() => mockPool);
    repository = new PatientsRepository(mockPool);
  });

  it("should find patient by id", async () => {
    // Arrange
    const mockResult = {
      rows: [{ id: 1, name: "John", email: "john@example.com" }],
    };
    mockPool.query.mockResolvedValue(mockResult as any);

    // Act
    const result = await repository.findById(1);

    // Assert
    expect(result).toEqual(mockResult.rows[0]);
    expect(mockPool.query).toHaveBeenCalledWith(
      expect.stringContaining("SELECT"),
      [1]
    );
  });
});
```

**Besoin d'aide ?** Demande à l'IA : "Comment tester un service qui dépend d'un repository ?" ou "Comment mock PostgreSQL dans Jest ?"

### 3. Utiliser Supertest pour tester les routes

**Setup Supertest :**

```bash
npm install -D supertest @types/supertest
```

**Exemple : Test d'une route**

```typescript
// src/__tests__/routes/patients.test.ts
import request from "supertest";
import express from "express";
import patientsRouter from "../../routes/patients";
import { pool } from "../../config/database";

jest.mock("../../config/database");

const app = express();
app.use(express.json());
app.use("/api/patients", patientsRouter);

describe("GET /api/patients/:id", () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  it("should return 200 with patient data", async () => {
    // Arrange
    const mockPatient = { id: 1, name: "John", email: "john@example.com" };
    (pool.query as jest.Mock).mockResolvedValue({
      rows: [mockPatient],
    });

    // Act
    const response = await request(app).get("/api/patients/1");

    // Assert
    expect(response.status).toBe(200);
    expect(response.body).toEqual(mockPatient);
  });

  it("should return 404 when patient not found", async () => {
    // Arrange
    (pool.query as jest.Mock).mockResolvedValue({ rows: [] });

    // Act
    const response = await request(app).get("/api/patients/999");

    // Assert
    expect(response.status).toBe(404);
    expect(response.body).toHaveProperty("error");
  });
});

describe("POST /api/patients", () => {
  it("should return 201 with created patient", async () => {
    // Arrange
    const newPatient = { name: "Jane", dateOfBirth: "1990-01-01", email: "jane@example.com" };
    const createdPatient = { id: 1, ...newPatient };
    (pool.query as jest.Mock).mockResolvedValue({
      rows: [createdPatient],
    });

    // Act
    const response = await request(app)
      .post("/api/patients")
      .send(newPatient);

    // Assert
    expect(response.status).toBe(201);
    expect(response.body).toHaveProperty("patient");
  });

  it("should return 400 with validation errors", async () => {
    // Act
    const response = await request(app)
      .post("/api/patients")
      .send({ name: "A" }); // Données invalides

    // Assert
    expect(response.status).toBe(400);
    expect(response.body).toHaveProperty("errors");
  });
});
```

**Concepts à maîtriser :**

- Tester les status codes HTTP
- Tester les bodies de réponse
- Tester les erreurs de validation
- Mock les dépendances (DB, services)

**Besoin d'aide ?** Demande à l'IA : "Comment tester une route POST avec Supertest ?" ou "Comment mock la base de données dans les tests ?"

---

## 📋 Après-midi (2h) - Tests Frontend

**📚 Documentation utile :**
- [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/) - Guide officiel
- [Testing Library Queries](https://testing-library.com/docs/queries/about/) - Comment interroger les éléments
- [User Event](https://testing-library.com/docs/user-event/intro/) - Simuler les interactions utilisateur

### 1. Tests avec React Testing Library

**Setup :**

```bash
npm install -D @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

**Concepts fondamentaux :**

```typescript
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import PatientForm from "../PatientForm";

describe("PatientForm", () => {
  it("should render form fields", () => {
    // Arrange & Act
    render(<PatientForm />);

    // Assert
    expect(screen.getByLabelText(/name/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
  });

  it("should submit form with valid data", async () => {
    // Arrange
    const mockOnSubmit = jest.fn();
    render(<PatientForm onSubmit={mockOnSubmit} />);
    const user = userEvent.setup();

    // Act
    await user.type(screen.getByLabelText(/name/i), "John");
    await user.type(screen.getByLabelText(/email/i), "john@example.com");
    await user.click(screen.getByRole("button", { name: /submit/i }));

    // Assert
    expect(mockOnSubmit).toHaveBeenCalledWith({
      name: "John",
      email: "john@example.com",
    });
  });
});
```

**Queries par priorité (de préférence à éviter) :**

1. **getByRole** - Le plus accessible (recommandé)
2. **getByLabelText** - Pour les inputs avec labels
3. **getByText** - Pour le texte visible
4. **getByTestId** - Dernier recours (éviter si possible)

**Exemple complet :**

```typescript
// src/components/__tests__/PatientList.test.tsx
import { render, screen, waitFor } from "@testing-library/react";
import { PatientList } from "../PatientList";
import { patientService } from "../../services/patientService";

jest.mock("../../services/patientService");

describe("PatientList", () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  it("should display loading state initially", () => {
    (patientService.getAll as jest.Mock).mockImplementation(
      () => new Promise(() => {}) // Promise qui ne se résout jamais
    );

    render(<PatientList />);
    expect(screen.getByText(/loading/i)).toBeInTheDocument();
  });

  it("should display patients when loaded", async () => {
    // Arrange
    const mockPatients = [
      { id: 1, name: "John", email: "john@example.com" },
      { id: 2, name: "Jane", email: "jane@example.com" },
    ];
    (patientService.getAll as jest.Mock).mockResolvedValue({
      patients: mockPatients,
    });

    // Act
    render(<PatientList />);

    // Assert
    await waitFor(() => {
      expect(screen.getByText("John")).toBeInTheDocument();
      expect(screen.getByText("Jane")).toBeInTheDocument();
    });
  });

  it("should display error message on failure", async () => {
    // Arrange
    (patientService.getAll as jest.Mock).mockRejectedValue(
      new Error("Failed to load")
    );

    // Act
    render(<PatientList />);

    // Assert
    await waitFor(() => {
      expect(screen.getByText(/error/i)).toBeInTheDocument();
    });
  });
});
```

**Besoin d'aide ?** Demande à l'IA : "Comment tester un composant React avec React Testing Library ?" ou "Comment mock un appel API dans un test React ?"

### 2. Mock des appels API

**Pattern recommandé :**

```typescript
// Mock le service
jest.mock("../../services/patientService", () => ({
  patientService: {
    getAll: jest.fn(),
    create: jest.fn(),
    // ... autres méthodes
  },
}));

// Dans le test
import { patientService } from "../../services/patientService";

beforeEach(() => {
  (patientService.getAll as jest.Mock).mockClear();
});

it("should call API on mount", async () => {
  (patientService.getAll as jest.Mock).mockResolvedValue({
    patients: [],
  });

  render(<PatientList />);

  await waitFor(() => {
    expect(patientService.getAll).toHaveBeenCalledTimes(1);
  });
});
```

---

## 📋 Exercice Pratique : "Break Everything"

**Objectif :** Introduire des bugs volontairement, puis les débugger sans AI.

### Bug 1 : API retourne 500 sur certains inputs

**Bug à introduire :**

```typescript
// Dans ta route POST /patients
app.post("/patients", async (req, res) => {
  const { name, email } = req.body;
  
  // BUG: Pas de validation, peut crasher si email est undefined
  const result = await pool.query(
    "INSERT INTO patients (name, email) VALUES ($1, $2)",
    [name, email.toLowerCase()] // Crash si email est undefined
  );
});
```

**Comment débugger :**

1. **Reproduire le bug** : Envoyer une requête avec `email` manquant
2. **Vérifier les logs** : Regarder la stack trace dans la console
3. **Identifier la cause** : `email.toLowerCase()` sur `undefined`
4. **Corriger** : Ajouter validation avant utilisation

**Outils de debugging :**
- `console.log` pour voir les valeurs
- Stack traces dans les logs
- Postman pour tester les requêtes

### Bug 2 : Race condition dans React

**Bug à introduire :**

```typescript
// Dans un composant
useEffect(() => {
  const fetchData = async () => {
    const data = await patientService.getAll();
    setPatients(data.patients); // Peut mettre à jour après unmount
  };
  fetchData();
}, []);
```

**Comment débugger :**

1. **Reproduire** : Naviguer rapidement entre pages
2. **Vérifier les warnings** : "Can't perform a React state update on an unmounted component"
3. **Identifier** : State update après unmount
4. **Corriger** : Utiliser un flag ou AbortController

**Solution :**

```typescript
useEffect(() => {
  let cancelled = false;
  
  const fetchData = async () => {
    const data = await patientService.getAll();
    if (!cancelled) {
      setPatients(data.patients);
    }
  };
  
  fetchData();
  
  return () => {
    cancelled = true;
  };
}, []);
```

### Bug 3 : Fuite mémoire (useEffect sans cleanup)

**Bug à introduire :**

```typescript
useEffect(() => {
  const interval = setInterval(() => {
    // Faire quelque chose
  }, 1000);
  // BUG: Pas de cleanup, l'interval continue après unmount
}, []);
```

**Comment débugger :**

1. **Observer** : Voir que l'interval continue après navigation
2. **Vérifier** : DevTools → Performance → Memory leaks
3. **Identifier** : Pas de cleanup dans useEffect
4. **Corriger** : Retourner une fonction de cleanup

**Solution :**

```typescript
useEffect(() => {
  const interval = setInterval(() => {
    // Faire quelque chose
  }, 1000);
  
  return () => {
    clearInterval(interval);
  };
}, []);
```

**Techniques de debugging :**

- **console.log stratégique** : Logger les valeurs aux moments clés
- **Chrome DevTools debugger** : Mettre des breakpoints
- **React DevTools** : Inspecter les props et state
- **Network tab** : Voir les requêtes HTTP
- **Lire les stack traces** : Comprendre où l'erreur se produit

**Besoin d'aide ?** Demande à l'IA : "Comment utiliser le debugger Chrome ?" ou "Comment détecter une race condition dans React ?"

---

## 📋 Soir (1h) - Documentation

### Documenter tes techniques de debugging

**Créer une checklist de debugging :**

```markdown
# Checklist de Debugging

## Backend
- [ ] Vérifier les logs serveur
- [ ] Tester avec Postman/curl
- [ ] Vérifier les requêtes SQL (logs)
- [ ] Vérifier les variables d'environnement
- [ ] Vérifier les types de données

## Frontend
- [ ] Vérifier la console du navigateur
- [ ] Vérifier l'onglet Network
- [ ] Utiliser React DevTools
- [ ] Vérifier les props et state
- [ ] Vérifier les re-renders

## Général
- [ ] Reproduire le bug systématiquement
- [ ] Isoler le problème (réduire au minimum)
- [ ] Vérifier les dépendances
- [ ] Vérifier les versions
- [ ] Chercher dans la documentation
```

**Prendre des notes sur :**
- Les bugs que tu as rencontrés
- Comment tu les as résolus
- Les outils qui t'ont aidé
- Les patterns à éviter

---

## ✅ Checklist de Validation

À la fin du Jour 8-9, vérifie que tu peux :

- [ ] Écrire des tests unitaires avec Jest
- [ ] Tester les routes API avec Supertest
- [ ] Tester les composants React avec React Testing Library
- [ ] Débugger efficacement sans AI
- [ ] Créer une checklist de debugging personnelle
- [ ] Identifier et corriger des bugs volontairement introduits

---

## 🎯 Points Clés à Retenir

1. **Tests = Confiance** : Les tests te donnent confiance pour refactoriser
2. **TDD = Qualité** : Tests d'abord = meilleur design
3. **Debugging = Méthode** : Approche systématique > intuition
4. **Tools = Efficacité** : Maîtriser les outils de debugging accélère
5. **Documentation = Référence** : Documenter tes techniques pour plus tard

---

## 🚀 Prochaines Étapes

Jour 10-11 : Tu vas pratiquer des simulations complètes d'entretien. C'est le moment de mettre tout en pratique dans des conditions réelles !
