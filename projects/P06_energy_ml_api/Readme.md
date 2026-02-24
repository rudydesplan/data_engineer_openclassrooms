## P6 — Prédiction conso bâtiments + API (BentoML) + déploiement cloud  
**Titre :** *Anticipez les besoins en consommation de bâtiments*  
**Repo :** `https://github.com/rudydesplan/data_engineer_openclassrooms/projects/P06_energy_ml_api`

### Contexte
La ville de Seattle cherche à mieux anticiper la **consommation énergétique** et les **émissions de CO₂** de bâtiments non résidentiels. Le besoin n’est pas seulement de produire un modèle ML : il faut le rendre **utilisable** par d’autres systèmes via une **API**, assurer la **reproductibilité** (conteneurisation), puis préparer un **déploiement cloud**.

### Objectif
- Construire un modèle de prédiction (consommation / CO₂) à partir de caractéristiques de bâtiments.
- Mettre en place un pipeline de préparation : nettoyage, encodage, feature engineering.
- Comparer plusieurs modèles et sélectionner le meilleur selon des métriques pertinentes et une validation robuste.
- Exposer le modèle via une **API** documentée (Swagger), avec validation d’inputs.
- Conteneuriser et rendre le service **déployable** sur une plateforme cloud.

### Démarche
- **EDA & préparation** :
  - exploration des variables, distributions, outliers,
  - traitement des valeurs manquantes et typage,
  - transformations (scaling/encodage selon besoins).
- **Feature engineering** :
  - création d’au moins **3 features** pertinentes (ratios, surfaces, interactions, agrégations, etc.),
  - justification de leur apport (intuition métier + impact sur performance).
- **Modélisation & sélection** :
  - benchmark de plusieurs algorithmes,
  - **validation croisée** pour limiter l’overfitting,
  - optimisation via **Grid Search**,
  - analyse de **feature importance** (lecture globale du modèle).
- **Industrialisation (serving)** :
  - packaging avec **BentoML** : `service.py`, endpoint de prédiction,
  - tests locaux (requêtes, cas limites),
  - exposition de la doc via **Swagger/OpenAPI**.
- **Qualité des entrées** :
  - validation via **Pydantic/Pandera** (types, champs obligatoires, règles anti-valeurs incohérentes).
- **Conteneurisation & déploiement** :
  - définition `bentofile.yaml`,
  - build image Docker, push vers registry,
  - déploiement sur une plateforme cloud (AWS/GCP/Azure) avec configuration minimale (variables, port, scaling de base).

### Outils
- **Python**
- **scikit-learn** (modèles, pipeline, CV, Grid Search)
- **BentoML** (packaging + API)
- **Pydantic / Pandera** (validation des données d’entrée)
- **Docker** (conteneurisation)
- **Cloud** (AWS / GCP / Azure — selon option de déploiement)

### Compétences démontrées
- Modélisation supervisée appliquée : EDA, feature engineering, validation croisée, tuning
- Interprétabilité “pragmatique” : feature importance et lecture des signaux
- Industrialisation ML : packaging, API, documentation Swagger
- Qualité & robustesse : validation d’inputs, gestion des cas limites
- Déployabilité : Docker + principes de déploiement cloud (image/registry/config)

### Résultats & valeur ajoutée (ce que ça apporte)
- Un service de prédiction **consommable par des non-techniques** (endpoint + Swagger), prêt à être intégré dans un SI.
- Une solution **reproductible** (dépendances packagées, image Docker), limitant le “ça marche chez moi”.
- Une base solide pour itérer : amélioration modèle, ajout de monitoring, versioning du modèle, et gestion du drift.

### Recul (apprentissage clé)
- Construire une API ML “utilisable” impose une vraie rigueur : ordre et définition des features, schéma d’entrée stable, validation stricte, dépendances maîtrisées, et image Docker optimisée. Un bon modèle sans interface robuste reste difficile à exploiter en production.
