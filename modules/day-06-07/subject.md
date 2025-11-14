# Jour 6-7 : Algorithmes & Data Structures

## 🤖 Règles d'Interaction avec l'IA

**Pour ce module, l'IA est autorisée à :**

- ✅ **Répondre à tes questions conceptuelles** : "Comment fonctionne un hash map ?", "Quelle est la complexité de cet algorithme ?"
- ✅ **Guider ta réflexion** : "Par où commencer pour résoudre ce problème ?", "Quelle structure de données utiliser ?"
- ✅ **Expliquer les algorithmes** : "Comment fonctionne le tri rapide ?", "Pourquoi utiliser deux pointeurs ?"
- ✅ **Suggérer des optimisations** : "Comment améliorer la complexité ?", "Y a-t-il une approche plus efficace ?"

**L'IA n'est PAS autorisée à :**

- ❌ **Résoudre directement** les problèmes LeetCode pour toi (tu dois coder toi-même)
- ❌ **Donner la solution complète** des algorithmes (hints seulement)
- ❌ **Écrire le code** des fonctions d'analyse médicale (tu dois implémenter)

**Si tu es vraiment bloqué (>30min sur un problème) :**
- L'IA peut te donner des hints ou expliquer l'approche générale, mais tu dois coder la solution toi-même.

---

## 🎯 Objectifs du Module

À la fin de ces 2 jours, tu seras capable de :

- Résoudre des problèmes algorithmiques sans aide
- Analyser des données de santé
- Implémenter des fonctions d'analyse médicale
- Écrire des tests Jest
- Pratiquer le TDD (Test-Driven Development)

---

## 📋 Matin (2h chaque jour) - LeetCode/HackerRank

**📚 Documentation utile :**
- [LeetCode](https://leetcode.com/) - Plateforme de problèmes algorithmiques
- [Big-O Cheat Sheet](https://www.bigocheatsheet.com/) - Complexité des algorithmes
- [JavaScript Algorithms](https://github.com/trekhleb/javascript-algorithms) - Implémentations d'algorithmes

### Jour 6 : Arrays & Strings

**Problèmes à résoudre (SANS AI assistant) :**

1. **Two Sum** (Easy)
   - Trouver deux nombres dans un array qui additionnent à une cible
   - Approche : Hash map pour O(n) au lieu de O(n²)

2. **Valid Parentheses** (Easy)
   - Vérifier si les parenthèses sont bien équilibrées
   - Approche : Stack (pile) pour suivre les ouvertures

3. **Longest Substring Without Repeating Characters** (Medium)
   - Trouver la plus longue sous-chaîne sans caractères répétés
   - Approche : Sliding window avec deux pointeurs

4. **Best Time to Buy and Sell Stock** (Easy)
   - Trouver le profit maximum possible
   - Approche : Tracker le minimum et le profit maximum

5. **Contains Duplicate** (Easy)
   - Vérifier si un array contient des doublons
   - Approche : Set pour O(n) au lieu de O(n²)

**Stratégie de résolution :**

1. **Comprendre le problème** (5min)
   - Lire 2-3 fois l'énoncé
   - Identifier les inputs et outputs
   - Trouver des exemples concrets

2. **Réfléchir à l'approche** (10min)
   - Solution naïve d'abord (brute force)
   - Puis optimiser (meilleure complexité)
   - Penser aux edge cases

3. **Coder** (20min)
   - Implémenter la solution
   - Tester avec les exemples fournis
   - Vérifier les edge cases

4. **Optimiser** (5min)
   - Analyser la complexité temporelle et spatiale
   - Chercher des optimisations possibles

**Besoin d'aide ?** Demande à l'IA : "Quelle structure de données utiliser pour Two Sum ?" ou "Comment fonctionne le sliding window ?"

### Jour 7 : Objects/Maps & Sorting

**Problèmes à résoudre (SANS AI assistant) :**

1. **Group Anagrams** (Medium)
   - Grouper les anagrammes ensemble
   - Approche : Utiliser un Map avec clé triée

2. **Top K Frequent Elements** (Medium)
   - Trouver les K éléments les plus fréquents
   - Approche : Map pour compter + tri ou heap

3. **Valid Anagram** (Easy)
   - Vérifier si deux strings sont des anagrammes
   - Approche : Compter les caractères ou trier

4. **Intersection of Two Arrays** (Easy)
   - Trouver l'intersection de deux arrays
   - Approche : Set pour O(n) lookup

5. **Sort Colors** (Medium)
   - Trier un array de 0, 1, 2 en place
   - Approche : Two pointers (Dutch National Flag)

**Concepts à maîtriser :**

- **Hash Maps/Objects** : O(1) lookup, utile pour compter
- **Sets** : Éviter les doublons, O(1) contains
- **Sorting** : Quicksort O(n log n), utile pour beaucoup de problèmes
- **Two Pointers** : Approche efficace pour arrays triés

**Besoin d'aide ?** Demande à l'IA : "Comment utiliser un Map en JavaScript ?" ou "Quelle est la complexité du tri ?"

---

## 📋 Après-midi (2h chaque jour) - Health Data Analysis

**📚 Documentation utile :**
- [WHO Growth Charts](https://www.who.int/tools/child-growth-standards) - Courbes de croissance OMS
- [JavaScript Math](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math) - Fonctions mathématiques
- [Date Manipulation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) - Gestion des dates

### Jour 6 : Analyse de croissance

**Contexte métier :**
Les courbes de croissance permettent de suivre le développement d'un bébé et de détecter d'éventuels problèmes de santé.

**Exercice :**

```typescript
// src/types/health.ts
export interface HealthRecord {
  date: string; // ISO format: "2024-01-15"
  weight: number; // kg
  height: number; // cm
}

// src/services/growthAnalysis.ts

/**
 * Calcule le taux de croissance entre deux mesures
 * @param records - Array de mesures de santé
 * @returns Array de taux de croissance en pourcentage
 */
export function calculateGrowthRate(records: HealthRecord[]): number[] {
  // TODO: Implémenter
  // Retourner un array de pourcentages de croissance
  // Exemple: [5.2, 3.8, 4.1] signifie +5.2%, +3.8%, +4.1%
}

/**
 * Détecte les valeurs anormales (hors des percentiles normaux)
 * @param records - Array de mesures
 * @returns Array d'indices des records anormaux
 */
export function findAnomalies(records: HealthRecord[]): number[] {
  // TODO: Implémenter
  // Considérer comme anormal si :
  // - Poids < 2.5kg ou > 6kg pour un bébé de 0-6 mois
  // - Taille < 45cm ou > 70cm pour un bébé de 0-6 mois
  // - Croissance négative entre deux mesures
}

/**
 * Prédit la prochaine valeur en utilisant une régression linéaire simple
 * @param records - Array de mesures
 * @returns Prédiction pour le prochain mois
 */
export function predictNextValue(records: HealthRecord[]): { weight: number; height: number } | null {
  // TODO: Implémenter
  // Utiliser une régression linéaire simple (y = mx + b)
  // Retourner null si pas assez de données (< 2 points)
}
```

**Approche suggérée :**

1. **calculateGrowthRate** :
   - Parcourir les records par paires
   - Calculer : `((nouveau - ancien) / ancien) * 100`
   - Gérer le cas où il n'y a qu'un seul record

2. **findAnomalies** :
   - Définir des seuils (min/max) selon l'âge
   - Vérifier chaque record contre ces seuils
   - Vérifier aussi la croissance négative

3. **predictNextValue** :
   - Calculer la moyenne des différences (slope)
   - Appliquer la régression linéaire
   - Retourner la prédiction

**Tests à écrire d'abord (TDD) :**

```typescript
// src/services/__tests__/growthAnalysis.test.ts
import { calculateGrowthRate, findAnomalies, predictNextValue } from "../growthAnalysis";

describe("calculateGrowthRate", () => {
  it("should calculate growth rate correctly", () => {
    const records = [
      { date: "2024-01-15", weight: 4.2, height: 52 },
      { date: "2024-02-15", weight: 4.8, height: 55 },
    ];
    const rates = calculateGrowthRate(records);
    expect(rates[0]).toBeCloseTo(14.3, 1); // ~14.3% de croissance poids
  });

  it("should return empty array if less than 2 records", () => {
    const records = [{ date: "2024-01-15", weight: 4.2, height: 52 }];
    expect(calculateGrowthRate(records)).toEqual([]);
  });
});

// ... autres tests
```

**Besoin d'aide ?** Demande à l'IA : "Comment calculer un pourcentage de croissance ?" ou "Comment implémenter une régression linéaire simple ?"

### Jour 7 : Calendrier vaccinal

**Contexte métier :**
Le calendrier vaccinal permet de suivre les vaccinations recommandées et de détecter les retards.

**Exercice :**

```typescript
// src/types/vaccine.ts
export interface Vaccine {
  name: string;
  recommendedAgeMonths: number; // Âge recommandé en mois
  given: boolean; // Si le vaccin a été administré
  givenDate?: string; // Date d'administration si donné
}

// src/services/vaccinationSchedule.ts

/**
 * Trouve les vaccins en retard
 * @param vaccines - Liste des vaccins
 * @param babyAgeMonths - Âge actuel du bébé en mois
 * @returns Array de noms de vaccins en retard
 */
export function getOverdueVaccines(vaccines: Vaccine[], babyAgeMonths: number): string[] {
  // TODO: Implémenter
  // Un vaccin est en retard si :
  // - recommendedAgeMonths < babyAgeMonths
  // - given === false
}

/**
 * Trouve les vaccins à venir dans les X prochains mois
 * @param vaccines - Liste des vaccins
 * @param babyAgeMonths - Âge actuel
 * @param monthsAhead - Nombre de mois à regarder
 * @returns Array de vaccins à venir
 */
export function getUpcomingVaccines(
  vaccines: Vaccine[],
  babyAgeMonths: number,
  monthsAhead: number
): Vaccine[] {
  // TODO: Implémenter
  // Un vaccin est à venir si :
  // - recommendedAgeMonths >= babyAgeMonths
  // - recommendedAgeMonths <= babyAgeMonths + monthsAhead
  // - given === false
}

/**
 * Calcule le pourcentage de complétude vaccinale
 * @param vaccines - Liste des vaccins
 * @returns Pourcentage entre 0 et 100
 */
export function calculateVaccinationCompleteness(vaccines: Vaccine[]): number {
  // TODO: Implémenter
  // Pourcentage = (vaccins donnés / total vaccins) * 100
}
```

**Approche suggérée :**

1. **getOverdueVaccines** :
   - Filtrer les vaccins non donnés
   - Vérifier si l'âge recommandé est passé
   - Retourner les noms

2. **getUpcomingVaccines** :
   - Filtrer les vaccins non donnés
   - Vérifier si l'âge recommandé est dans la fenêtre
   - Retourner les vaccins complets

3. **calculateVaccinationCompleteness** :
   - Compter les vaccins donnés
   - Diviser par le total
   - Multiplier par 100

**Tests à écrire d'abord (TDD) :**

```typescript
describe("getOverdueVaccines", () => {
  it("should find overdue vaccines", () => {
    const vaccines = [
      { name: "BCG", recommendedAgeMonths: 0, given: true },
      { name: "DTP", recommendedAgeMonths: 2, given: false },
    ];
    const overdue = getOverdueVaccines(vaccines, 3);
    expect(overdue).toContain("DTP");
  });
});

// ... autres tests
```

**Besoin d'aide ?** Demande à l'IA : "Comment filtrer un array en JavaScript ?" ou "Comment calculer un pourcentage ?"

---

## 📋 Soir (1h) - Tests Jest

**📚 Documentation utile :**
- [Jest Documentation](https://jestjs.io/docs/getting-started) - Guide Jest
- [TDD Principles](https://www.freecodecamp.org/news/test-driven-development-what-it-is-and-what-it-is-not-41fa6bca02a2/) - Principes TDD

**Apprendre TDD : Test d'abord, puis implémentation**

**Workflow TDD :**

1. **Red** : Écrire un test qui échoue
2. **Green** : Écrire le code minimum pour faire passer le test
3. **Refactor** : Améliorer le code sans casser les tests

**Exemple pratique :**

```typescript
// 1. Écrire le test d'abord (RED)
describe("calculateGrowthRate", () => {
  it("should return empty array for empty input", () => {
    expect(calculateGrowthRate([])).toEqual([]);
  });
});

// 2. Implémenter le minimum (GREEN)
export function calculateGrowthRate(records: HealthRecord[]): number[] {
  if (records.length < 2) return [];
  // ... implémentation minimale
}

// 3. Ajouter plus de tests, puis refactoriser
```

**Avantages du TDD :**

- Code plus testable (tu penses aux tests avant)
- Meilleure couverture de tests
- Confiance pour refactoriser
- Documentation vivante (les tests documentent le comportement)

**Besoin d'aide ?** Demande à l'IA : "Comment écrire un test Jest ?" ou "Quelle est la structure d'un test TDD ?"

---

## ✅ Checklist de Validation

À la fin du Jour 6-7, vérifie que tu peux :

- [ ] Résoudre 5 problèmes LeetCode sans aide
- [ ] Implémenter des fonctions d'analyse de données de santé
- [ ] Écrire des tests Jest pour tes fonctions
- [ ] Pratiquer le TDD (test d'abord, puis code)
- [ ] Comprendre la complexité temporelle et spatiale

---

## 🎯 Points Clés à Retenir

1. **Algorithms = Patterns** : Apprendre les patterns (two pointers, sliding window, hash maps)
2. **Data Structures = Performance** : Choisir la bonne structure pour la bonne complexité
3. **TDD = Qualité** : Tests d'abord = code plus robuste
4. **Health Data = Précision** : Les calculs médicaux doivent être précis et validés
5. **Practice = Mastery** : Plus tu pratiques, plus tu deviens rapide

---

## 🚀 Prochaines Étapes

Jour 8-9 : Tu vas apprendre à tester ton code backend et frontend, et à débugger efficacement sans AI. C'est crucial pour devenir autonome !
