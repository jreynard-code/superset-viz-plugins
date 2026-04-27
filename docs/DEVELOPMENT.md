# Guide de développement

## Prérequis

- **Node.js** >= 18.0.0 (LTS recommandé : 20.x)
- **Yarn** >= 1.22.0 (Yarn Classic, pas Yarn Berry)

Vérification des versions :
```bash
node --version   # >= 18.0.0
yarn --version   # >= 1.22.0
```

---

## Installation

### 1. Cloner le dépôt

```bash
git clone https://github.com/nielsen-oss/superset-viz-plugins.git
cd superset-viz-plugins
```

### 2. Installer les dépendances

```bash
yarn install
```

Yarn Workspaces installe toutes les dépendances en une seule commande : les `devDependencies` partagées à la racine et les `dependencies` de chaque plugin sont hoistées dans `node_modules/` à la racine.

> ⚠️ **Ne pas utiliser `npm install`** : le projet utilise Yarn Workspaces, npm ne gère pas correctement les workspaces dans cette configuration.

---

## Build

### Build complet (tous les plugins)

```bash
yarn build
```

Cette commande exécute via Lerna sur les 6 plugins :
1. `build:prod` — Transpilation Babel vers `dist/` (CommonJS)
2. `build:esm` — Transpilation Babel vers `esm/` (ES Modules)

### Build d'un seul plugin

```bash
yarn workspace @superset-viz-plugins/plugin-chart-composed run build:prod
```

### Build en mode développement (avec watch)

```bash
yarn workspace @superset-viz-plugins/plugin-chart-composed run build:watch
```

### Nettoyage des artefacts de build

```bash
yarn clean
```

Supprime les dossiers `dist/`, `esm/` et `tsconfig.tsbuildinfo` de tous les plugins.

---

## Tests

### Lancer tous les tests

```bash
yarn jest
```

### Lancer les tests de tous les packages modifiés depuis master

```bash
yarn test
```

> Cette commande utilise `lerna run test --since origin/master` : seuls les packages ayant des commits différents de `master` seront testés.

### Lancer les tests d'un seul plugin

```bash
yarn workspace @superset-viz-plugins/plugin-chart-composed run test
```

Ou directement avec Jest :
```bash
cd plugins/plugin-chart-composed
npx jest --coverage --verbose
```

### Mode watch (développement TDD)

```bash
yarn workspace @superset-viz-plugins/plugin-chart-composed run tdd
```

### Options utiles Jest

```bash
# Relancer seulement les tests en échec
npx jest --onlyFailures

# Filtrer par nom de test
npx jest --testNamePattern="plugin-chart-composed"

# Mettre à jour les snapshots
npx jest --updateSnapshot
```

### Configuration Jest

- **Config racine** : `jest.config.js` → délègue à chaque `plugins/*/jest.config.js`
- **Config partagée** : `jest.config.base.js` (environnement jsdom, transformers, aliases)
- **Setup** : `setupJest.js` → importe `@testing-library/jest-dom` et configure les traductions Superset
- **Environnement** : `jsdom` (simulation du DOM navigateur)
- **Transformers** :
  - `ts-jest` pour les fichiers `.ts/.tsx`
  - `jest-svg-transformer` pour les fichiers `.svg`
  - `jest-transform-stub` pour les assets CSS/images

---

## Lint et formatage

### Vérifier le lint

```bash
yarn lint
```

Lance ESLint sur `plugins/**/*.{ts,tsx}` et `stories/**/*.tsx`.

### Corriger automatiquement

```bash
yarn lint:fix
```

### Prettier

```bash
# Vérification
yarn prettier

# Formatage automatique
yarn format
```

### Configuration

- **ESLint** : `.eslintrc.js` — extend `airbnb`, `prettier`, `plugin:react-hooks/recommended`
- **Prettier** : `.prettierrc.js`
- **TypeScript** : `tsconfig.json` — `strict: true`, target `es2019`, moduleResolution `node`

---

## Storybook

### Lancer Storybook en local

```bash
yarn storybook
```

Démarre Storybook sur http://localhost:6006 avec les stories de tous les plugins.

### Builder Storybook pour le déploiement

```bash
yarn build-storybook
```

### Structure des stories

```
stories/
├── plugin-chart-bubble/      BubbleChart.stories.tsx
├── plugin-chart-composed/    ComposedChart.stories.tsx (+ 5 autres stories)
├── plugin-chart-pie/         PieChart.stories.tsx
├── plugin-chart-pivot-table/ PivotTable.stories.tsx
├── plugin-chart-status/      StatusChart.stories.tsx
└── plugin-chart-waterfall/   WaterfallChart.stories.tsx
```

---

## Ajouter un nouveau plugin

### 1. Créer la structure

Copier un plugin existant comme base :
```bash
cp -r plugins/plugin-chart-status plugins/plugin-chart-nouveau
```

### 2. Mettre à jour `package.json`

```json
{
  "name": "@superset-viz-plugins/plugin-chart-nouveau",
  "version": "0.1.0",
  "pluginName": "NouveauChartPlugin"
}
```

### 3. Implémenter les fichiers requis

| Fichier | Contenu |
|---|---|
| `src/plugin/index.ts` | Instanciation de `ChartPlugin` |
| `src/plugin/buildQuery.ts` | Construction de la requête Superset |
| `src/plugin/controlPanel.ts` | Panneau de configuration Superset |
| `src/plugin/transformProps.ts` | Transformation données API → props React |
| `src/[NouveauChart].tsx` | Composant React de visualisation |
| `src/index.ts` | Export du plugin |

### 4. Enregistrer dans le workspace

Lerna/Yarn détectera automatiquement le nouveau plugin grâce au glob `"plugins/*"` dans `package.json` et `lerna.json`.

```bash
yarn install   # relie les dépendances
```

### 5. Ajouter une story

Créer `stories/plugin-chart-nouveau/NouveauChart.stories.tsx`.

---

## Gestion des versions et releases

### Voir les packages modifiés

```bash
npx lerna changed
```

### Voir le diff des packages modifiés

```bash
npx lerna diff
```

### Créer une version patch manuellement

```bash
yarn ci:create-patch-version
```

### Créer une version minor manuellement

```bash
yarn ci:create-minor-version
```

### Publier depuis un tag existant

```bash
yarn ci:release-from-tag
```

---

## Gestion des commits (Commitizen)

Le projet utilise [Conventional Commits](https://www.conventionalcommits.org/) pour standardiser les messages de commit et permettre la génération automatique de changelogs.

### Commit interactif guidé

```bash
yarn commit
# ou
git add .
npx git-cz
```

### Format de commit manuel

```
<type>(<scope>): <description>

Types : feat, fix, docs, style, refactor, test, chore
Scope : nom du plugin (ex: plugin-chart-composed, plugin-chart-pie)

Exemples :
  feat(plugin-chart-composed): add second Y axis support
  fix(plugin-chart-waterfall): fix bar offset calculation
  chore: upgrade recharts to 2.15.4
```

---

## Architecture des packages npm publiés

Chaque plugin est publié sur npm avec deux entrées :

```json
{
  "main": "lib/index.js",    ← CommonJS (require)
  "module": "esm/index.js"   ← ES Modules (import)
}
```

> **Note** : Le build Babel génère les fichiers dans `dist/` (alias `lib/`). L'entrée `module` en ESM est générée dans `esm/`. Le `files` du `package.json` expose `["esm", "lib"]`.

---

## Dépannage courant

### `babel: not found` au build

Ce problème apparaît si Lerna utilise Nx (qui n'hérite pas du PATH). Vérifier que `lerna.json` contient :
```json
{
  "useNx": false
}
```

### Peer dependency warnings sur `@superset-ui/*`

Ces packages déclarent `react@^16.13.1` comme peerDependency mais le projet utilise React 18. Ces warnings sont normaux et n'impactent pas le build. La correction sera disponible dans une future version de `@superset-ui`.

### Erreur `useWorkspaces` au lancement de Lerna

Lerna v8 a supprimé cette option. S'assurer que `lerna.json` ne contient **pas** `"useWorkspaces": true`.

### Husky ne se déclenche pas

Husky v9 nécessite une initialisation explicite. Après `yarn install` :
```bash
npx husky init
```
Puis recréer les hooks (voir `package.json` section `husky` pour les commandes à reproduire).
