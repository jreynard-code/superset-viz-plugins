# Changelog — Migration vers les versions récentes (Avril 2026)

Le projet n'avait pas été mis à jour depuis 2021. Cette migration remet à niveau l'ensemble des dépendances, des outils de build et des workflows GitHub Actions.

---

## 1. Mise à jour des librairies

### 1.1 Dépendances `@superset-ui`

| Package | Ancienne version | Nouvelle version |
|---|---|---|
| `@superset-ui/core` | `0.18.19` | `0.20.4` |
| `@superset-ui/chart-controls` | `0.18.19` | `0.20.3` |

### 1.2 React et types associés

| Package | Ancienne version | Nouvelle version |
|---|---|---|
| `react` | `^16.13.1` | `^18.3.1` |
| `react-dom` | `^16.13.1` | `^18.3.1` |
| `@types/react` | `^16.9.17` | `^18.3.28` |
| `@types/react-dom` | `^16.9.4` | `^18.3.7` |

> **Impact plugins** : tous les `package.json` des sous-projets ont été mis à jour pour déclarer `react: "^18.3.1"` en `peerDependency`.

### 1.3 TypeScript

| Package | Ancienne version | Nouvelle version |
|---|---|---|
| `typescript` | `^4.0.5` | `^5.8.3` |

### 1.4 Babel

| Package | Ancienne version | Nouvelle version | Notes |
|---|---|---|---|
| `@babel/cli` | `^7.10.4` | `^7.28.0` | |
| `@babel/core` | `7.7.4` | `^7.29.0` | |
| `@babel/preset-typescript` | `^7.10.4` | `^7.28.0` | |
| `@babel/plugin-proposal-nullish-coalescing-operator` | `^7.16.5` | *(supprimé)* | Remplacé |
| `@babel/plugin-transform-nullish-coalescing-operator` | *(nouveau)* | `^7.28.0` | Remplace le `proposal` |
| `@babel/plugin-proposal-decorators` | `^7.16.5` | `^7.29.0` | |
| `babel-jest` | `^24.9.0` | `^29.7.0` | |
| `babel-loader` | `^8.2.3` | `^9.2.1` | |
| `babel-preset-react-app` | `^9.1.0` | `^10.1.0` | |
| `babel-plugin-named-asset-import` | `^0.3.5` | `^0.3.8` | |
| `babel-eslint` | `10.0.3` | *(supprimé)* | Remplacé par `@babel/eslint-parser` |
| `@babel/eslint-parser` | *(nouveau)* | `^7.28.0` | Remplace `babel-eslint` |

### 1.5 Jest et testing

| Package | Ancienne version | Nouvelle version | Notes |
|---|---|---|---|
| `jest` | `^26.6.3` | `^29.7.0` | |
| `ts-jest` | `^26.4.4` | `^29.4.9` | |
| `@types/jest` | `^26.0.15` | `^29.5.14` | |
| `@testing-library/react` | `^11.1.1` | `^16.3.2` | |
| `@testing-library/jest-dom` | `^4.2.4` | `^6.9.1` | |
| `@testing-library/user-event` | `^8.0.3` | `^14.6.1` | |
| `jest-environment-jsdom-fourteen` | `0.1.0` | *(supprimé)* | Obsolète |
| `jest-environment-jsdom` | *(non versionnée)* | `^29.7.0` | Remplace le précédent |
| `jest-resolve` | `24.9.0` | *(supprimé)* | Inclus dans Jest 29 |

### 1.6 ESLint et Prettier

| Package | Ancienne version | Nouvelle version | Notes |
|---|---|---|---|
| `eslint` | `^7.17.0` | `^8.57.1` | |
| `@typescript-eslint/eslint-plugin` | `^4.11.1` | `^8.59.0` | |
| `@typescript-eslint/parser` | `^4.11.1` | `^8.59.0` | |
| `eslint-config-prettier` | `^7.1.0` | `^10.1.8` | |
| `eslint-config-airbnb` | `^18.2.1` | `^19.0.4` | |
| `eslint-config-react-app` | `^5.1.0` | `^7.0.1` | |
| `eslint-plugin-prettier` | `^3.1.2` | `^5.5.5` | |
| `eslint-plugin-react` | `^7.20.6` | `^7.37.5` | |
| `eslint-plugin-react-hooks` | `^4.2.0` | `^4.6.2` | |
| `eslint-plugin-import` | `^2.19.1` | `^2.32.0` | |
| `eslint-plugin-jest` | `^24.1.3` | `^28.14.0` | |
| `eslint-plugin-jsx-a11y` | `^6.2.3` | `^6.10.2` | |
| `eslint-plugin-testing-library` | `^3.10.1` | `^7.16.2` | |
| `eslint-plugin-flowtype` | `^4.5.3` | `^8.0.3` | |
| `eslint-loader` | `3.0.2` | *(supprimé)* | Obsolète |
| `eslint-webpack-plugin` | *(nouveau)* | `^4.2.0` | Remplace `eslint-loader` |
| `prettier` | `^1.19.1` | `^3.5.3` | |

### 1.7 Webpack et build

| Package | Ancienne version | Nouvelle version |
|---|---|---|
| `webpack` | `5` | `^5.98.0` |
| `webpack-cli` | `^3.3.11` | `^5.1.4` |
| `webpack-manifest-plugin` | `^2.2.0` | `^5.0.0` |
| `ts-loader` | `^7.0.5` | `^9.5.7` |
| `url-loader` | `^4.1.0` | `^4.1.1` |
| `clean-webpack-plugin` | `^3.0.0` | `^4.0.0` |
| `tsconfig-paths-webpack-plugin` | `^3.5.2` | `^4.2.0` |
| `fork-ts-checker-webpack-plugin` | `^6.5.0` | `^9.1.0` |
| `thread-loader` | `^2.1.3` | `^4.0.4` |
| `@svgr/webpack` | `^5.5.0` | `^8.1.0` |

### 1.8 Storybook

| Package | Ancienne version | Nouvelle version | Notes |
|---|---|---|---|
| `@storybook/react` | `^6.4.0-beta.11` | `^8.6.18` | |
| `@storybook/addon-actions` | `^6.4.0-beta.11` | `^8.6.18` | |
| `@storybook/addon-essentials` | `^6.4.0-beta.11` | `^8.6.18` | |
| `@storybook/addon-links` | `^6.4.0-beta.11` | `^8.6.18` | |
| `@storybook/builder-webpack5` | `^6.4.0-beta.11` | *(supprimé)* | Intégré dans Storybook 8 |
| `@storybook/manager-webpack5` | `^6.4.0-beta.11` | *(supprimé)* | Intégré dans Storybook 8 |
| `@storybook/storybook-deployer` | `^2.8.10` | `^2.8.16` | |
| `chromatic` | `^5.5.0` | `^16.6.0` | |

### 1.9 Outils divers

| Package | Ancienne version | Nouvelle version |
|---|---|---|
| `lerna` | `^3.22.1` | `^8.2.0` |
| `husky` | `^4.2.5` | `^9.1.7` |
| `lint-staged` | `^10.0.3` | `^15.5.0` |
| `commitizen` | `^4.1.2` | `^4.3.1` |
| `@commitlint/cli` | `^11.0.0` | `^20.5.0` |
| `@commitlint/config-conventional` | `^9.1.2` | `^20.5.0` |
| `@commitlint/config-lerna-scopes` | `^9.1.2` | `^20.4.3` |
| `react-bootstrap` | `^1.4.0` | `^2.10.10` |
| `@emotion/react` | `^11.4.1` | `^11.14.0` |
| `@emotion/styled` | `^11.3.0` | `^11.14.1` |
| `@emotion/jest` | `^11.1.0` | `^11.14.2` |
| `antd` | `^4.16.13` | `^4.24.16` *(v4 LTS)* |

### 1.10 Plugins `recharts`

La dépendance `recharts` de chaque plugin a été mise à jour de la v1 vers la v2 :

| Package | Ancienne version | Nouvelle version | Plugins concernés |
|---|---|---|---|
| `recharts` | `^1.8.5` | `^2.15.4` | bubble, composed, pie, waterfall |

> ⚠️ **Attention** : recharts v2 introduit des changements d'API par rapport à la v1. Les composants sources des plugins peuvent nécessiter des ajustements si certains props ou comportements ont changé.

### 1.11 Engine requirements

| Moteur | Ancienne version | Nouvelle version |
|---|---|---|
| Node.js | `>=10.10.0` | `>=18.0.0` |
| npm | `>=6.8.0` | `>=8.0.0` |
| yarn | `>=1.13.0` | `>=1.22.0` |

---

## 2. Mises à jour de configuration

### 2.1 `babel.config.js` et `.storybook/main.js`

Le plugin Babel `@babel/plugin-proposal-nullish-coalescing-operator` a été renommé dans Babel 7.23+. Toutes les références ont été mises à jour :

```diff
- plugins: ['@babel/plugin-proposal-nullish-coalescing-operator']
+ plugins: ['@babel/plugin-transform-nullish-coalescing-operator']
```

Fichiers modifiés :
- `babel.config.js`
- `.storybook/main.js`

### 2.2 `lerna.json`

**Suppression de `useWorkspaces`** : cette option a été retirée dans Lerna v8. Lerna résout désormais les packages via la configuration `workspaces` du package manager automatiquement.

```diff
- "useWorkspaces": true,
```

**Ajout de `useNx: false`** : Lerna v8 active Nx par défaut pour l'exécution des scripts. Nx ne transmet pas `node_modules/.bin` au `PATH`, ce qui empêchait de trouver l'exécutable `babel`. Désactiver Nx restaure le comportement classique de Lerna.

```diff
+ "useNx": false,
```

---

## 3. Mises à jour des GitHub Actions

### 3.1 `.github/workflows/ci.yml`

| Élément | Avant | Après |
|---|---|---|
| `ubuntu` runner | `ubuntu-18.04` | `ubuntu-latest` |
| `actions/checkout` | `v2` | `v4` |
| `actions/setup-node` | `v2` | `v4` |
| Node.js version | `14` | `20` |

### 3.2 `.github/workflows/release.yml`

| Élément | Avant | Après |
|---|---|---|
| `actions/checkout` (x2) | `v2` | `v4` |
| `actions/setup-node` | `v2` | `v4` |
| Node.js version | `16` | `20` |

### 3.3 `.github/workflows/chromatic.yml`

| Élément | Avant | Après |
|---|---|---|
| `actions/checkout` | `v1` | `v4` |
| `chromaui/action` | `v1` | `v11` |

---

## 4. Points d'attention pour la suite

### Husky v9
Husky v9 a changé la façon dont les hooks sont déclarés. La configuration dans `package.json` (section `husky`) n'est plus supportée. Les hooks doivent migrer vers le dossier `.husky/` :

```bash
npx husky init
```

Puis recréer les hooks :
```bash
echo "npx lint-staged" > .husky/pre-commit
echo "exec < /dev/tty && git cz --hook || true" > .husky/prepare-commit-msg
echo "npx commitlint --edit \$1" > .husky/commit-msg
```

### Storybook 8
Storybook 8 a refondu son architecture. La configuration `.storybook/main.js` devra être mise à jour si Storybook est utilisé activement (la migration vers le format `main.ts` avec le nouveau builder est recommandée).

### recharts v1 → v2
La migration recharts v1 vers v2 est une rupture majeure. L'API des composants (`Tooltip`, custom ticks, props de `<Cell>`, etc.) a été modifiée. Les fichiers sources des plugins devront être vérifiés et ajustés si des erreurs apparaissent à l'exécution.

### `@superset-ui` et React 18
Les packages `@superset-ui/core@0.20.4` et `@superset-ui/chart-controls@0.20.3` déclarent encore `react@^16.13.1` en peerDependency. Ces warnings sont sans impact sur le build mais pourront causer des problèmes à l'exécution si des API React 18 sont utilisées.
