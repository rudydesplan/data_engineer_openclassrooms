## P4 — Audit d’un environnement de données (SuperSmartMarket)  
**Titre :** *Auditez un environnement de données* (SuperSmartMarket)  
**Repo :** `https://github.com/rudydesplan/P04_audit_olap`

### Contexte
SuperSmartMarket dispose d’un environnement BI/OLAP alimentant des tableaux de bord (PowerBI). Des **incohérences de chiffre d’affaires (CA)** sont observées entre différentes vues/rapports, ce qui met en risque la prise de décision. La mission consiste à auditer l’écosystème (données + traitements + usages) pour **identifier l’origine probable** des écarts et proposer un plan de stabilisation.

### Objectif
- Réaliser un **audit structuré** de l’environnement data (sources, transformations, stockage, BI).
- Faire du **rétro-engineering** : reconstruire compréhension des tables, relations, règles métiers et flux.
- Prototyper un environnement local pour **rejouer** et **vérifier** les calculs (tests SQL de cohérence).
- Analyser les **logs** / traces d’exécution afin d’expliquer l’écart (ex. événements datés du 15 août).
- Produire des **recommandations actionnables** : correctifs, contrôles, automatisations, gouvernance de changement.

### Démarche
- **Cartographie & compréhension** : inventaire des composants (sources → ETL/ELT → base → PowerBI), identification des points de transformation, schéma global des flux.
- **Documentation fonctionnelle** : création d’un **dictionnaire de données** (définitions, unités, types, règles) + schéma relationnel (tables, clés, relations).
- **Prototype local** : chargement d’un sous-ensemble représentatif des données dans un SGBD local, typage, reconstruction des relations, mise en place de requêtes de contrôle.
- **Tests de cohérence SQL** : recalcul de KPI (CA, volumes, agrégations) et comparaison avec les résultats BI.
- **Analyse des logs** : lecture et interprétation des traces d’insertion/traitements, identification d’événements/anomalies expliquant l’écart (notamment autour du 15 août).
- **Recommandations** : proposition d’actions correctives et préventives (contrôles automatisés, processus de déploiement, conventions de versioning, documentation + veille).

### Outils
- **SQL** (requêtes de contrôle, recalcul KPI, validations)
- **SGBD local** (ex. SQLite / MySQL / PostgreSQL) pour prototypage et rejouabilité
- **Outil de modélisation** *(optionnel)* : SQL Power Architect / draw.io / Mermaid (schéma)
- **PowerBI** *(contexte d’usage)* : compréhension des mesures, filtres, visuels et écarts de restitution

### Compétences démontrées
- Audit data & diagnostic : cadrage, hypothèses, collecte de preuves
- Rétro-engineering : compréhension de modèle, règles métiers, dépendances et flux
- Validation de cohérence : tests SQL, comparaisons, contrôle qualité orienté KPI
- Analyse de logs : interprétation de traces, identification d’incidents/ruptures de flux
- Communication : restitution structurée (support, recommandations, priorisation)

### Résultats & valeur ajoutée (ce que ça apporte)
- Une **explication argumentée** des causes probables d’incohérences (avec éléments vérifiables via requêtes/tests et logs).
- Une base documentaire (dictionnaire + schéma + requêtes) réutilisable pour l’équipe.
- Un plan de stabilisation concret : contrôles de qualité, procédures de changement, et actions d’automatisation pour fiabiliser les chiffres dans PowerBI.

### Recul (apprentissage clé)
- Un audit efficace doit livrer à la fois des **preuves** (tests/requêtes reproductibles, logs) et de la **prévention** (contrôles automatisés, gouvernance de changement, documentation) : corriger sans renforcer le processus mène souvent à reproduire les mêmes écarts.
