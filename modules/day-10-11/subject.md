# Jour 10-11 : Simulations d'Entretien

## 🤖 Règles d'Interaction avec l'IA

**Pour ce module, l'IA est STRICTEMENT INTERDITE :**

- ❌ **ZÉRO AI assistant** pendant les simulations
- ❌ **Pas de questions** à l'IA pendant les 60 minutes
- ❌ **Pas de code généré** par l'IA
- ❌ **Conditions réelles** : Comme si tu étais en entretien

**L'IA peut t'aider APRÈS chaque simulation :**

- ✅ **Review de ta performance** : "Comment aurais-tu pu faire mieux ?"
- ✅ **Analyse des difficultés** : "Qu'est-ce qui t'a bloqué ?"
- ✅ **Suggestions d'amélioration** : "Quels patterns tu aurais dû utiliser ?"
- ✅ **Préparation pour la prochaine** : "Sur quoi te concentrer pour la prochaine simulation ?"

**Règle d'or :** Si tu utilises l'IA pendant une simulation, considère-la comme échouée et recommence.

---

## 🎯 Objectifs du Module

À la fin de ces 2 jours, tu auras :

- Pratiqué 4 simulations complètes d'entretien (60min chacune)
- Géré le stress du timer strict
- Identifié tes points faibles
- Développé des réflexes d'entretien
- Créé un plan d'amélioration personnel

---

## 📋 Format des Simulations

**Chaque simulation : 60 minutes strictes**

### Phase 1 (10min) : Code Exploration

**Objectif :** Comprendre un codebase existant rapidement

**Stratégie :**
1. **Lire le README** (2min) - Comprendre le projet
2. **Explorer la structure** (3min) - Identifier les dossiers clés
3. **Lire les fichiers principaux** (5min) - Comprendre l'architecture

**Questions à te poser :**
- Quelle est l'architecture du projet ?
- Où sont les routes API ?
- Où sont les composants React ?
- Comment sont organisées les données ?

**📚 Documentation utile :**
- [How to Read Code](https://www.freecodecamp.org/news/how-to-read-code-bf478c262932/) - Guide pour lire du code
- [Code Reading Techniques](https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/) - Techniques de lecture

### Phase 2 (15min) : Algorithme

**Objectif :** Implémenter une fonction avec tests fournis

**Stratégie :**
1. **Lire les tests** (3min) - Comprendre le comportement attendu
2. **Analyser les inputs/outputs** (2min) - Identifier les types
3. **Réfléchir à l'approche** (5min) - Choisir l'algorithme
4. **Implémenter** (5min) - Coder la solution

**Conseils :**
- Les tests sont ta documentation
- Commence par une solution simple qui marche
- Optimise après si tu as le temps

**📚 Documentation utile :**
- [Test-Driven Development](https://www.freecodecamp.org/news/test-driven-development-what-it-is-and-what-it-is-not-41fa6bca02a2/) - TDD
- [Algorithm Patterns](https://www.patterns.dev/) - Patterns algorithmiques

### Phase 3 (25min) : Full-Stack Feature

**Objectif :** Implémenter une feature complète backend + frontend

**Stratégie :**
1. **Planifier** (3min) - Décomposer en étapes
2. **Backend d'abord** (10min) - Routes, validation, DB
3. **Frontend ensuite** (10min) - Composants, appels API
4. **Intégration** (2min) - Tester le flux complet

**Conseils :**
- Backend d'abord = plus facile à tester
- Commence simple, améliore après
- Teste manuellement à chaque étape

**📚 Documentation utile :**
- [REST API Design](https://restfulapi.net/) - Bonnes pratiques REST
- [React Component Patterns](https://react.dev/learn/thinking-in-react) - Patterns React

### Phase 4 (10min) : UI Improvement

**Objectif :** Améliorer l'expérience utilisateur

**Stratégie :**
1. **Identifier les améliorations** (2min) - UX, accessibilité, visuel
2. **Implémenter** (7min) - Choisir 1-2 améliorations
3. **Vérifier** (1min) - Tester rapidement

**Conseils :**
- Focus sur l'impact utilisateur
- Accessibilité = important
- Loading states, error messages

**📚 Documentation utile :**
- [Web Accessibility](https://www.w3.org/WAI/fundamentals/accessibility-intro/) - Accessibilité web
- [UX Best Practices](https://www.nngroup.com/articles/usability-101-introduction-to-usability/) - Bonnes pratiques UX

---

## 📋 Simulation 1 : Patient Health Record

**Contexte :** App de suivi santé bébé

**Repo à cloner (ou créer) :**
```
baby-health-tracker/
├── backend/          # Node.js + Express
├── frontend/         # React + TypeScript
└── README.md
```

### Phase 1 : Code Exploration (10min)

**Tâches :**
- [ ] Lire le README
- [ ] Explorer la structure backend
- [ ] Explorer la structure frontend
- [ ] Identifier où sont les routes API
- [ ] Identifier où sont les composants React

### Phase 2 : Algorithme (15min)

**Fonction à implémenter :**

```typescript
/**
 * Calcule le percentile de BMI pour un bébé
 * @param weight - Poids en kg
 * @param height - Taille en cm
 * @param ageMonths - Âge en mois
 * @param gender - "male" | "female"
 * @returns Percentile entre 0 et 100
 */
function calculateBMIPercentile(
  weight: number,
  height: number,
  ageMonths: number,
  gender: "male" | "female"
): number {
  // TODO: Implémenter
  // Utiliser les courbes de croissance OMS
  // Retourner le percentile (0-100)
}
```

**Tests fournis :**

```typescript
describe("calculateBMIPercentile", () => {
  it("should return percentile for 6-month-old baby", () => {
    const percentile = calculateBMIPercentile(7.5, 67, 6, "male");
    expect(percentile).toBeGreaterThan(0);
    expect(percentile).toBeLessThanOrEqual(100);
  });
  
  // ... autres tests
});
```

**Approche suggérée :**
- Utiliser les données de référence OMS (fournies dans le repo)
- Interpoler entre les valeurs de référence
- Gérer les cas limites (âge hors courbes)

### Phase 3 : Full-Stack Feature (25min)

**Feature à ajouter : Système d'allergies**

**Backend (10min) :**
- [ ] Créer table `allergies` avec foreign key vers `patients`
- [ ] Routes CRUD : GET, POST, PUT, DELETE
- [ ] Validation des données
- [ ] Tester avec Postman

**Frontend (10min) :**
- [ ] Composant pour afficher les allergies d'un patient
- [ ] Formulaire pour ajouter une allergie
- [ ] Connexion à l'API
- [ ] Gestion des états (loading, error)

**Intégration (5min) :**
- [ ] Tester le flux complet
- [ ] Vérifier que les allergies s'affichent dans l'UI patient

### Phase 4 : UI Improvement (10min)

**Améliorations à implémenter :**
- [ ] Ajouter un graphique de croissance (recharts)
- [ ] Améliorer les messages d'erreur
- [ ] Ajouter des loading states

---

## 📋 Simulation 2 : Vaccination Tracker

**Contexte :** Application de suivi des vaccinations

### Phase 2 : Algorithme (15min)

**Fonction à implémenter :**

```typescript
/**
 * Calcule le calendrier vaccinal recommandé
 * @param birthDate - Date de naissance (ISO string)
 * @param vaccines - Liste des vaccins avec leurs règles
 * @returns Calendrier avec dates recommandées et statut
 */
function getVaccinationSchedule(
  birthDate: string,
  vaccines: Vaccine[]
): VaccinationSchedule {
  // TODO: Implémenter
  // Calculer les dates recommandées basées sur la date de naissance
  // Gérer les exceptions (retards, avances)
  // Retourner le calendrier complet
}
```

**Approche suggérée :**
- Calculer l'âge actuel depuis la date de naissance
- Pour chaque vaccin, calculer la date recommandée
- Marquer comme "overdue" si la date est passée
- Marquer comme "upcoming" si dans les prochains mois

### Phase 3 : Full-Stack Feature (25min)

**Feature à ajouter : Système de notifications vaccins**

**Backend (10min) :**
- [ ] Endpoint GET `/api/vaccines/reminders`
- [ ] Logique pour trouver les vaccins en retard
- [ ] Logique pour trouver les vaccins à venir (7 jours)
- [ ] Retourner la liste des rappels

**Frontend (10min) :**
- [ ] Badge de notification avec le nombre de rappels
- [ ] Liste des rappels à afficher
- [ ] Connexion à l'API
- [ ] Mise à jour automatique

**Intégration (5min) :**
- [ ] Tester que les notifications s'affichent
- [ ] Vérifier le badge de notification

### Phase 4 : UI Improvement (10min)

**Améliorations :**
- [ ] Améliorer le calendrier vaccinal (affichage visuel)
- [ ] Ajouter des couleurs pour les statuts (overdue, upcoming, done)
- [ ] Améliorer la lisibilité

---

## 📋 Méthode de Simulation

### Avant chaque simulation :

1. **Préparer l'environnement**
   - [ ] Fermer toutes les distractions
   - [ ] Préparer un chronomètre
   - [ ] Avoir de l'eau à portée
   - [ ] Préparer un bloc-notes pour notes rapides

2. **Rappel des règles**
   - [ ] ZÉRO AI assistant
   - [ ] Timer strict
   - [ ] Pas de documentation excessive (juste la doc officielle si vraiment nécessaire)

### Pendant la simulation :

1. **Gérer le temps**
   - Ne pas rester bloqué >5min sur un problème
   - Passer à la suite si nécessaire
   - Revenir après si tu as le temps

2. **Communiquer (si en entretien réel)**
   - Penser à voix haute
   - Expliquer tes choix
   - Demander des clarifications si besoin

3. **Prioriser**
   - Faire fonctionner d'abord
   - Optimiser après
   - UI improvement = bonus

### Après chaque simulation :

1. **Review immédiate** (10min)
   - [ ] Qu'est-ce qui t'a pris le plus de temps ?
   - [ ] Qu'est-ce qui t'a bloqué ?
   - [ ] Qu'aurais-tu pu faire mieux ?

2. **Notes détaillées** (10min)
   - [ ] Difficultés rencontrées
   - [ ] Patterns à retenir
   - [ ] Points à améliorer

3. **Plan d'action** (5min)
   - [ ] Sur quoi te concentrer pour la prochaine simulation ?
   - [ ] Quels concepts revoir ?

---

## 📋 Template de Review Post-Simulation

**Simulation #X - [Date]**

**Temps par phase :**
- Phase 1 (Exploration) : X min
- Phase 2 (Algorithme) : X min
- Phase 3 (Full-Stack) : X min
- Phase 4 (UI) : X min

**Difficultés rencontrées :**
1. [Description]
2. [Description]

**Ce qui a bien fonctionné :**
1. [Description]
2. [Description]

**Points à améliorer :**
1. [Description]
2. [Description]

**Plan pour la prochaine simulation :**
- [ ] Revoir [concept X]
- [ ] Pratiquer [technique Y]
- [ ] Améliorer [compétence Z]

---

## ✅ Checklist de Validation

À la fin du Jour 10-11, vérifie que tu as :

- [ ] Complété 4 simulations complètes (60min chacune)
- [ ] Respecté le timer strict (pas de temps supplémentaire)
- [ ] Identifié tes difficultés principales
- [ ] Créé un plan d'amélioration
- [ ] Noté les patterns à retenir
- [ ] Gagné en confiance pour l'entretien réel

---

## 🎯 Points Clés à Retenir

1. **Timer = Réalité** : Respecter le temps est crucial en entretien
2. **Communication = Clé** : Expliquer ta pensée aide l'interviewer
3. **Simplicité > Complexité** : Solution qui marche > solution élégante qui bug
4. **Tests = Guidance** : Les tests te guident vers la solution
5. **Review = Amélioration** : Analyser après chaque simulation pour progresser

---

## 🚀 Prochaines Étapes

Jour 12 : Tu vas apprendre les patterns et best practices pour structurer ton code comme un pro. C'est le moment de polir tes compétences !
