## P2 — Analyse de données de systèmes éducatifs  
**Titre :** *Analysez les données de systèmes éducatifs*  
**Repo :** `https://github.com/rudydesplan/data_engineer_openclassrooms/projects/P02_education`

### Contexte
Dans le cadre d’une mission d’exploration de données orientée “décision”, l’objectif est de partir de plusieurs fichiers d’indicateurs (éducation / développement), puis de construire une base d’analyse cohérente et exploitable. Le principal défi est la **multiplicité des indicateurs** (risque de redondance) et la **qualité hétérogène** des données (valeurs manquantes, formats, incohérences).

### Objectif
- Explorer et comprendre un jeu de données multi-indicateurs (pays / années / métriques).
- Mettre en place un processus de **nettoyage** et de **préparation** reproductible.
- Réduire le périmètre à une sélection pertinente d’indicateurs/pays, en évitant les variables redondantes.
- Produire une analyse lisible (data storytelling) soutenue par des visualisations.

### Démarche
- **Audit qualité** : contrôle des doublons, valeurs manquantes, cohérence inter-champs, standardisation des formats.
- **Nettoyage & préparation** : filtrage, typage, gestion des valeurs manquantes, consolidation des tables utiles.
- **Réduction du périmètre (approche métier)** : regroupement par thématiques / familles d’indicateurs afin de cadrer l’analyse.
- **Analyse de redondance** : calcul de corrélations (Pearson/Spearman), visualisation (heatmap), suppression des indicateurs fortement corrélés lorsque cela n’apporte pas d’information supplémentaire.
- **Analyse descriptive & dataviz** : distributions, comparaisons, tendances, automatisation via fonctions/boucles pour itérer rapidement.
- **Reproductibilité** : environnement de travail géré et figé via `pyproject.toml` (Poetry).

### Outils
- **Python**
- **Pandas** (préparation / transformations)
- **Matplotlib / Seaborn** (visualisation)
- **Poetry** (gestion des dépendances et reproductibilité)

### Compétences démontrées
- Nettoyage et préparation de données (data wrangling)
- Analyse exploratoire (EDA) et interprétation de statistiques descriptives
- Réduction de dimension “métier” (sélection/filtrage d’indicateurs)
- Dataviz pour appuyer une décision (storytelling)
- Reproductibilité d’un environnement (gestion de dépendances)

### Résultats & valeur ajoutée (ce que ça apporte)
- Une **shortlist d’indicateurs non redondants** et cohérents, utilisables pour une analyse comparative par pays/années.
- Un pipeline EDA/nettoyage **reproductible** (dépendances contrôlées) permettant de rejouer l’analyse et d’étendre facilement la sélection.
- Une base méthodologique réutilisable pour traiter d’autres datasets multi-indicateurs.

### Recul (apprentissage clé)
- La corrélation est utile pour réduire la redondance, mais elle peut être **trompeuse** si la complétude est faible ou si les données sont mal typées : il faut prioriser les contrôles de qualité (notamment valeurs manquantes) **avant** d’interpréter les corrélations.
