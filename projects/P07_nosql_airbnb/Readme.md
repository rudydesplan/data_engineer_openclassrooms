## P7 — NoSQL (NosCités) : requêtes, BI, réplication & sharding  
**Titre :** *Concevez et analysez une base de données NoSQL*  
**Repo :** `https://github.com/rudydesplan/P07_nosql_airbnb`

### Contexte
L’association NosCités exploite une base MongoDB sur les locations courte durée à Paris. Suite à un incident (crash / indisponibilité), la base doit être **restaurée** et surtout rendue **fiable** et **durable**. En parallèle, l’association a besoin d’analyses pour vérifier la cohérence des données (notamment en vue des **JO 2024**) et produire des statistiques exploitables via un outil BI. Le périmètre s’étend ensuite à une logique multi-sites (Paris + Lyon) avec **réplication** puis **sharding**.

### Objectif
- Restaurer et remettre en service la base MongoDB (import, validation structure).
- Produire des analyses (chiffres clés + analyses avancées) sur les locations et hôtes.
- Mettre à disposition un **dashboard BI** connecté à MongoDB.
- Concevoir une architecture pérenne :
  - **ReplicaSet** pour la haute disponibilité,
  - puis **sharding** pour distribuer la charge / partitionner par ville (Paris/Lyon).

### Démarche
- **Restauration & exploration** :
  - import des données et vérification de l’intégrité (volumes, champs attendus),
  - exploration de la structure des documents (MongoDB Compass).
- **Analyses rapides (CLI)** :
  - requêtes via `mongosh` pour KPI immédiats : nombre d’annonces, hôtes, instant book, superhosts, distributions simples.
- **Analyses avancées (Python)** :
  - extraction ciblée via **PyMongo**,
  - calculs statistiques et agrégations complexes avec **Polars** (médianes, densité par quartiers, taux de réservation, comparaisons).
- **Restitution BI** :
  - connexion à **Power BI** / **Tableau**,
  - création de vues/indicateurs lisibles et d’un dashboard branché sur MongoDB.
- **Pérennisation de l’infra** :
  - intégration Paris + Lyon dans une collection unifiée (standardisation des champs + champ `city`),
  - mise en place d’un **ReplicaSet** (nœuds + arbitre) pour tolérance aux pannes,
  - définition d’une stratégie de **sharding** (clé de partition, typiquement par ville) pour distribution et scalabilité.

### Outils
- **MongoDB** : `mongosh`, MongoDB Compass, import/export
- **Python** : **PyMongo**
- **Polars** : analyses avancées, agrégations et statistiques
- **BI** : **Power BI** ou **Tableau** (connexion MongoDB + dashboard)

### Compétences démontrées
- Data engineering NoSQL “end-to-end” : import → requêtes → analyses avancées → BI
- Requêtage MongoDB (CLI) + extraction applicative (PyMongo)
- Analytique performante avec un dataframe engine (Polars)
- Conception d’architecture MongoDB : **réplication** (HA) et **sharding** (scalabilité/distribution)
- Data storytelling : restitution et indicateurs exploitables en BI

### Résultats & valeur ajoutée (ce que ça apporte)
- Une base MongoDB **remise en état** et validée (structure + cohérence).
- Des KPI et analyses approfondies utilisables par l’association pour piloter et communiquer (JO 2024).
- Un dashboard BI connecté à la base pour accès self-service.
- Une architecture multi-sites plus **résiliente** (ReplicaSet) et plus **scalable** (sharding), limitant les risques de rechute.

### Recul (apprentissage clé)
- MongoDB Query Language est très efficace pour des KPI simples, mais atteint vite ses limites dès qu’on veut des analyses avancées et itératives : la combinaison **MongoDB (stockage + filtres) + Polars (calcul analytique)** est un excellent compromis performance/productivité.
