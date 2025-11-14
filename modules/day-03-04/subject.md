# Jour 3-4 : Maîtrise React + TypeScript

## 🤖 Règles d'Interaction avec l'IA

**Pour ce module, l'IA est autorisée à :**

- ✅ **Répondre à tes questions conceptuelles** : "Comment fonctionne useState ?", "Quelle est la différence entre useEffect et useMemo ?"
- ✅ **Guider ta réflexion** : "Comment structurer mon composant ?", "Quelle approche pour gérer les erreurs ?"
- ✅ **Expliquer les erreurs React** : "Pourquoi j'ai ce warning ?", "Comment débugger ce problème de re-render ?"
- ✅ **Suggérer des améliorations** : "Comment optimiser ce composant ?", "Quels patterns React utiliser ici ?"

**L'IA n'est PAS autorisée à :**

- ❌ **Écrire les composants complets** pour toi (pas de copier-coller de composants entiers)
- ❌ **Résoudre directement** les exercices pratiques (tu dois coder toi-même)
- ❌ **Donner des solutions toutes faites** sans que tu aies essayé d'abord

**Si tu es vraiment bloqué (>20min) :**

- L'IA peut te donner des hints ou un exemple minimal, mais tu dois comprendre et adapter toi-même.

---

## 🎯 Objectifs du Module

À la fin de ces 2 jours, tu seras capable de :

- Créer une app React + TypeScript from scratch
- Implémenter des composants fonctionnels avec hooks
- Gérer les états (loading, error, success)
- Connecter le frontend à ton API backend
- Extraire la logique dans des custom hooks

---

## 📋 Matin (3h) - Setup & Composants React

### 1. Créer une app React + TypeScript from scratch

**📚 Documentation utile :**

- [Vite Documentation](https://vitejs.dev/) - Build tool rapide pour React
- [React Documentation](https://react.dev/) - Guide officiel React
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html) - Guide TypeScript

**Setup avec Vite :**

```bash
npm create vite@latest doctolib-frontend -- --template react-ts
cd doctolib-frontend
npm install
npm install axios  # Pour les appels API
```

**Structure recommandée :**

```
doctolib-frontend/
├── src/
│   ├── components/
│   │   ├── shared/          # Composants réutilisables
│   │   │   ├── Button.tsx
│   │   │   ├── Modal.tsx
│   │   │   └── Input.tsx
│   │   └── features/        # Composants métier
│   │       ├── PatientForm.tsx
│   │       ├── PatientList.tsx
│   │       └── PatientCard.tsx
│   ├── hooks/               # Custom hooks
│   │   └── usePatients.ts
│   ├── services/            # API clients
│   │   └── patientService.ts
│   ├── types/               # Types TypeScript
│   │   └── patient.ts
│   └── App.tsx
├── package.json
└── tsconfig.json
```

**Exercice mental :**

- Pourquoi utiliser Vite plutôt que Create React App ?
- Quelle est la différence entre un composant fonctionnel et un composant classe ?
- Comment TypeScript aide-t-il dans React ?

**Besoin d'aide ?** Demande à l'IA : "Comment configurer TypeScript avec React ?" ou "Quelle structure de dossiers pour un projet React ?"

### 2. Implémenter 3 composants sans regarder de doc

**📚 Documentation utile :**

- [React Hooks](https://react.dev/reference/react) - useState, useEffect, etc.
- [React Component Patterns](https://react.dev/learn/thinking-in-react) - Comment penser en composants
- [TypeScript avec React](https://react-typescript-cheatsheet.netlify.app/) - Cheat sheet TypeScript + React

**Exercice pratique : Interface patient CRUD**

1. **Formulaire d'ajout patient** (composant contrôlé)

   - Champs : nom, date de naissance, email
   - Validation côté client (email format, champs requis)
   - État de soumission (loading, success, error)
   - Reset du formulaire après succès

   **Questions à te poser :**

   - Comment gérer les inputs contrôlés ?
   - Comment valider avant soumission ?
   - Comment afficher les erreurs de validation ?

2. **Liste des patients** (avec recherche)

   - Affichage des patients (card ou table)
   - Barre de recherche (input)
   - Filtrage en temps réel (pas de bouton "Rechercher")
   - État vide si aucun résultat

   **Questions à te poser :**

   - Comment filtrer la liste efficacement ?
   - Comment éviter trop de re-renders ?
   - Comment gérer le cas "aucun résultat" ?

3. **Modal de confirmation** (avant suppression)

   - Réutilisable (props pour message personnalisé)
   - Props pour callbacks (onConfirm, onCancel)
   - Overlay avec backdrop
   - Fermeture au clic sur backdrop ou bouton

   **Questions à te poser :**

   - Comment rendre un composant réutilisable ?
   - Comment gérer le z-index et l'overlay ?
   - Comment empêcher le scroll du body quand modal ouverte ?

**Utiliser uniquement `useState` et `useEffect`** (pas de librairies externes pour l'état)

**Besoin d'aide ?** Demande à l'IA : "Comment créer un input contrôlé dans React ?" ou "Comment implémenter une recherche en temps réel ?"

---

## 📋 Après-midi (2h) - Intégration Backend

### 1. Connecter ton frontend React à ton API Node.js

**📚 Documentation utile :**

- [Axios Documentation](https://axios-http.com/docs/intro) - Client HTTP pour les appels API
- [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) - Alternative native à Axios
- [CORS Explained](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) - Comprendre CORS

**Service API client :**

```typescript
// src/services/patientService.ts
import axios from "axios";

const API_BASE_URL = "http://localhost:3000/api";

export interface Patient {
  id: number;
  name: string;
  dateOfBirth: string;
  email: string;
  createdAt: string;
}

export interface CreatePatientDto {
  name: string;
  dateOfBirth: string;
  email: string;
}

export const patientService = {
  getAll: async (
    page = 1,
    limit = 10
  ): Promise<{ patients: Patient[]; total: number; page: number }> => {
    const response = await axios.get(`${API_BASE_URL}/patients`, {
      params: { page, limit },
    });
    return response.data;
  },

  getById: async (id: number): Promise<Patient> => {
    const response = await axios.get(`${API_BASE_URL}/patients/${id}`);
    return response.data;
  },

  create: async (patient: CreatePatientDto): Promise<Patient> => {
    const response = await axios.post(`${API_BASE_URL}/patients`, patient);
    return response.data;
  },

  update: async (
    id: number,
    patient: Partial<CreatePatientDto>
  ): Promise<Patient> => {
    const response = await axios.put(`${API_BASE_URL}/patients/${id}`, patient);
    return response.data;
  },

  delete: async (id: number): Promise<void> => {
    await axios.delete(`${API_BASE_URL}/patients/${id}`);
  },
};
```

**Configuration CORS côté backend (si nécessaire) :**

```typescript
// Dans ton serveur Express
import cors from "cors";

app.use(
  cors({
    origin: "http://localhost:5173", // Port par défaut de Vite
    credentials: true,
  })
);
```

**Besoin d'aide ?** Demande à l'IA : "Comment configurer CORS dans Express ?" ou "Comment gérer les erreurs Axios dans React ?"

### 2. Gérer les états de loading, error, success

**📚 Documentation utile :**

- [React useState Hook](https://react.dev/reference/react/useState) - Gestion d'état
- [React useEffect Hook](https://react.dev/reference/react/useEffect) - Effets de bord
- [Error Handling in React](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary) - Gestion d'erreurs

**Pattern recommandé :**

```typescript
import { useState, useEffect } from "react";
import { patientService, Patient } from "../services/patientService";

const PatientList = () => {
  const [patients, setPatients] = useState<Patient[]>([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchPatients = async () => {
      setLoading(true);
      setError(null);
      try {
        const data = await patientService.getAll();
        setPatients(data.patients);
      } catch (err) {
        setError("Failed to load patients. Please try again.");
        console.error("Error fetching patients:", err);
      } finally {
        setLoading(false);
      }
    };

    fetchPatients();
  }, []);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (patients.length === 0) return <div>No patients found.</div>;

  return (
    <ul>
      {patients.map((patient) => (
        <li key={patient.id}>{patient.name}</li>
      ))}
    </ul>
  );
};
```

**Concepts à maîtriser :**

- `useState` pour gérer les états locaux
- `useEffect` pour les effets de bord (appels API)
- Gestion des 3 états : loading, error, success
- Cleanup dans `useEffect` si nécessaire (annulation de requêtes)

**Besoin d'aide ?** Demande à l'IA : "Comment gérer les états loading/error dans React ?" ou "Comment éviter les memory leaks avec useEffect ?"

### 3. Implémenter error boundaries

**📚 Documentation utile :**

- [React Error Boundaries](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary) - Guide officiel
- [Error Boundary Example](https://react.dev/reference/react/Component#error-boundaries) - Exemple de code

**Pourquoi ?**

- Capturer les erreurs React (erreurs de rendu, pas les erreurs async)
- Afficher un fallback UI au lieu de crasher toute l'app
- Logger les erreurs pour le debugging

**Exemple d'implémentation :**

```typescript
// src/components/shared/ErrorBoundary.tsx
import { Component, ReactNode } from "react";

interface Props {
  children: ReactNode;
  fallback?: ReactNode;
}

interface State {
  hasError: boolean;
  error?: Error;
}

class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    console.error("Error caught by boundary:", error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || <div>Something went wrong.</div>;
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

**Utilisation :**

```typescript
<ErrorBoundary fallback={<div>Error loading patients</div>}>
  <PatientList />
</ErrorBoundary>
```

**Note importante :** Les Error Boundaries ne capturent PAS les erreurs dans :

- Les event handlers
- Le code asynchrone (setTimeout, callbacks)
- Le SSR
- Les Error Boundaries eux-mêmes

**Besoin d'aide ?** Demande à l'IA : "Comment créer un Error Boundary dans React ?" ou "Quelles erreurs un Error Boundary peut-il capturer ?"

---

## 📋 Soir (1h) - Custom Hooks

### Refactoriser : extraire la logique dans custom hooks

**📚 Documentation utile :**

- [Custom Hooks](https://react.dev/learn/reusing-logic-with-custom-hooks) - Guide officiel
- [Rules of Hooks](https://react.dev/reference/rules/rules-of-hooks) - Règles à respecter

**Créer `usePatients()` hook :**

```typescript
// src/hooks/usePatients.ts
import { useState, useEffect } from "react";
import {
  patientService,
  Patient,
  CreatePatientDto,
} from "../services/patientService";

interface UsePatientsReturn {
  patients: Patient[];
  loading: boolean;
  error: string | null;
  fetchPatients: () => Promise<void>;
  createPatient: (patient: CreatePatientDto) => Promise<void>;
  updatePatient: (
    id: number,
    patient: Partial<CreatePatientDto>
  ) => Promise<void>;
  deletePatient: (id: number) => Promise<void>;
}

export const usePatients = (): UsePatientsReturn => {
  const [patients, setPatients] = useState<Patient[]>([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const fetchPatients = async () => {
    setLoading(true);
    setError(null);
    try {
      const data = await patientService.getAll();
      setPatients(data.patients);
    } catch (err) {
      setError("Failed to load patients");
    } finally {
      setLoading(false);
    }
  };

  const createPatient = async (patient: CreatePatientDto) => {
    try {
      const newPatient = await patientService.create(patient);
      setPatients((prev) => [...prev, newPatient]);
    } catch (err) {
      setError("Failed to create patient");
      throw err;
    }
  };

  const updatePatient = async (
    id: number,
    patient: Partial<CreatePatientDto>
  ) => {
    try {
      const updated = await patientService.update(id, patient);
      setPatients((prev) => prev.map((p) => (p.id === id ? updated : p)));
    } catch (err) {
      setError("Failed to update patient");
      throw err;
    }
  };

  const deletePatient = async (id: number) => {
    try {
      await patientService.delete(id);
      setPatients((prev) => prev.filter((p) => p.id !== id));
    } catch (err) {
      setError("Failed to delete patient");
      throw err;
    }
  };

  useEffect(() => {
    fetchPatients();
  }, []);

  return {
    patients,
    loading,
    error,
    fetchPatients,
    createPatient,
    updatePatient,
    deletePatient,
  };
};
```

**Utilisation dans un composant :**

```typescript
const PatientList = () => {
  const { patients, loading, error, deletePatient } = usePatients();

  // ... reste du composant
};
```

**Avantages :**

- Réutilisable dans plusieurs composants
- Logique séparée de l'UI
- Plus facile à tester (hook isolé)
- Code plus propre et maintenable

**Règles des Hooks à respecter :**

- ✅ Appeler les hooks au top level (pas dans des conditions)
- ✅ Appeler les hooks uniquement dans des composants React ou custom hooks
- ✅ Nommer les custom hooks avec `use` au début

**Besoin d'aide ?** Demande à l'IA : "Comment créer un custom hook React ?" ou "Comment tester un custom hook ?"

---

## ✅ Checklist de Validation

À la fin du Jour 3-4, vérifie que tu peux :

- [ ] Créer une app React + TypeScript from scratch avec Vite
- [ ] Implémenter 3 composants fonctionnels (form, list, modal)
- [ ] Gérer les états loading/error/success
- [ ] Connecter le frontend à ton API backend
- [ ] Créer et utiliser un custom hook
- [ ] Implémenter un Error Boundary
- [ ] Gérer CORS si nécessaire

---

## 🎯 Points Clés à Retenir

1. **React = Composants** : Pense en composants réutilisables
2. **Hooks = Logique réutilisable** : useState, useEffect, custom hooks
3. **TypeScript = Sécurité** : Types pour éviter les erreurs à l'exécution
4. **API = Service layer** : Séparer les appels API dans des services
5. **Error handling = UX** : Gérer loading, error, success pour une bonne UX

---

## 🚀 Prochaines Étapes

Jour 5 : Intégration complète frontend + backend avec une feature complexe (rendez-vous). Tu vas connecter tout ce que tu as appris !
