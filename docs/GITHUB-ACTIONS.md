# GitHub Actions — Documentation des workflows

Ce projet utilise 3 workflows GitHub Actions automatisés.

---

## 1. `ci.yml` — Workflow d'intégration continue

**Fichier** : `.github/workflows/ci.yml`  
**Déclencheur** : Tout `push` sur n'importe quelle branche

### Objectif

Valider que le code pushé est correct : il se build, passe le lint et passe les tests.

### Étapes

```
1. Checkout du code
2. Installation de Node.js 20 (LTS)
3. yarn install --frozen-lockfile
4. yarn build              ← Build de tous les plugins
5. yarn lint               ← ESLint sur les sources
6. yarn test               ← Tests Jest (seulement les packages modifiés depuis master)
```

### Points importants

- L'option `--frozen-lockfile` garantit que le `yarn.lock` est respecté (pas de mise à jour silencieuse des dépendances en CI)
- `yarn test` utilise `--since origin/master` via lerna pour ne tester que les packages modifiés (optimisation du temps de CI)
- Le `fetch-depth: '0'` est nécessaire pour que lerna puisse calculer les changements par rapport à `origin/master`

### Secrets requis

Aucun secret requis pour ce workflow.

---

## 2. `release.yml` — Workflow de release et déploiement Docker

**Fichier** : `.github/workflows/release.yml`  
**Déclencheur** : `push` sur la branche `master` uniquement

### Objectif

Publier les packages npm avec une nouvelle version, injecter les plugins dans le code source de Superset, et produire une image Docker de Superset incluant les plugins custom.

### Variables d'environnement

| Variable | Valeur par défaut | Description |
|---|---|---|
| `SUPERSET_CHECKOUT_BRANCH` | `1.4` | Branche/tag du repo `apache/superset` à utiliser pour le Docker |
| `PRESET_NAME` | `Nielsen` | Nom du preset de plugins généré dans Superset |
| `PROJECT_WORKING_DIRECTORY` | `superset-viz-plugins` | Répertoire de travail du repo |
| `REPOSITORY_OWNER` | `nielsen-oss` | Propriétaire GitHub du repo |

### Étapes détaillées

```
1. Checkout du repo de plugins (fetch-depth: 0 pour les tags)
2. Fetch des tags git (pour lerna version)
3. Installation de Node.js 20
4. yarn install --frozen-lockfile
5. yarn build
6. Configuration npm/git
   └── Écriture du token npm dans ~/.npmrc
   └── Configuration de l'identité git (action@github.com)
   └── Configuration de la remote origin avec le GITHUB_TOKEN
7. Bump de version et publication npm
   └── yarn prerelease (build des artefacts dist/esm)
   └── yarn ci:release-from-tag (lerna publish depuis le tag courant)
8. Checkout du repo apache/superset (branche 1.4)
9. Préparation du build Superset (script prepare_build.sh)
   └── Copie du .npmrc dans superset-frontend
   └── Ajout des dépendances plugins dans le package.json de Superset
   └── Génération du fichier de preset (NielsenPreset.ts)
   └── Génération du fichier setupPluginsExtra (enregistrement des plugins)
   └── npm install dans superset-frontend
10. Build de l'image Docker et push sur DockerHub
    └── Appel du script docker_build_push.sh
    └── Image taguée : nielsenoss/apache-superset:1.4
```

### Secrets requis

| Secret | Description |
|---|---|
| `NPM_TOKEN` | Token d'accès npm pour publier les packages |
| `GITHUB_TOKEN` | Token GitHub (fourni automatiquement par GitHub Actions) |
| `DOCKERHUB_USERNAME` | Identifiant DockerHub |
| `DOCKERHUB_TOKEN` | Token d'accès DockerHub |

### Script `scripts/prepare_build.sh`

Ce script bash orchestre l'injection des plugins dans Superset :

1. Copie le `.npmrc` dans le répertoire `superset-frontend`
2. Exécute `scripts/addDependencies.js` — ajoute les packages npm des plugins dans le `package.json` de Superset
3. Exécute `scripts/generatePreset.js` — génère un fichier `${PRESET_NAME}Preset.ts` qui enregistre tous les plugins dans Superset
4. Déplace le preset dans `superset/superset-frontend/src/visualizations/presets/`
5. Exécute `scripts/generateSetupPluginsExtra.js` — génère le fichier d'override `setupPluginsExtra`
6. Déplace le fichier dans `superset/superset-frontend/src/setup/`
7. Lance `npm install` dans `superset-frontend` pour installer les packages plugins
8. Exécute `yarn lint-fix` pour corriger les éventuels problèmes de lint

---

## 3. `chromatic.yml` — Déploiement Storybook

**Fichier** : `.github/workflows/chromatic.yml`  
**Déclencheur** : Tout `push` sur n'importe quelle branche

### Objectif

Déployer les stories Storybook sur [Chromatic](https://www.chromatic.com/) pour permettre :
- La visualisation interactive des composants de chaque plugin
- La revue visuelle des changements (détection de régressions visuelles)

### Étapes

```
1. Checkout du code
2. yarn install
3. Déploiement sur Chromatic via chromaui/action@v11
```

### Secrets requis

| Secret | Description |
|---|---|
| `CHROMATIC_PROJECT_TOKEN` | Token du projet Chromatic |
| `GITHUB_TOKEN` | Token GitHub (fourni automatiquement) |

### Résultat

Les stories sont consultables sur :
- Vue stories : `https://master--5fec4c81935a8c002151e85f.chromatic.com`
- Vue librairie : `https://chromatic.com/library?appId=5fec4c81935a8c002151e85f&branch=master`

---

## Récapitulatif

| Workflow | Déclencheur | Durée estimée | Produit |
|---|---|---|---|
| `ci.yml` | Tout push | ~5 min | ✅ Validation build/lint/tests |
| `release.yml` | Push sur `master` | ~20-30 min | npm packages + image Docker |
| `chromatic.yml` | Tout push | ~5 min | Storybook déployé |

---

## Adaptation pour un fork

Si vous avez forké ce repository pour votre propre usage, vous devez :

1. Mettre à jour les variables d'environnement dans `release.yml` :
   - `REPOSITORY_OWNER` : votre organisation GitHub
   - `PRESET_NAME` : le nom de votre preset
   - `REPOSITORY_NAME` dans l'étape Docker : votre repository DockerHub

2. Créer les secrets GitHub dans `Settings > Secrets and variables > Actions` :
   - `NPM_TOKEN`
   - `DOCKERHUB_USERNAME`
   - `DOCKERHUB_TOKEN`
   - `CHROMATIC_PROJECT_TOKEN`
