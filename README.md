# Projet d'Analyse de Données — Sleep Efficiency

**4MA — INSA Toulouse | 2025–2026**

---

## Problématique

> **Quels sont les facteurs comportementaux, physiologiques et démographiques qui structurent la qualité du sommeil, et peut-on identifier des profils typiques de dormeurs ?**

---

## Dataset

- **Source** : [Sleep Efficiency Dataset — Kaggle](https://www.kaggle.com/datasets/equilibriumm/sleep-efficiency)
- **Taille** : 452 individus × 15 variables
- **Variables** : mix de variables quantitatives (âge, durée du sommeil, efficacité, réveils, consommation de caféine/alcool, exercice, pourcentages de sommeil REM/profond/léger) et qualitatives (genre, statut tabagique, heures de coucher/réveil)

---

## Structure du projet

```
.
├── README.md
├── Projet_Analyse_de_données.ipynb   # Notebook principal
└── Sleep_Efficiency.csv              # Jeu de données (à télécharger sur Kaggle)
```

---

## Démarche

### 1. Préparation des données
- Suppression des variables redondantes (`ID`, `Wakeup time`)
- Extraction d'une variable `bedtime_hour` avec normalisation autour de minuit
- Imputation des valeurs manquantes par la médiane (< 6 % de valeurs manquantes sur 4 variables)
- Vérification de l'absence de doublons et des outliers (conservés, car valeurs réelles)

### 2. Analyse bidimensionnelle
- Matrice de corrélation pour identifier les relations entre variables
- Relations clés détectées : forte corrélation négative entre sommeil profond et léger (-0.97), lien entre efficacité du sommeil et sommeil profond et léger

### 3. Réduction de dimension

| Méthode | Description |
|--------|-------------|
| **ACP** | Analyse en Composantes Principales sur les 11 variables quantitatives. PC1 explique ~30 % de la variance et oppose clairement bons et mauvais dormeurs. |
| **FAMD** | Analyse Factorielle des Données Mixtes, intégrant les variables qualitatives (genre, statut tabagique). Confirme et enrichit les résultats de l'ACP. |

### 4. Clustering

| Méthode | Critère de choix de K | K retenu |
|--------|----------------------|----------|
| **K-means** | Méthode du coude + score silhouette | 2 | 
| **CAH** (Ward) | Score silhouette + dendrogramme | 2 | 
| **GMM** | Critère BIC (+ silhouette en confirmation) | 2 |

Les trois méthodes identifient les mêmes deux profils. La similarité entre partitions est mesurée par deux métriques :
- **ARI (Adjusted Rand Index)** : mesure le pourcentage de paires d'individus classées de la même façon par deux méthodes, corrigé par le hasard (1 = partitions identiques, 0 = aléatoire).
- **NMI (Normalized Mutual Information)** : mesure combien la connaissance d'une partition informe sur l'autre (1 = partitions identiques, 0 = aucun lien).

Des scores ARI et NMI proches de 1 entre K-means, CAH et GMM confirment la robustesse de la partition obtenue.
### 5. Approche graphe

| Méthode | Description |
|--------|-------------|
| **k-NN Graph** (k=7) | Graphe de similarité sur variables standardisées. Deux composantes visuellement distinctes. |
| **Spectral Clustering** | K=2 confirmé par l'eigengap. Retrouve la même partition bons/mauvais dormeurs. |
| **SBM** | Détecte 8 sous-communautés, révèle une structure plus fine mais moins interprétable globalement. |

---

## Résultats principaux

L'analyse converge vers **deux profils de dormeurs** robustes, identifiés par toutes les méthodes :

**Cluster 1 — Mauvais dormeurs**
- Efficacité du sommeil faible 
- Sommeil majoritairement léger , peu de sommeil profond 
- Plus de réveils nocturnes 
- Consommation d'alcool plus élevée, moins d'exercice

**Cluster 2 — Bons dormeurs**
- Efficacité du sommeil élevée 
- Sommeil majoritairement profond
- Moins de réveils, moins d'alcool, davantage d'exercice

> L'âge, la durée du sommeil, la consommation de caféine, le genre et le statut tabagique ne se révèlent pas discriminants entre les deux groupes.

---

## Installation et utilisation

### Prérequis

Python 3.9+ et les bibliothèques suivantes :

```bash
pip install numpy pandas matplotlib seaborn scipy scikit-learn prince networkx graspologic
```

### Lancer le notebook

```bash
jupyter notebook Projet_Analyse_de_données.ipynb
```

> **Note** : le fichier `Sleep_Efficiency.csv` doit être placé dans le même répertoire que le notebook avant l'exécution.

---

## Bibliothèques utilisées

- `pandas`, `numpy` — manipulation des données
- `matplotlib`, `seaborn` — visualisation
- `scipy` — statistiques et clustering hiérarchique
- `scikit-learn` — ACP, K-means, CAH, GMM, Spectral Clustering, métriques
- `prince` — ACP et FAMD
- `networkx` — construction et visualisation du graphe
- `graspologic` — Stochastic Block Model (SBM)

---
