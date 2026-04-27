# Vue d'ensemble du projet

## Qu'est-ce que ce projet ?

Ce dépôt est un **monorepo de plugins de visualisation custom pour Apache Superset**, un outil de Business Intelligence open source.

Il permet à des équipes qui opèrent leur propre instance Superset d'**ajouter des types de graphiques sur-mesure** non disponibles nativement dans Superset, de les versionner sous forme de packages npm, et de les embarquer automatiquement dans une image Docker prête à déployer.

Le dépôt a été initialement créé par les équipes de Nielsen (nielsen-oss). Il est conçu comme un **template repository GitHub** : l'objectif est qu'une équipe crée son propre fork/copie, y ajoute ses plugins, et bénéficie de la chaîne CI/CD intégrée.

---

## Architecture générale

```
superset-viz-plugins/
├── plugins/                  ← Chaque sous-dossier est un plugin npm indépendant
│   ├── plugin-chart-bubble/
│   ├── plugin-chart-composed/
│   ├── plugin-chart-pie/
│   ├── plugin-chart-pivot-table/
│   ├── plugin-chart-status/
│   └── plugin-chart-waterfall/
├── packages/
│   └── core/                 ← Package de composants partagés entre plugins
├── stories/                  ← Exemples Storybook pour chaque plugin
├── scripts/                  ← Scripts de build/release vers Superset
├── docs/                     ← Documentation du projet
├── .github/workflows/        ← CI/CD GitHub Actions
├── package.json              ← Racine du monorepo (devDependencies partagées)
├── lerna.json                ← Configuration Lerna pour la gestion du monorepo
└── tsconfig.json             ← Configuration TypeScript partagée
```

---

## Stack technique

| Outil | Rôle |
|---|---|
| **TypeScript** | Langage principal de tous les plugins |
| **React 18** | Framework UI des composants de visualisation |
| **recharts** | Bibliothèque de graphiques SVG basée sur React |
| **@superset-ui/core** | Types et utilitaires du SDK Superset |
| **@superset-ui/chart-controls** | Construction du panneau de contrôle Superset |
| **Lerna v8** | Orchestration du monorepo (scripts, versions, publication npm) |
| **Yarn Workspaces** | Gestion des dépendances partagées (hoisting) |
| **Babel** | Transpilation TypeScript/TSX → JavaScript |
| **Webpack** | Build de développement pour les stories Storybook |
| **Jest + @testing-library** | Tests unitaires |
| **ESLint + Prettier** | Lint et formatage du code |
| **Storybook** | Documentation visuelle et développement interactif des plugins |
| **Chromatic** | Déploiement des stories Storybook et revue visuelle |

---

## Fonctionnement global — de la source à la production

```
┌─────────────────────────────────────────────────────────────────┐
│  Développeur                                                    │
│  └── crée/modifie un plugin dans plugins/*                      │
└──────────────────────────┬──────────────────────────────────────┘
                           │ git push
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│  GitHub Actions — ci.yml                                        │
│  └── yarn install + yarn build + yarn lint + yarn test          │
└──────────────────────────┬──────────────────────────────────────┘
                           │ (si branche master)
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│  GitHub Actions — release.yml                                   │
│  ├── Build des plugins                                          │
│  ├── Publication des packages sur npm                           │
│  ├── Injection des plugins dans apache/superset                 │
│  └── Build et push d'une image Docker sur DockerHub             │
└─────────────────────────────────────────────────────────────────┘
```

---

## Artefacts produits

| Artefact | Localisation |
|---|---|
| Packages npm | [npmjs.com/@superset-viz-plugins](https://www.npmjs.com/search?q=%40superset-viz-plugins) |
| Image Docker | [hub.docker.com/r/nielsenoss/apache-superset](https://hub.docker.com/r/nielsenoss/apache-superset) |
| Storybook | [5fec4c81935a8c002151e85f.chromatic.com](https://master--5fec4c81935a8c002151e85f.chromatic.com) |

---

## Comment un plugin s'intègre dans Superset

Chaque plugin expose :
1. **Une classe Plugin** (`extends ChartPlugin`) qui enregistre le type de graphique dans le registre Superset
2. **Un `buildQuery`** qui traduit la configuration du panneau de contrôle en requête SQL/API Superset
3. **Un `transformProps`** qui transforme la réponse API en props passées au composant React
4. **Un panneau de contrôle** (`controlPanel`) qui définit les options configurables par l'utilisateur dans l'interface Superset
5. **Un composant React** qui effectue le rendu du graphique

Le script `scripts/prepare_build.sh` injecte automatiquement ces plugins dans le code source de Superset avant le build Docker.

---

## Connexion manuelle à une instance Superset existante

Pour utiliser ces plugins sur une instance Superset existante sans Docker :

1. Remplacer `superset/superset-frontend/webpack.config.js` par le fichier [docs/webpack.config.js](./webpack.config.js)
2. Installer les packages npm des plugins dans le frontend Superset
3. Suivre [ce tutoriel](https://preset.io/blog/2020-07-02-hello-world/) pour enregistrer les plugins dans Superset
