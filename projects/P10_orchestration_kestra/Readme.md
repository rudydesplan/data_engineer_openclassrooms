## P10 — Orchestration des flux (Kestra) + data quality tests (BottleNeck)
**Titre :** *Mettez en place un pipeline d’orchestration des flux* (Option B) :contentReference[oaicite:0]{index=0}  
**Repo :** `https://github.com/rudydesplan/data_engineer_openclassrooms/projects/P10_orchestration_kestra`

### Contexte
Vous êtes Data Engineer chez **BottleNeck** (marchand de vin). Un data analyst (Stéphane) a déjà défini la démarche de nettoyage, réconciliation et analyse des données issues de 2 systèmes (**ERP** et **CMS**) + un fichier de liaison. Le besoin métier est récurrent : fournir **chaque mois** des rapports de chiffre d’affaires et des extractions de vins “premium” vs “ordinaires”. La DSI a choisi **Kestra** pour orchestrer l’automatisation et attend une démonstration au COPIL. :contentReference[oaicite:1]{index=1}

### Objectif
- Concevoir une **architecture d’automatisation** (data lineage + tests) couvrant ingestion → nettoyage → fusion → agrégation → extractions. :contentReference[oaicite:2]{index=2}  
- Implémenter un workflow **Kestra** complet (YAML) incluant scripts SQL/Python :
  - dédoublonnage,
  - suppression des valeurs manquantes,
  - fusion via `liaison.xlsx`,
  - calcul du CA par produit + CA global,
  - détection des vins premium (z-score).   
- Ajouter des **tests automatisés** à chaque étape : doublons, valeurs manquantes, cohérence volumétrie post-jointures, cohérence CA, cohérence z-score.   
- Planifier l’exécution **tous les 15 du mois à 9h** (trigger/cron Kestra). :contentReference[oaicite:5]{index=5}  

### Démarche
- **Conception / data lineage**
  - Schématisation de l’enchaînement des tâches (incluant les tests) : ingestion fichiers `erp.xlsx`, `web.xlsx`, `liaison.xlsx` → nettoyage → jointure → agrégations → exports. :contentReference[oaicite:7]{index=7}  
- **Orchestration nominale sur Kestra**
  - Mise en place du flow YAML : tâches d’exécution SQL (DuckDB) et Python, gestion des outputs (Excel/CSV), branches éventuelles pour premium vs ordinaires. :contentReference[oaicite:8]{index=8}  
- **Implémentation des tests**
  - Ajout de tâches de tests SQL/Python après chaque étape (gates qualité) :
    - absence de doublons,
    - absence de valeurs manquantes,
    - volumétrie cohérente après jointures,
    - cohérence du CA calculé,
    - cohérence premium vs ordinaires (z-score / z-score distribution).   
- **Planification + robustesse**
  - Mise en place du trigger cron (15 du mois, 9h).
  - Prévoir une stratégie en cas d’indisponibilité d’un service tiers (ex. DuckDB) : retry, alerting, stop contrôlé, logs exploitables. :contentReference[oaicite:10]{index=10}  
- **Restitution**
  - Préparation d’un support de présentation : contexte, concepts Kestra, workflow complet, démonstration, tests.   

### Outils
- **Kestra** (orchestration, scheduling via trigger/cron) :contentReference[oaicite:12]{index=12}  
- **DuckDB + SQL** (dédoublonnage, nettoyage, fusion, agrégations CA) :contentReference[oaicite:13]{index=13}  
- **Python** (détection premium via z-score, éventuels tests et contrôles) :contentReference[oaicite:14]{index=14}  
- **Draw.io** (diagramme de flux au format `.drawio`)   

### Compétences démontrées
- Conception d’un workflow d’automatisation (ordonnancement, dépendances, data lineage) :contentReference[oaicite:17]{index=17}  
- Orchestration avec Kestra (tâches, branches, scheduling, exécution & suivi) :contentReference[oaicite:18]{index=18}  
- Intégration de **tests de data quality** dans un pipeline (gates avant mise en prod)   
- Traitement de données SQL/Python (nettoyage, jointure, agrégation, extraction)   
- Communication : vulgarisation + support + démonstration en soutenance/COPIL   

### Résultats & valeur ajoutée (ce que ça apporte)
- Une chaîne **industrialisée** et planifiée, qui produit automatiquement :
  - un rapport CA par produit + CA global (Excel),
  - une extraction vins premium,
  - une extraction vins ordinaires.   
- Un pipeline plus **fiable** grâce aux tests systématiques (doublons, manquants, volumétrie, CA, z-score), réduisant les risques d’envoyer des rapports erronés aux responsables produit.   
- Une solution **présentable** à une audience non-tech (diagramme + démo Kestra + explications).   

### Recul (apprentissage clé)
- L’orchestration “pro” ne consiste pas seulement à enchaîner des tâches : la vraie valeur vient des **garde-fous** (tests de qualité intégrés), de la **planification**, et de la **gestion des pannes** (logs, retry, arrêt contrôlé). Kestra sert d’ossature, mais la robustesse vient surtout de la discipline “data quality + run”. :contentReference[oaicite:25]{index=25}  
