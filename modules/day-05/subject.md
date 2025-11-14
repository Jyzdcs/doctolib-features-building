# Jour 5 : Full-Stack Integration

## 🤖 Règles d'Interaction avec l'IA

**Pour ce module, l'IA est autorisée à :**

- ✅ **Répondre à tes questions conceptuelles** : "Comment faire un JOIN SQL ?", "Comment gérer CORS ?"
- ✅ **Guider ta réflexion** : "Comment structurer les relations SQL ?", "Quelle approche pour la synchronisation frontend/backend ?"
- ✅ **Expliquer les erreurs** : "Pourquoi j'ai cette erreur CORS ?", "Comment débugger ce problème de sérialisation JSON ?"
- ✅ **Suggérer des améliorations** : "Comment optimiser cette requête SQL ?", "Quels edge cases considérer ?"

**L'IA n'est PAS autorisée à :**

- ❌ **Écrire le code complet** pour toi (pas de copier-coller de routes/composants entiers)
- ❌ **Résoudre directement** les exercices pratiques (tu dois coder toi-même)
- ❌ **Donner des solutions toutes faites** sans que tu aies essayé d'abord

**Si tu es vraiment bloqué (>20min) :**

- L'IA peut te donner des hints ou un exemple minimal, mais tu dois comprendre et adapter toi-même.

---

## 🎯 Objectifs du Module

À la fin de cette journée, tu seras capable de :

- Connecter complètement frontend + backend
- Implémenter une feature complexe avec relations SQL
- Débugger les problèmes CORS, sérialisation JSON
- Faire un code review personnel

---

## 📋 Matin (2h) - Connexion Complète

### 1. Connecter complètement frontend + backend

**📚 Documentation utile :**

- [CORS Explained](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) - Comprendre CORS
- [Express CORS Middleware](https://expressjs.com/en/resources/middleware/cors.html) - Configuration CORS
- [JSON Serialization](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON) - Gestion JSON

**Flux complet à implémenter :**

1. **Ajout patient → affichage immédiat dans la liste**

   - POST depuis le formulaire
   - Mise à jour de la liste sans rechargement
   - Gestion optimiste (afficher avant confirmation serveur)

2. **Modification patient → mise à jour en temps réel**

   - PUT depuis le formulaire d'édition
   - Mise à jour de l'affichage immédiatement
   - Gestion des erreurs si la mise à jour échoue

3. **Suppression patient → confirmation + retrait de la liste**
   - Modal de confirmation avant suppression
   - DELETE après confirmation
   - Retrait de la liste immédiatement

**Questions à te poser :**

- Comment mettre à jour la liste après une création/modification ?
- Faut-il re-fetcher toutes les données ou mettre à jour localement ?
- Comment gérer les erreurs si l'opération échoue ?

**Besoin d'aide ?** Demande à l'IA : "Comment synchroniser l'état frontend après une mutation ?" ou "Quelle stratégie pour mettre à jour la liste après création ?"

### 2. Débugger les problèmes courants

**CORS (Cross-Origin Resource Sharing) :**

**Problème :** Le navigateur bloque les requêtes entre `localhost:5173` (frontend) et `localhost:3000` (backend)

**Solution côté backend :**

```typescript
// src/server.ts
import cors from "cors";

const app = express();

app.use(
  cors({
    origin: "http://localhost:5173", // Port par défaut de Vite
    credentials: true, // Si tu utilises des cookies
  })
);

// Ou pour accepter toutes les origines en dev (pas en prod !)
// app.use(cors());
```

**Vérification :**

- Vérifie que le middleware CORS est bien configuré
- Vérifie les headers dans l'onglet Network de DevTools
- Vérifie que l'URL du backend est correcte dans le frontend

**Sérialisation JSON :**

**Problème :** Les dates PostgreSQL sont des objets Date, pas des strings JSON

**Solution côté backend :**

```typescript
// Option 1 : Formater les dates dans les queries SQL
const result = await pool.query(
  "SELECT id, name, date_of_birth::text, email, created_at::text FROM patients WHERE id = $1",
  [patientId]
);

// Option 2 : Formater après la query
const patient = result.rows[0];
if (patient) {
  patient.dateOfBirth = patient.date_of_birth.toISOString().split("T")[0];
  patient.createdAt = patient.created_at.toISOString();
}

// Option 3 : Utiliser un middleware de transformation
app.use((req, res, next) => {
  const originalJson = res.json;
  res.json = function (data) {
    // Transformer les dates si nécessaire
    return originalJson.call(this, data);
  };
  next();
});
```

**Solution côté frontend :**

```typescript
// Types TypeScript cohérents
interface Patient {
  id: number;
  name: string;
  dateOfBirth: string; // ISO string format
  email: string;
  createdAt: string; // ISO string format
}

// Parser les dates si nécessaire
const patient: Patient = {
  ...response.data,
  dateOfBirth: new Date(response.data.dateOfBirth).toISOString().split("T")[0],
};
```

**Autres problèmes courants :**

- **Content-Type manquant** : S'assurer que `Content-Type: application/json` est envoyé
- **Body vide** : Vérifier que `app.use(express.json())` est configuré
- **Headers manquants** : Vérifier les headers nécessaires (Authorization, etc.)

**Besoin d'aide ?** Demande à l'IA : "Comment configurer CORS dans Express ?" ou "Comment gérer les dates entre PostgreSQL et JSON ?"

---

## 📋 Après-midi (2h) - Feature Complexe

### Système de rendez-vous liés aux patients

**📚 Documentation utile :**

- [PostgreSQL Foreign Keys](https://www.postgresql.org/docs/current/tutorial-fk.html) - Relations entre tables
- [SQL JOINs](https://www.postgresql.org/docs/current/tutorial-join.html) - Jointures SQL
- [Express Routing](https://expressjs.com/en/guide/routing.html) - Routes imbriquées

**Backend :**

**1. Schéma de base de données :**

```sql
-- Table appointments
CREATE TABLE appointments (
  id SERIAL PRIMARY KEY,
  patient_id INTEGER NOT NULL REFERENCES patients(id) ON DELETE CASCADE,
  date_time TIMESTAMP NOT NULL,
  duration_minutes INTEGER DEFAULT 30,
  notes TEXT,
  status VARCHAR(50) DEFAULT 'scheduled', -- scheduled, completed, cancelled
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Index pour les recherches fréquentes
CREATE INDEX idx_appointments_patient_id ON appointments(patient_id);
CREATE INDEX idx_appointments_date_time ON appointments(date_time);
CREATE INDEX idx_appointments_status ON appointments(status);

-- Index composite pour les requêtes complexes
CREATE INDEX idx_appointments_patient_date ON appointments(patient_id, date_time DESC);
```

**2. Routes CRUD pour rendez-vous :**

```typescript
// src/routes/appointments.ts
import express from "express";
import { pool } from "../config/database";

const router = express.Router();

// GET /patients/:patientId/appointments
router.get("/patients/:patientId/appointments", async (req, res, next) => {
  try {
    const { patientId } = req.params;
    const result = await pool.query(
      `SELECT a.id, a.date_time, a.duration_minutes, a.notes, a.status, a.created_at
       FROM appointments a
       WHERE a.patient_id = $1
       ORDER BY a.date_time DESC`,
      [patientId]
    );
    res.json({ appointments: result.rows });
  } catch (error) {
    next(error);
  }
});

// POST /patients/:patientId/appointments
router.post("/patients/:patientId/appointments", async (req, res, next) => {
  try {
    const { patientId } = req.params;
    const { dateTime, durationMinutes, notes } = req.body;

    const result = await pool.query(
      `INSERT INTO appointments (patient_id, date_time, duration_minutes, notes)
       VALUES ($1, $2, $3, $4)
       RETURNING id, patient_id, date_time, duration_minutes, notes, status, created_at`,
      [patientId, dateTime, durationMinutes || 30, notes || null]
    );

    res.status(201).json({ appointment: result.rows[0] });
  } catch (error) {
    next(error);
  }
});

// PUT /appointments/:id
router.put("/appointments/:id", async (req, res, next) => {
  try {
    const { id } = req.params;
    const { dateTime, durationMinutes, notes, status } = req.body;

    const updates: string[] = [];
    const values: any[] = [];
    let paramIndex = 1;

    if (dateTime !== undefined) {
      updates.push(`date_time = $${paramIndex++}`);
      values.push(dateTime);
    }
    if (durationMinutes !== undefined) {
      updates.push(`duration_minutes = $${paramIndex++}`);
      values.push(durationMinutes);
    }
    if (notes !== undefined) {
      updates.push(`notes = $${paramIndex++}`);
      values.push(notes);
    }
    if (status !== undefined) {
      updates.push(`status = $${paramIndex++}`);
      values.push(status);
    }

    values.push(id);
    const result = await pool.query(
      `UPDATE appointments
       SET ${updates.join(", ")}, updated_at = CURRENT_TIMESTAMP
       WHERE id = $${paramIndex}
       RETURNING *`,
      values
    );

    if (result.rows.length === 0) {
      return res.status(404).json({ error: "Appointment not found" });
    }

    res.json({ appointment: result.rows[0] });
  } catch (error) {
    next(error);
  }
});

// DELETE /appointments/:id
router.delete("/appointments/:id", async (req, res, next) => {
  try {
    const { id } = req.params;
    const result = await pool.query(
      "DELETE FROM appointments WHERE id = $1 RETURNING id",
      [id]
    );

    if (result.rows.length === 0) {
      return res.status(404).json({ error: "Appointment not found" });
    }

    res.status(204).send();
  } catch (error) {
    next(error);
  }
});

export default router;
```

**3. Relations SQL (JOIN) :**

```typescript
// Exemple : GET /appointments avec infos patient
router.get("/appointments", async (req, res, next) => {
  try {
    const result = await pool.query(
      `SELECT 
         a.id, 
         a.date_time, 
         a.duration_minutes, 
         a.status,
         p.id as patient_id,
         p.name as patient_name,
         p.email as patient_email
       FROM appointments a
       INNER JOIN patients p ON a.patient_id = p.id
       WHERE a.date_time >= CURRENT_DATE
       ORDER BY a.date_time ASC`
    );
    res.json({ appointments: result.rows });
  } catch (error) {
    next(error);
  }
});
```

**Frontend :**

**1. Service API :**

```typescript
// src/services/appointmentService.ts
import axios from "axios";

const API_BASE_URL = "http://localhost:3000/api";

export interface Appointment {
  id: number;
  patientId: number;
  dateTime: string;
  durationMinutes: number;
  notes?: string;
  status: "scheduled" | "completed" | "cancelled";
  createdAt: string;
}

export const appointmentService = {
  getByPatient: async (patientId: number): Promise<Appointment[]> => {
    const response = await axios.get(
      `${API_BASE_URL}/patients/${patientId}/appointments`
    );
    return response.data.appointments;
  },

  create: async (
    patientId: number,
    appointment: Partial<Appointment>
  ): Promise<Appointment> => {
    const response = await axios.post(
      `${API_BASE_URL}/patients/${patientId}/appointments`,
      appointment
    );
    return response.data.appointment;
  },

  update: async (
    id: number,
    appointment: Partial<Appointment>
  ): Promise<Appointment> => {
    const response = await axios.put(
      `${API_BASE_URL}/appointments/${id}`,
      appointment
    );
    return response.data.appointment;
  },

  delete: async (id: number): Promise<void> => {
    await axios.delete(`${API_BASE_URL}/appointments/${id}`);
  },
};
```

**2. Composant d'affichage :**

```typescript
// src/components/features/PatientAppointments.tsx
import { useEffect, useState } from "react";
import {
  appointmentService,
  Appointment,
} from "../../services/appointmentService";

interface Props {
  patientId: number;
}

const PatientAppointments = ({ patientId }: Props) => {
  const [appointments, setAppointments] = useState<Appointment[]>([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    const fetchAppointments = async () => {
      setLoading(true);
      try {
        const data = await appointmentService.getByPatient(patientId);
        setAppointments(data);
      } catch (error) {
        console.error("Failed to load appointments", error);
      } finally {
        setLoading(false);
      }
    };

    fetchAppointments();
  }, [patientId]);

  if (loading) return <div>Loading appointments...</div>;

  return (
    <div>
      <h3>Appointments</h3>
      {appointments.length === 0 ? (
        <p>No appointments scheduled.</p>
      ) : (
        <ul>
          {appointments.map((apt) => (
            <li key={apt.id}>
              {new Date(apt.dateTime).toLocaleString()} - {apt.durationMinutes}
              min - {apt.status}
            </li>
          ))}
        </ul>
      )}
    </div>
  );
};

export default PatientAppointments;
```

**3. Formulaire de création :**

```typescript
// src/components/features/AppointmentForm.tsx
import { useState } from "react";
import { appointmentService } from "../../services/appointmentService";

interface Props {
  patientId: number;
  onSuccess?: () => void;
}

const AppointmentForm = ({ patientId, onSuccess }: Props) => {
  const [dateTime, setDateTime] = useState("");
  const [durationMinutes, setDurationMinutes] = useState(30);
  const [notes, setNotes] = useState("");
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    setLoading(true);
    setError(null);

    try {
      await appointmentService.create(patientId, {
        dateTime,
        durationMinutes,
        notes,
      });
      onSuccess?.();
      // Reset form
      setDateTime("");
      setDurationMinutes(30);
      setNotes("");
    } catch (err) {
      setError("Failed to create appointment");
    } finally {
      setLoading(false);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="datetime-local"
        value={dateTime}
        onChange={(e) => setDateTime(e.target.value)}
        required
      />
      <input
        type="number"
        value={durationMinutes}
        onChange={(e) => setDurationMinutes(Number(e.target.value))}
        min="15"
        step="15"
      />
      <textarea value={notes} onChange={(e) => setNotes(e.target.value)} />
      {error && <div>{error}</div>}
      <button type="submit" disabled={loading}>
        {loading ? "Creating..." : "Create Appointment"}
      </button>
    </form>
  );
};

export default AppointmentForm;
```

**Questions à te poser :**

- Comment structurer les routes REST pour les relations ?
- Faut-il `/patients/:id/appointments` ou `/appointments?patientId=:id` ?
- Comment gérer la validation des dates (pas dans le passé) ?
- Comment gérer les conflits (deux rendez-vous au même moment) ?

**Besoin d'aide ?** Demande à l'IA : "Comment structurer les routes REST pour les relations ?" ou "Comment implémenter un JOIN SQL dans Node.js ?"

---

## 📋 Soir (2h) - Code Review Personnel

**📚 Documentation utile :**

- [Clean Code Principles](https://github.com/ryanmcdermott/clean-code-javascript) - Principes de code propre
- [Code Review Best Practices](https://github.com/google/eng-practices/blob/master/review/README.md) - Bonnes pratiques

**Questions à te poser :**

1. **Lisibilité :**

   - Mon code est-il facile à lire ?
   - Les noms de variables/fonctions sont-ils clairs ?
   - Y a-t-il trop de commentaires ou pas assez ?

2. **Structure :**

   - Les responsabilités sont-elles bien séparées ?
   - Y a-t-il de la duplication de code ?
   - Les fonctions sont-elles trop longues ?

3. **Performance :**

   - Y a-t-il des requêtes N+1 ?
   - Les requêtes SQL sont-elles optimisées ?
   - Y a-t-il des re-renders inutiles côté React ?

4. **Sécurité :**

   - Les inputs sont-ils validés ?
   - Les requêtes SQL sont-elles paramétrées ?
   - Les erreurs exposent-elles trop d'infos ?

5. **Maintenabilité :**
   - Le code sera-t-il facile à modifier dans 6 mois ?
   - Y a-t-il des "magic numbers" à extraire en constantes ?
   - Les types TypeScript sont-ils bien définis ?

**Exercice pratique :**

1. **Refactoriser une fonction trop longue**

   - Extraire des sous-fonctions
   - Renommer les variables
   - Ajouter des commentaires si nécessaire

2. **Éliminer la duplication**

   - Identifier le code dupliqué
   - Extraire dans une fonction réutilisable
   - Tester que ça fonctionne toujours

3. **Améliorer les noms**
   - Remplacer les noms vagues (`data`, `temp`, `x`)
   - Utiliser des noms descriptifs (`patientList`, `isLoading`, `errorMessage`)

**Besoin d'aide ?** Demande à l'IA : "Comment améliorer cette fonction ?" ou "Y a-t-il de la duplication dans ce code ?"

---

## ✅ Checklist de Validation

À la fin du Jour 5, vérifie que tu peux :

- [ ] Connecter frontend et backend sans erreurs CORS
- [ ] Implémenter une feature avec relations SQL (foreign keys, JOINs)
- [ ] Gérer la sérialisation JSON correctement (dates, etc.)
- [ ] Faire un code review personnel constructif
- [ ] Refactoriser du code pour l'améliorer

---

## 🎯 Points Clés à Retenir

1. **CORS = Configuration backend** : Toujours configurer CORS pour les requêtes cross-origin
2. **Relations SQL = Foreign keys** : Utiliser les contraintes de clés étrangères pour l'intégrité
3. **JOINs = Performance** : Préférer JOINs à plusieurs requêtes séparées
4. **Code review = Amélioration continue** : Toujours revoir son code avant de continuer
5. **Refactoring = Maintenabilité** : Le code propre est plus facile à maintenir

---

## 🚀 Prochaines Étapes

Jour 6-7 : Tu vas travailler sur les algorithmes et l'analyse de données de santé. C'est le moment de mettre en pratique tes compétences en résolution de problèmes !
