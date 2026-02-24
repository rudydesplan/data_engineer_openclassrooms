## P12 — Gérer un projet d’infrastructure (Sport Data Solution) : pipeline + monitoring + PowerBI  
**Titre :** *Gérez un projet d’infrastructure* (Option B — Mission fictive : *Créez et automatisez une architecture de données*)  
**Repo :** `https://github.com/rudydesplan/data_engineer_openclassrooms/projects/P12_infra_project`

### Contexte
Vous êtes Data Engineer chez **Sport Data Solution**, une start-up qui développe des solutions de monitoring et d’analyse de performance pour sportifs. La cofondatrice (Juliette) souhaite lancer un **POC** pour un système d’avantages salariés lié à la pratique sportive (prime et/ou jours “bien-être”) et créer automatiquement des publications dans un channel **Slack** pour stimuler l’émulation. Le POC doit aussi permettre de cadrer quelles données collecter et de calculer l’impact financier pour l’entreprise.

### Objectif
- Mettre en place un pipeline **end-to-end** : extraction → transformation → chargement → tests qualité → monitoring continu.
- Tester la faisabilité de la solution et cadrer les données RH + données sportives à collecter.
- Calculer l’**impact financier** des avantages (prime et/ou jours “bien-être” selon règles d’éligibilité).
- Produire une restitution complète :
  - support de présentation (architecture, pipeline, monitoring, justification des choix),
  - démonstration live,
  - rapport **PowerBI** avec indicateurs clés,
  - capacité à **rejouer l’historique** si une source ou un paramètre change.

### Démarche
- **Cadrage métier & données**
  - Analyse des sources (RH + activités sportives) et clarification des règles d’éligibilité.
  - Définition des indicateurs : population éligible, taux d’adoption, coût des primes, coût des jours “bien-être”, tendances par période/équipe.
- **Design de l’architecture**
  - Conception d’une architecture robuste et sécurisée (zones/étapes), avec traçabilité des flux et règles de qualité.
  - Stratégie de rejouabilité : relance historique complète (backfill) si données/paramètres changent.
- **Pipeline data**
  - Ingestion des sources RH + sportives.
  - Transformations : nettoyage, normalisation, jointures, application des règles, calcul des avantages (prime / jours).
  - Chargement dans une zone analytique prête pour PowerBI.
- **Automatisation & intégrations**
  - Orchestration (planification, dépendances, relances).
  - Publication automatique dans Slack lors des événements d’activité sportive (trigger/événements).
- **Tests qualité & monitoring**
  - Tests : complétude, unicité, cohérence des jointures, validation des règles d’éligibilité, contrôles sur les montants calculés.
  - Monitoring : logs, métriques, alertes sur échec, volumétrie anormale, dérive des coûts.
- **Restitution**
  - Présentation + démo live.
  - Dashboard PowerBI : KPIs principaux, filtres (période, équipe), vues coût global et par salarié, capacité de replay/backfill.

### Outils
*(libre choix — exemple cohérent avec les attendus du projet)*
- **Orchestration** : Airflow / Prefect
- **Stockage & analytique** : PostgreSQL / DuckDB / BigQuery / Snowflake (selon choix)
- **Transformations** : SQL + Python (pandas/polars)
- **Qualité** : tests SQL, Great Expectations / Soda (optionnel)
- **Monitoring** : logs + métriques
- **BI** : PowerBI
- **Intégration** : Slack API / Webhooks

### Compétences démontrées
- Gestion de projet technique : cadrage, priorisation, restitution et démonstration
- Conception d’architecture data robuste (sécurité, rejouabilité, traçabilité)
- Pipeline end-to-end : ingestion → transformation → chargement → exposition BI
- Data quality “built-in” : tests automatisés et critères d’acceptation
- Monitoring & run : logs, alertes, détection d’anomalies
- Intégration applicative : automatisation Slack + événements

### Résultats & valeur ajoutée (ce que ça apporte)
- Un **POC fonctionnel** démontrant la faisabilité de l’avantage sportif, avec règles et calculs intégrés.
- Des indicateurs clairs sur :
  - éligibilité / adoption,
  - coût des primes,
  - impact des jours “bien-être”,
  - tendances temporelles et comparaisons (équipes, périodes).
- Un pipeline automatisé, monitoré, et **rejouable** (backfill) en cas de modification des sources ou des paramètres.

### Recul (apprentissage clé)
Un projet d’infrastructure “réussi” ne se juge pas seulement sur le pipeline : il se juge sur la capacité à **prouver** la fiabilité (tests), à **opérer** dans la durée (monitoring/alerting), et à **rejouer** l’historique quand le métier change (règles, paramètres, données). C’est ce qui transforme un POC en base crédible pour une V1.
