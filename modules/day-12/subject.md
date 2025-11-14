# Jour 12 : Patterns & Best Practices

## 🤖 Règles d'Interaction avec l'IA

**Pour ce module, l'IA est autorisée à :**

- ✅ **Répondre à tes questions conceptuelles** : "Qu'est-ce que le pattern Repository ?", "Quand utiliser Context API ?"
- ✅ **Guider ta réflexion** : "Comment structurer mon code avec ce pattern ?", "Quelle approche pour cette feature ?"
- ✅ **Expliquer les patterns** : "Comment fonctionne le pattern Controller-Service-Repository ?", "Pourquoi séparer Container et Presentational ?"
- ✅ **Suggérer des améliorations** : "Comment améliorer cette structure ?", "Quels patterns appliquer ici ?"

**L'IA n'est PAS autorisée à :**

- ❌ **Refactoriser directement** ton code (tu dois le faire toi-même)
- ❌ **Donner des solutions complètes** sans que tu aies essayé d'abord
- ❌ **Écrire tout le code** des patterns (tu dois implémenter)

**Si tu es vraiment bloqué (>20min) :**
- L'IA peut te donner des exemples de patterns ou des hints, mais tu dois adapter à ton code.

---

## 🎯 Objectifs du Module

À la fin de cette journée, tu maîtriseras :

- Patterns backend (Controller/Service/Repository)
- Patterns frontend (Container/Presentational, Custom Hooks, Context API)
- Refactoring de code existant
- Création d'un cheat sheet personnel

---

## 📋 Matin (3h) - Patterns Backend

**📚 Documentation utile :**
- [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) - Architecture propre
- [Repository Pattern](https://martinfowler.com/eaaCatalog/repository.html) - Pattern Repository
- [Express Best Practices](https://expressjs.com/en/advanced/best-practice-performance.html) - Bonnes pratiques Express

### 1. Controller/Service/Repository Pattern

**Pourquoi ce pattern ?**

- **Séparation des responsabilités** : Chaque couche a un rôle précis
- **Testabilité** : Facile de tester chaque couche isolément
- **Réutilisabilité** : Les services peuvent être réutilisés
- **Maintenabilité** : Code plus facile à comprendre et modifier

**Structure :**

```
src/
├── routes/
│   └── patients.ts          # Définition routes (HTTP layer)
├── controllers/
│   └── patientsController.ts # HTTP handling (req/res)
├── services/
│   └── patientsService.ts   # Business logic (règles métier)
└── repositories/
    └── patientsRepository.ts # DB access (queries SQL)
```

**Exemple complet :**

```typescript
// 1. Repository (DB access)
// src/repositories/patientsRepository.ts
import { Pool } from "pg";
import { Patient } from "../types/patient";

export class PatientsRepository {
  constructor(private pool: Pool) {}

  async findById(id: number): Promise<Patient | null> {
    const result = await this.pool.query(
      "SELECT * FROM patients WHERE id = $1",
      [id]
    );
    return result.rows[0] || null;
  }

  async create(patient: Omit<Patient, "id">): Promise<Patient> {
    const result = await this.pool.query(
      "INSERT INTO patients (name, email, date_of_birth) VALUES ($1, $2, $3) RETURNING *",
      [patient.name, patient.email, patient.dateOfBirth]
    );
    return result.rows[0];
  }

  // ... autres méthodes
}

// 2. Service (Business logic)
// src/services/patientsService.ts
import { PatientsRepository } from "../repositories/patientsRepository";
import { Patient } from "../types/patient";

export class PatientsService {
  constructor(private repository: PatientsRepository) {}

  async getById(id: number): Promise<Patient> {
    const patient = await this.repository.findById(id);
    if (!patient) {
      throw new Error("Patient not found");
    }
    return patient;
  }

  async create(patientData: Omit<Patient, "id">): Promise<Patient> {
    // Validation métier
    if (patientData.email.includes("test")) {
      throw new Error("Test emails not allowed");
    }

    // Logique métier
    const patient = await this.repository.create(patientData);
    
    // Actions supplémentaires (ex: envoyer email)
    // await emailService.sendWelcomeEmail(patient.email);
    
    return patient;
  }

  // ... autres méthodes
}

// 3. Controller (HTTP handling)
// src/controllers/patientsController.ts
import { Request, Response, NextFunction } from "express";
import { PatientsService } from "../services/patientsService";

export class PatientsController {
  constructor(private service: PatientsService) {}

  async getById(req: Request, res: Response, next: NextFunction) {
    try {
      const { id } = req.params;
      const patient = await this.service.getById(Number(id));
      res.json(patient);
    } catch (error: any) {
      if (error.message === "Patient not found") {
        return res.status(404).json({ error: error.message });
      }
      next(error);
    }
  }

  async create(req: Request, res: Response, next: NextFunction) {
    try {
      const patient = await this.service.create(req.body);
      res.status(201).json(patient);
    } catch (error: any) {
      if (error.message.includes("not allowed")) {
        return res.status(400).json({ error: error.message });
      }
      next(error);
    }
  }

  // ... autres méthodes
}

// 4. Routes (Route definition)
// src/routes/patients.ts
import express from "express";
import { Pool } from "pg";
import { PatientsRepository } from "../repositories/patientsRepository";
import { PatientsService } from "../services/patientsService";
import { PatientsController } from "../controllers/patientsController";

const router = express.Router();
const pool = new Pool(/* config */);

const repository = new PatientsRepository(pool);
const service = new PatientsService(repository);
const controller = new PatientsController(service);

router.get("/:id", (req, res, next) => controller.getById(req, res, next));
router.post("/", (req, res, next) => controller.create(req, res, next));

export default router;
```

**Avantages :**
- Chaque couche est testable indépendamment
- Facile de changer la DB sans toucher au service
- Facile d'ajouter de la logique métier sans toucher aux routes

**Besoin d'aide ?** Demande à l'IA : "Comment structurer le pattern Controller-Service-Repository ?" ou "Quelle logique mettre dans le service vs le repository ?"

### 2. Middleware Chaining

**Pattern :** Enchaîner plusieurs middlewares pour une route

**Exemples :**

```typescript
// Validation middleware
import { body, validationResult } from "express-validator";

const validatePatient = [
  body("name").trim().isLength({ min: 2 }),
  body("email").isEmail(),
];

// Auth middleware (exemple)
const authenticate = (req: Request, res: Response, next: NextFunction) => {
  const token = req.headers.authorization;
  if (!token) {
    return res.status(401).json({ error: "Unauthorized" });
  }
  // Vérifier le token...
  next();
};

// Utilisation
router.post(
  "/",
  authenticate,        // 1. Vérifier l'auth
  validatePatient,     // 2. Valider les données
  (req, res, next) => controller.create(req, res, next) // 3. Traiter la requête
);
```

**Besoin d'aide ?** Demande à l'IA : "Comment enchaîner des middlewares Express ?" ou "Quel ordre pour les middlewares ?"

### 3. Error Handling Centralisé

**Pattern :** Un seul middleware d'erreur pour toute l'app

```typescript
// src/middlewares/errorHandler.ts
import { Request, Response, NextFunction } from "express";

export class AppError extends Error {
  constructor(
    public statusCode: number,
    message: string,
    public isOperational = true
  ) {
    super(message);
    this.name = this.constructor.name;
    Error.captureStackTrace(this, this.constructor);
  }
}

export const errorHandler = (
  err: Error | AppError,
  req: Request,
  res: Response,
  next: NextFunction
) => {
  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      error: err.message,
    });
  }

  // Erreur PostgreSQL
  if ((err as any).code === "23505") {
    return res.status(409).json({
      error: "Duplicate entry",
    });
  }

  // Erreur générique
  console.error("Error:", err);
  res.status(500).json({
    error: "Internal server error",
  });
};

// Utilisation dans les services
throw new AppError(404, "Patient not found");

// Dans server.ts
app.use(errorHandler);
```

### 4. Input Validation

**Stratégies :**

```typescript
// Niveau route (express-validator)
router.post("/", validatePatient, controller.create);

// Niveau service (validation métier)
async create(data: CreatePatientDto) {
  if (data.email.includes("test")) {
    throw new AppError(400, "Test emails not allowed");
  }
  // ...
}
```

---

## 📋 Après-midi (2h) - Patterns Frontend

**📚 Documentation utile :**
- [React Patterns](https://reactpatterns.com/) - Patterns React
- [Container/Presentational Pattern](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0) - Pattern Container/Presentational
- [Custom Hooks](https://react.dev/learn/reusing-logic-with-custom-hooks) - Custom hooks

### 1. Container/Presentational Components

**Container :** Logique, état, appels API
**Presentational :** UI pure, props seulement

**Exemple :**

```typescript
// Presentational Component (UI pure)
// src/components/features/PatientCard.tsx
interface PatientCardProps {
  patient: Patient;
  onEdit: (id: number) => void;
  onDelete: (id: number) => void;
}

export const PatientCard = ({ patient, onEdit, onDelete }: PatientCardProps) => {
  return (
    <div className="patient-card">
      <h3>{patient.name}</h3>
      <p>{patient.email}</p>
      <button onClick={() => onEdit(patient.id)}>Edit</button>
      <button onClick={() => onDelete(patient.id)}>Delete</button>
    </div>
  );
};

// Container Component (Logique)
// src/components/features/PatientList.tsx
export const PatientList = () => {
  const { patients, loading, error, deletePatient } = usePatients();

  const handleDelete = async (id: number) => {
    if (confirm("Are you sure?")) {
      await deletePatient(id);
    }
  };

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      {patients.map((patient) => (
        <PatientCard
          key={patient.id}
          patient={patient}
          onEdit={(id) => navigate(`/patients/${id}/edit`)}
          onDelete={handleDelete}
        />
      ))}
    </div>
  );
};
```

**Avantages :**
- Composants réutilisables (Presentational)
- Logique centralisée (Container)
- Plus facile à tester

### 2. Custom Hooks pour Logique Réutilisable

**Exemples :**

```typescript
// useForm hook
export const useForm = <T>(initialValues: T) => {
  const [values, setValues] = useState<T>(initialValues);
  const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({});

  const handleChange = (name: keyof T, value: any) => {
    setValues((prev) => ({ ...prev, [name]: value }));
    // Clear error for this field
    setErrors((prev) => ({ ...prev, [name]: undefined }));
  };

  const validate = (): boolean => {
    // Validation logic
    return true;
  };

  return { values, errors, handleChange, validate };
};

// useDebounce hook
export const useDebounce = <T>(value: T, delay: number) => {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);

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
```

### 3. Context API pour État Global

**Quand utiliser :**
- Auth state (utilisateur connecté)
- Theme (dark/light mode)
- Notifications globales

**Exemple :**

```typescript
// src/contexts/AuthContext.tsx
interface AuthContextType {
  user: User | null;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
}

const AuthContext = createContext<AuthContextType | undefined>(undefined);

export const AuthProvider = ({ children }: { children: ReactNode }) => {
  const [user, setUser] = useState<User | null>(null);

  const login = async (email: string, password: string) => {
    const user = await authService.login(email, password);
    setUser(user);
  };

  const logout = () => {
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
};

export const useAuth = () => {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error("useAuth must be used within AuthProvider");
  }
  return context;
};
```

### 4. Error Boundaries

**Implémentation :** (Voir Jour 3-4 pour l'exemple complet)

---

## 📋 Soir (1h) - Cheat Sheet Personnel

**Créer ton cheat sheet avec :**

### Syntaxe SQL courante

```sql
-- SELECT avec JOIN
SELECT p.*, a.date_time
FROM patients p
LEFT JOIN appointments a ON p.id = a.patient_id;

-- INSERT avec RETURNING
INSERT INTO patients (name, email) 
VALUES ($1, $2) 
RETURNING *;

-- UPDATE avec conditions
UPDATE patients 
SET name = $1 
WHERE id = $2 
RETURNING *;
```

### Hooks React patterns

```typescript
// useState
const [state, setState] = useState(initialValue);

// useEffect avec cleanup
useEffect(() => {
  const subscription = subscribe();
  return () => subscription.unsubscribe();
}, [deps]);

// useMemo pour calculs coûteux
const expensiveValue = useMemo(() => compute(), [deps]);
```

### Express middleware patterns

```typescript
// Error handling
app.use((err, req, res, next) => {
  res.status(err.status || 500).json({ error: err.message });
});

// Async route handler
router.get("/", async (req, res, next) => {
  try {
    // ...
  } catch (error) {
    next(error);
  }
});
```

### Commandes Git essentielles

```bash
git add .
git commit -m "feat: add patient feature"
git push origin main
git pull origin main
```

### TypeScript utilities

```typescript
// Partial
type PartialPatient = Partial<Patient>;

// Pick
type PatientName = Pick<Patient, "name" | "email">;

// Omit
type CreatePatient = Omit<Patient, "id" | "createdAt">;
```

---

## ✅ Checklist de Validation

À la fin du Jour 12, vérifie que tu peux :

- [ ] Structurer ton code avec Controller-Service-Repository
- [ ] Utiliser les patterns frontend (Container/Presentational, Custom Hooks)
- [ ] Refactoriser du code existant avec ces patterns
- [ ] Créer un cheat sheet personnel complet

---

## 🎯 Points Clés à Retenir

1. **Patterns = Structure** : Les patterns donnent une structure claire au code
2. **Séparation = Testabilité** : Séparer les responsabilités = code plus testable
3. **Réutilisabilité = Efficacité** : Code réutilisable = moins de duplication
4. **Cheat Sheet = Référence** : Avoir un cheat sheet accélère le développement
5. **Refactoring = Amélioration** : Refactoriser régulièrement améliore la qualité

---

## 🚀 Prochaines Étapes

Jour 13 : Tu vas apprendre à gérer tous les edge cases et les erreurs. C'est crucial pour du code robuste en production !
