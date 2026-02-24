## P8 — Construire & tester une infrastructure data (Airbyte, Mongo/DocumentDB, monitoring)  
**Titre :** *Construisez et testez une infrastructure de données*  
**Repo :** `https://github.com/rudydesplan/P08_data_infra_testing`

### Contexte
Une DSI souhaite mettre en place une infrastructure de gestion de données **fiable** et **contrôlable**, intégrant des contraintes opérationnelles : déploiement reproductible (Docker), stockage NoSQL managé (AWS DocumentDB / MongoDB), ingestion automatisée (Airbyte), et surtout une logique de **tests** et de **monitoring** permettant de valider que l’infrastructure est réellement exploitable (disponibilité, qualité, réplication).

### Objectif
- Mettre en place une infrastructure data complète : **modélisation → ingestion → stockage → validation → monitoring**.
- Assurer l’automatisation de l’ingestion (Airbyte) vers un stockage NoSQL (MongoDB/DocumentDB).
- Définir et exécuter des **tests de fonctionnement** :
  - accessibilité du service,
  - validité des documents vs schéma attendu,
  - cohérence des mises à jour et vérification de la réplication.
- Documenter l’architecture, le process, et fournir des preuves (captures, résultats de tests, logs).

### Démarche
- **Modélisation** :
  - définition d’un schéma cible (structure document / champs attendus),
  - standardisation pour intégrer plusieurs sources dans une collection cohérente.
- **Mise en place de l’ingestion** :
  - configuration d’Airbyte (source → destination),
  - paramétrage des syncs (full/incremental selon le cas),
  - validation du mapping vers le schéma cible.
- **Déploiement & reproductibilité** :
  - environnement Docker/Docker Compose pour exécuter les composants (Airbyte + services nécessaires),
  - configuration des connexions et des variables.
- **Tests & validation** (scripts Python) :
  - test d’accessibilité (latence/disponibilité),
  - mesure du taux de documents invalides vs schéma / règles (champs manquants, types incohérents),
  - test de mise à jour + vérification de la réplication (avant/après).
- **Monitoring & observabilité** :
  - consultation des métriques/logs (MongoDB/DocumentDB),
  - vérification via dashboards AWS (si DocumentDB),
  - collecte de preuves (captures + logs pertinents).
- **Documentation** :
  - logigramme “collecte → transformation → stockage → tests → monitoring”,
  - procédure d’exécution et interprétation des tests.

### Outils
- **Airbyte** (connecteurs, synchronisations)
- **MongoDB / AWS DocumentDB** (stockage, réplication, supervision)
- **Docker / Docker Compose** (environnement reproductible)
- **Python** (scripts de tests, contrôles qualité, automatisation)
- **Monitoring & logs** (dashboards AWS / logs MongoDB, selon plateforme)

### Compétences démontrées
- Conception d’infrastructure data (chaîne complète ingestion → stockage → validation)
- Paramétrage d’outils d’ingestion (Airbyte) et compréhension des modes de sync
- Tests “infra + data” : disponibilité, qualité documentaire, réplication
- Observabilité : lecture de logs, métriques, validation opérationnelle
- Reproductibilité : Dockerisation, documentation, runbook

### Résultats & valeur ajoutée (ce que ça apporte)
- Une infrastructure démontrée comme **fonctionnelle** (preuves par tests), et pas seulement “installée”.
- Un cadre de validation réutilisable : scripts et méthodologie permettant d’auditer la qualité des syncs et la stabilité de la plateforme.
- Une base exploitable pour passer en exploitation : monitoring + contrôles qualité + doc de prise en main.

### Recul (apprentissage clé)
- “Ça marche” ne suffit pas : une infra data doit prouver sa **disponibilité**, la **validité** des données qu’elle ingère, et sa **résilience** (ex. réplication). Les tests automatisés + l’observabilité transforment un POC technique en socle exploitable en production.
