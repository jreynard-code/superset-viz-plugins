# Documentation des plugins

Ce document décrit chacun des 6 plugins de visualisation inclus dans ce monorepo.

Tous les plugins suivent la même structure interne :

```
plugin-chart-xxx/
├── src/
│   ├── index.ts              ← Point d'entrée, exporte le ChartPlugin
│   ├── [Composant].tsx       ← Composant(s) React du graphique
│   ├── types.ts              ← Types TypeScript
│   └── plugin/
│       ├── index.ts          ← Instanciation du ChartPlugin
│       ├── buildQuery.ts     ← Construction de la requête Superset
│       ├── controlPanel.ts   ← Définition du panneau de configuration Superset
│       └── transformProps.ts ← Transformation des données API → props React
├── test/                     ← Tests Jest
└── package.json
```

---

## plugin-chart-composed

**Package** : `@superset-viz-plugins/plugin-chart-composed`  
**Version** : `0.36.6`  
**Dépendance** : `recharts ^2.15.4`

### Description

Plugin de visualisation **composite** (Composed Chart) qui permet de combiner librement plusieurs types de graphiques sur le même axe. C'est le plugin le plus avancé du monorepo.

### Types de graphiques supportés

| Type | Sous-types disponibles |
|---|---|
| **Bar Chart** | Regular, Stacked, Mixed stacked |
| **Line Chart** | Simple, Smooth, Step |
| **Area Chart** | Simple, Smooth, Step |
| **Scatter Chart** | Circle, Diamond, Square, Cross |
| **Bubble Chart** | Taille des bulles proportionnelle à une métrique |
| **Norm Chart** | Normalisation (barres 100%) |

### Fonctionnalités notables

- **Axe Y secondaire** : possibilité de définir un deuxième axe Y pour certaines métriques
- **Orientation horizontale/verticale** : layout configurable
- **Breakdowns** : découpage des données par dimension (génère des séries multiples)
- **Tooltips personnalisés** : affichage enrichi des valeurs au survol
- **Ticks personnalisés** : labels d'axes avec rotation et formatage configurables
- **Légende positionnelle** : top, right, bottom, left
- **Formatage des nombres** : format D3 configurable par métrique
- **Mode agrégation ou mode raw** : requête SQL adaptée selon le mode
- **Tri des barres** : ordre d'affichage configurable

### Composants React

- `ComposedChart.tsx` : composant principal, orchestre tous les sous-composants recharts
- `ComposedBar.tsx` : rendu des barres avec logique de stacking
- `ComposedNorm.tsx` : rendu des barres normalisées 100%
- `ComposedChartTick.tsx` : tick personnalisé pour les axes
- `ComposedChartTooltip.tsx` : tooltip personnalisé
- `ScatterChartTooltip.tsx` : tooltip spécifique au mode scatter/bubble

---

## plugin-chart-waterfall

**Package** : `@superset-viz-plugins/plugin-chart-waterfall`  
**Version** : `0.15.1`  
**Dépendance** : `recharts ^2.15.4`

### Description

Plugin de **graphique en cascade** (Waterfall Chart). Ce type de graphique représente des variations successives d'une valeur (gains et pertes), chaque barre partant du niveau d'arrivée de la précédente.

### Fonctionnalités

- Barres positives (gains) et négatives (pertes) avec couleurs différenciées
- Barre de total calculée automatiquement
- **Ticks personnalisés** sur l'axe X
- **Tooltips** affichant la valeur delta et la valeur cumulée
- Légende configurable (position top, right, bottom, left)
- Formatage des nombres via format D3

### Composants React

- `WaterfallChart.tsx` : composant principal basé sur `BarChart` recharts
- `WaterfallBar.tsx` : logique de rendu d'une barre cascade (calcul des offsets)
- `WaterfallTick.tsx` : tick d'axe personnalisé
- `WaterfallTooltip.tsx` : tooltip personnalisé

---

## plugin-chart-bubble

**Package** : `@superset-viz-plugins/plugin-chart-bubble`  
**Version** : `0.0.3`  
**Dépendance** : `recharts ^2.15.4`

### Description

Plugin de **graphique à bulles** (Bubble Chart) indépendant (distinct du mode bubble du plugin composed). Représente 3 dimensions de données : axe X, axe Y, et taille des bulles (axe Z).

### Fonctionnalités

- Configuration des axes X, Y et Z (taille des bulles)
- Labels configurables sur les axes
- Couleurs par catégorie (color namespace Superset)
- Formatage des nombres sur les axes
- Légende positionnelle
- Tooltip personnalisé

### Composants React

- `BubbleChart.tsx` : composant principal basé sur `ScatterChart` recharts
- `BubbleTick.tsx` : tick d'axe personnalisé
- `BubbleTooltip.tsx` : tooltip personnalisé

---

## plugin-chart-pie

**Package** : `@superset-viz-plugins/plugin-chart-pie`  
**Version** : `0.10.1`  
**Dépendance** : `recharts ^2.15.4`

### Description

Plugin de **graphique en secteurs / camembert** (Pie Chart). Alternative au pie chart natif de Superset avec des fonctionnalités supplémentaires.

### Fonctionnalités

- **Groupement par dimensions** : découpage des données selon une ou plusieurs colonnes
- **Active shape** : interaction au clic/survol sur un secteur (affichage d'un secteur développé)
- Légende positionnelle configurable (top, right, bottom, left)
- Formatage des nombres
- Couleurs par catégorie (color namespace Superset)
- Multi-séries : plusieurs camemberts côte à côte selon un critère de groupement

### Composants React

- `PieChart.tsx` : composant principal basé sur `PieChart` recharts
- `utils.tsx` : helpers pour le rendu de l'active shape, du calcul de légende et des positions

---

## plugin-chart-pivot-table

**Package** : `@superset-viz-plugins/plugin-chart-pivot-table`  
**Version** : `0.14.0`  
**Pas de dépendance recharts** (composant tabulaire pur)

### Description

Plugin de **tableau croisé dynamique** (Pivot Table). Affiche des données sous forme de tableau avec des lignes et des colonnes définies par des dimensions, et des valeurs calculées (métriques) à l'intersection.

### Fonctionnalités

- **Axes Rows et Columns** : configuration libre des dimensions en ligne et en colonne
- **Métriques multiples** : plusieurs métriques affichées par cellule
- **Totaux** : lignes et colonnes de total calculées (configurable)
- **Vue compacte** : mode compact pour densifier l'affichage
- **Valeur nulle** : placeholder configurable pour les cellules vides
- **Scroll** : tableau avec scroll horizontal et vertical si les données dépassent la taille du widget

### Composants React

- `PivotTable.tsx` : composant principal, layout général du tableau
- `ColumnsHeader.tsx` : en-tête des colonnes
- `RowsHeader.tsx` : en-tête des lignes
- `HeaderOfHeader.tsx` : coin supérieur gauche du tableau (zone de croisement)
- `TotalColumn.tsx` : colonne de total
- `Layout.ts` : système de grille CSS utilisé pour positionner les cellules

---

## plugin-chart-status

**Package** : `@superset-viz-plugins/plugin-chart-status`  
**Version** : `0.4.0`  
**Pas de dépendance recharts** (composant d'affichage pur)

### Description

Plugin de **visualisation de statut** (Status Chart). Affiche une valeur de statut textuelle associée à un objet, avec une couleur configurable par statut. Idéal pour les tableaux de bord de monitoring.

### Fonctionnalités

- **Colonne objet** : affiche le nom de l'objet surveillé
- **Colonne statut** : affiche la valeur du statut avec sa couleur associée
- **Mapping couleur/statut** : configuration d'une map `{statut: couleur}` (jusqu'à N couleurs configurables)
- **Filtres additionnels** : filtres Superset supplémentaires pour la colonne objet

### Composants React

- `StatusChart.tsx` : composant principal, affichage centré du nom de l'objet et de son statut coloré

---

## Package partagé — `@superset-viz-plugins/core`

**Version** : `0.1.2`  
**Localisation** : `packages/core/`

Package de composants et utilitaires partagés entre les plugins. Il peut exposer des composants React communs (ex. styled components) réutilisés dans plusieurs plugins.

---

## Tableau récapitulatif

| Plugin | Type de visualisation | Librairie de rendu | Tests |
|---|---|---|---|
| `plugin-chart-composed` | Multi-types (bar, line, area, scatter, bubble, norm) | recharts | ✅ |
| `plugin-chart-waterfall` | Cascade | recharts | ✅ |
| `plugin-chart-bubble` | Bulles 3D | recharts | ✅ |
| `plugin-chart-pie` | Secteurs / camembert | recharts | ✅ |
| `plugin-chart-pivot-table` | Tableau croisé | CSS Grid custom | ✅ |
| `plugin-chart-status` | Indicateur de statut | styled-components | ✅ |
