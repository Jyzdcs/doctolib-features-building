# Plan de Préparation 14 Jours - Entretien Doctolib SWE Intern

## 🎯 Objectifs

- Maîtriser le cycle complet de développement de features full-stack
- Devenir autonome sans Cursor/AI assistants
- Acquérir des réflexes de debugging et problem-solving
- Comprendre profondément React + Node.js + PostgreSQL

---

## 📅 SEMAINE 1 : Fondations & Autonomie

### **Jour 1-2 : Fondamentaux Node.js/Express + PostgreSQL**

**Matin (3h)**

- Créer un serveur Express from scratch sans tutoriel
- Implémenter 5 routes REST (GET, POST, PUT, DELETE, PATCH)
- Connecter PostgreSQL avec `pg` library
- Écrire des queries SQL brutes (pas d'ORM)

**Exercice pratique:**

```
Créer une API de gestion de patients:
- POST /patients (créer patient)
- GET /patients/:id (récupérer patient)
- GET /patients (liste avec pagination)
- PUT /patients/:id (update)
- DELETE /patients/:id
```

**Après-midi (2h)**

- Implémenter validation des données (express-validator)
- Gestion d'erreurs propre (try/catch, error middleware)
- Tester avec Postman/Thunder Client MANUELLEMENT
- Documenter ton API (README simple)

**Soir (1h)**

- Lire la doc officielle Express (sections: routing, middleware, error handling)
- Noter les patterns que tu ne connaissais pas

---

### **Jour 3-4 : Maîtrise React + TypeScript**

**Matin (3h)**

- Créer une app React + TypeScript from scratch (`vite create`)
- Implémenter 3 composants sans regarder de doc:
  - Formulaire contrôlé avec validation
  - Liste avec filtrage
  - Modal de confirmation
- Utiliser uniquement `useState`, `useEffect`

**Exercice pratique:**

```
Interface patient CRUD:
- Formulaire d'ajout patient (nom, date naissance, validation)
- Liste des patients (avec recherche)
- Édition inline
- Confirmation avant suppression
```

**Après-midi (2h)**

- Connecter ton frontend React à ton API Node.js (fetch/axios)
- Gérer les états de loading, error, success
- Implémenter error boundaries
- Comprendre le cycle de vie des composants

**Soir (1h)**

- Refactoriser ton code: extraire la logique dans custom hooks
- Créer `usePatients()` qui gère fetch, loading, error

---

### **Jour 5 : Full-Stack Integration**

**Matin (2h)**

- Connecter complètement frontend + backend
- Implémenter le flux complet: ajout → affichage → modification → suppression
- Débugger les problèmes CORS, de sérialisation JSON
- Comprendre les headers HTTP

**Après-midi (2h)**

- Ajouter une feature complexe:
  - Système de rendez-vous liés aux patients
  - Relations SQL (foreign keys)
  - Affichage des rendez-vous dans l'UI patient

**Soir (2h)**

- Code review personnel:
  - Ton code est-il lisible?
  - Les noms de variables ont-ils du sens?
  - Y a-t-il de la duplication?
  - Refactoriser

---

### **Jour 6-7 : Algorithmes & Data Structures**

**Matin (2h chaque jour)**
Résoudre sur LeetCode/HackerRank (SANS AI):

- Jour 6: Arrays & Strings (5 problèmes easy/medium)
  - Two Sum, Valid Parentheses, Longest Substring
- Jour 7: Objects/Maps & Sorting (5 problèmes)
  - Group Anagrams, Top K Frequent Elements

**Après-midi (2h chaque jour)**
**Exercice "Health Data Analysis"** (simule l'entretien):

Jour 6:

```javascript
// Créer des fonctions qui analysent des données de santé
const healthRecords = [
  { date: '2024-01-15', weight: 4.2, height: 52 },
  { date: '2024-02-15', weight: 4.8, height: 55 },
  // ...
];

// Implémenter:
1. calculateGrowthRate(records) // % de croissance
2. findAnomalies(records) // détecte valeurs anormales
3. predictNextValue(records) // régression linéaire simple
```

Jour 7:

```javascript
// Vaccination schedule
const vaccines = [
  { name: 'BCG', recommendedAge: 0, given: true },
  { name: 'DTP', recommendedAge: 2, given: false },
  // ...
];

// Implémenter:
1. getOverdueVaccines(vaccines, babyAgeMonths)
2. getUpcomingVaccines(vaccines, babyAgeMonths, months)
3. calculateVaccinationCompleteness(vaccines)
```

**Soir (1h)**

- Écrire des tests Jest pour tes fonctions
- Apprendre TDD: test d'abord, puis implémentation

---

## 📅 SEMAINE 2 : Pratique Intensive & Simulation

### **Jour 8-9 : Tests & Debugging**

**Matin (2h)**

- Apprendre Jest en profondeur
- Écrire tests unitaires pour ton API Node.js
- Utiliser `supertest` pour tester les routes

**Après-midi (2h)**

- Tests frontend avec React Testing Library
- Tester les interactions utilisateur (clicks, form submit)
- Mock des appels API

**Exercice pratique:**

```
Casser volontairement ton code (introduire bugs):
- Bug 1: API retourne 500 sur certains inputs
- Bug 2: Race condition dans React
- Bug 3: Fuite mémoire (useEffect sans cleanup)

Puis DEBUG sans AI:
- Utiliser console.log stratégiquement
- Utiliser debugger Chrome DevTools
- Lire les stack traces
```

**Soir (1h)**

- Documenter tes techniques de debugging
- Créer une checklist de debugging

---

### **Jour 10-11 : Simulations d'Entretien**

**Chaque jour: 2 simulations complètes (60min chacune)**

**Simulation 1: Patient Health Record**

```
Contexte: App de suivi santé bébé

Phase 1 (10min): Code exploration
- Clone un repo existant
- Comprendre l'architecture
- Identifier les fichiers clés

Phase 2 (15min): Algorithme
Implémenter: calculateBMIPercentile(weight, height, age)
- Utiliser courbes de croissance OMS
- Tests fournis

Phase 3 (25min): Full-stack feature
Ajouter: système d'allergies
- Backend: routes CRUD allergies
- Frontend: UI pour gérer allergies
- Lier aux patients

Phase 4 (10min): UI improvement
- Ajouter un graphique de croissance (recharts)
```

**Simulation 2: Vaccination Tracker**

```
Phase 1: Code exploration (existant)

Phase 2: Algorithme
Implémenter: getVaccinationSchedule(birthDate, vaccines)
- Calculer dates recommandées
- Gérer exceptions/retards

Phase 3: Full-stack
Ajouter: système de notifications vaccins
- Backend: endpoint pour rappels
- Frontend: badge notification

Phase 4: UI
- Améliorer le calendrier vaccinal
```

**Méthode de simulation:**

1. Chronomètre strict
2. ZÉRO AI assistant
3. Note tes difficultés
4. Review après: qu'aurais-tu pu faire mieux?

---

### **Jour 12 : Patterns & Best Practices**

**Matin (3h)**
Étudier et implémenter:

- **Backend patterns:**

  - Controller/Service/Repository pattern
  - Middleware chaining
  - Error handling centralisé
  - Input validation

- **Frontend patterns:**
  - Container/Presentational components
  - Custom hooks pour logique réutilisable
  - Context API pour état global
  - Error boundaries

**Après-midi (2h)**
Refactoriser tes projets précédents avec ces patterns

**Soir (1h)**
Créer ton "cheat sheet" personnel:

- Syntaxe SQL courante
- Hooks React patterns
- Express middleware patterns
- Commandes Git essentielles

---

### **Jour 13 : Edge Cases & Error Handling**

**Toute la journée (5h)**

**Exercice: "Break Everything"**
Reprendre tes projets et tester:

1. **Backend edge cases:**

   - Requêtes avec données manquantes
   - Types de données incorrects
   - IDs inexistants
   - Doublons
   - Strings vides, null, undefined

2. **Frontend edge cases:**

   - API lente/en erreur
   - Clics multiples rapides
   - Formulaires avec données invalides
   - Navigation rapide (unmount pendant fetch)

3. **Implémenter les protections:**
   - Validation stricte
   - Loading states
   - Debouncing
   - Request cancellation (AbortController)

**Soir (2h)**

- Lire des vraies PRs sur GitHub (React, Express)
- Observer comment les pros écrivent du code
- Noter les patterns de code review

---

### **Jour 14 : Répétition Générale**

**Matin (2h)**
Simulation complète finale:

- Nouveau projet from scratch
- Timer 60min strict
- Conditions réelles (pas de doc excessive)

**Après-midi (2h)**
**Mental preparation:**

- Revoir tes notes
- Relire le mail de Doctolib
- Préparer tes questions
- Revoir les concepts clés:
  - REST API design
  - React lifecycle
  - SQL queries
  - TypeScript basics

**Soir (1h)**

- Relaxation
- Préparer ton setup:
  - Connexion internet stable
  - Browser à jour
  - Casque/micro testés
- Coucher tôt

---

## 🛠️ Ressources Essentielles

### Documentation à maîtriser:

- [Express.js Official Docs](https://expressjs.com/) - Routing, Middleware
- [React Official Docs](https://react.dev/) - Hooks, State Management
- [PostgreSQL Tutorial](https://www.postgresql.org/docs/)
- [Jest Documentation](https://jestjs.io/)

### Projets pratiques à faire:

1. **Mini-Doctolib** (Jours 1-5)
2. **Health Analytics** (Jours 6-7)
3. **Simulations** (Jours 10-11)

### Outils de debugging:

- Chrome DevTools (Network, Console, Sources)
- React Developer Tools
- Postman/Thunder Client

---

## 📝 Checklist Process Feature Building

Mémorise ce process (à appliquer le jour J):

### 1. **Compréhension** (5min)

- [ ] Lire le requirement 2 fois
- [ ] Identifier: Backend? Frontend? Les deux?
- [ ] Lister les étapes nécessaires
- [ ] Poser des questions si flou

### 2. **Backend First** (si applicable)

- [ ] Définir le modèle de données (SQL schema)
- [ ] Créer la route API
- [ ] Implémenter la logique métier
- [ ] Tester avec outil HTTP (Postman/curl)
- [ ] Gérer les erreurs

### 3. **Frontend** (si applicable)

- [ ] Créer le composant
- [ ] Implémenter l'UI de base
- [ ] Connecter à l'API
- [ ] Gérer loading/error states
- [ ] Tester manuellement

### 4. **Testing**

- [ ] Tests unitaires (algorithmes)
- [ ] Tests d'intégration (API)
- [ ] Tests manuels UI

### 5. **Refinement**

- [ ] Code review personnel
- [ ] Renommer variables si nécessaire
- [ ] Extraire code dupliqué
- [ ] Vérifier edge cases

---

## 💡 Tips pour le Jour J

### Pendant l'entretien:

1. **Pense à voix haute**: Explique ton raisonnement
2. **Commence simple**: Solution basique qui marche > solution complexe buggée
3. **Tests = guidance**: Lis bien les tests fournis, ils décrivent le comportement attendu
4. **Questions OK**: Si un requirement est flou, demande
5. **Time management**: Ne reste pas bloqué >5min sur un détail

### Si tu bloques:

1. Reformule le problème à voix haute
2. Simplifie: résous une version plus simple d'abord
3. Regarde les tests: que demandent-ils exactement?
4. Skip temporairement et reviens après

### Communication:

- "Je vais commencer par le backend, puis connecter le frontend"
- "Je vois deux approches: X ou Y, je vais faire X parce que..."
- "Je teste mon endpoint avant de continuer"
- "Je refactorise ce bout de code pour le rendre plus lisible"

---

## 🎯 Métriques de Succès

À la fin des 14 jours, tu devrais pouvoir:

- ✅ Créer une API REST complète en 30min
- ✅ Créer un composant React CRUD en 30min
- ✅ Résoudre un algorithme medium sans aide
- ✅ Débugger efficacement avec DevTools
- ✅ Écrire des tests Jest basiques
- ✅ Lire et comprendre du code existant rapidement

---

## 🚀 Let's Go!

**Règle d'or**: Chaque jour, code SANS AI assistant. C'est inconfortable mais c'est comme ça que tu progresseras vraiment.

Bonne préparation! 💪
