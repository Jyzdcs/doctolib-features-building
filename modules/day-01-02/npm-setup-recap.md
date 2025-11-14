# 📦 Récap : Commandes npm/npx & Setup TypeScript

## 🎯 Les Commandes Essentielles

### Setup Initial d'un Projet Node.js + TypeScript

```bash
# 1. Créer le projet
npm init -y                    # Crée package.json avec valeurs par défaut

# 2. Installer les dépendances RUNTIME (production)
npm install express pg dotenv  # → va dans "dependencies"

# 3. Installer les dépendances DEV (développement uniquement)
npm install -D @types/express @types/node @types/pg nodemon typescript
# → va dans "devDependencies"

# 4. Générer tsconfig.json (manuellement, pas automatique)
npx tsc --init                 # Crée le fichier de config TypeScript
```

---

## 💡 Concepts Clés

### `npm` vs `npx`

| Commande | Rôle | Exemple |
|----------|------|---------|
| **`npm`** | **Package manager** : installe/gère les packages | `npm install express` |
| **`npx`** | **Package runner** : exécute un CLI depuis node_modules | `npx tsc --init` |

**Pourquoi `npx` ?**
- Utilise la version **locale** du projet (pas une globale)
- Garantit la même version pour toute l'équipe
- Plus sûr et reproductible

### `dependencies` vs `devDependencies`

| Type | Quand utilisé ? | Exemples |
|------|-----------------|----------|
| **`dependencies`** | Nécessaire en **production** | `express`, `pg`, `dotenv` |
| **`devDependencies`** | Nécessaire seulement en **dev/build** | `typescript`, `nodemon`, `@types/*` |

**Règle d'or :**
- Si ton serveur a besoin de la lib pour **tourner** → `dependencies`
- Si c'est juste pour **coder/compiler/tester** → `devDependencies`

---

## 🔍 Ce que Chaque Commande Fait

### `npm init -y`
- Crée `package.json` avec valeurs par défaut
- **Sans `-y`** : pose des questions interactives
- **Avec `-y`** : skip les questions, utilise les defaults

### `npm install <pkg>`
- **Télécharge** le package dans `node_modules/`
- **Ajoute** le package dans `package.json` → `"dependencies"`
- Les deux actions en même temps (pas juste l'un ou l'autre)

### `npm install -D <pkg>`
- Même chose que `npm install`, mais va dans `"devDependencies"`
- `-D` = `--save-dev` (équivalent)

### `npx tsc --init`
- **N'installe pas** TypeScript (déjà fait avec `npm install -D typescript`)
- **Génère** le fichier `tsconfig.json` avec une config par défaut
- À faire **une seule fois** après avoir installé TypeScript

---

## ✅ Checklist Mental

Quand tu setup un nouveau projet backend TypeScript :

- [ ] `npm init -y` → package.json créé
- [ ] `npm install express pg dotenv` → runtime deps installées
- [ ] `npm install -D typescript @types/* nodemon` → dev deps installées
- [ ] `npx tsc --init` → tsconfig.json généré
- [ ] Vérifier `package.json` : deps au bon endroit
- [ ] Vérifier `node_modules/` : packages présents

---

## 🎯 Réponse Interview-Style

**"Pourquoi tu utilises `npx` au lieu de `npm` pour `tsc` ?"**

> "`npm` est le package manager qui installe les dépendances. `npx` exécute les outils CLI installés localement dans le projet. J'utilise `npx tsc` pour garantir que j'utilise la version de TypeScript définie dans mon `package.json`, pas une version globale qui pourrait différer. C'est plus reproductible et évite les problèmes de version entre développeurs."

---

## 📝 Notes Rapides

- **`npm install`** = installe **ET** déclare dans package.json (les deux)
- **`tsconfig.json`** n'est **PAS** généré automatiquement → besoin de `npx tsc --init`
- **`-D`** = devDependencies (dev only)
- **Sans `-D`** = dependencies (production)
- **`npx`** = exécute depuis node_modules local (pas global)

