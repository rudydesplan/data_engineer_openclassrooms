## P5 — Migration de données médicales en NoSQL (MongoDB + Docker)  
**Titre :** *Migrez des données médicales à l’aide du NoSQL*  
**Repo :** `https://github.com/rudydesplan/P05_mongo_migration`

### Contexte
Une organisation doit migrer un dataset médical initialement fourni en **CSV** vers une base **NoSQL** afin de faciliter l’exploitation, la recherche et l’évolution du modèle. Les contraintes sont classiques “projet infra” : migration automatisée, contrôle qualité, sécurité (authentification + rôles), et surtout **reproductibilité** de l’installation pour pouvoir la livrer/relivrer facilement.

### Objectif
- Migrer un dataset **CSV → MongoDB** de manière automatisée et fiable.
- Mettre en place des **contrôles d’intégrité** avant/après migration (types, doublons, valeurs manquantes).
- Structurer la base pour des usages réels : choix de structure document, **indexation**, typage, performance minimale.
- Sécuriser l’accès via **authentification** et **gestion des rôles**.
- Livrer une solution **portable** et “one-command” via Docker/Docker Compose, avec documentation complète.

### Démarche
- **Analyse du CSV** : compréhension des colonnes, identification des champs critiques, règles de validation (obligatoires, domaines, formats).
- **Script de migration** :
  - lecture/parse du CSV,
  - normalisation et typage,
  - insertion en base (batching si nécessaire),
  - logs des opérations et gestion des erreurs.
- **Contrôles qualité** :
  - pré-migration : détection doublons, valeurs manquantes, types non conformes,
  - post-migration : comptage documents, contrôles d’échantillons, vérification des champs/contraintes attendues.
- **Optimisation MongoDB** :
  - création d’index adaptés aux requêtes (champs de recherche/filtrage),
  - ajustement de la structure si besoin (document vs sous-documents).
- **Sécurité** :
  - activation de l’authentification,
  - création d’utilisateurs et rôles (principe du moindre privilège).
- **Reproductibilité** :
  - docker-compose (MongoDB + volumes),
  - montage du dataset CSV,
  - commandes simples pour build/run/stop,
  - README détaillant installation et exécution.

### Outils
- **MongoDB** (base NoSQL, index, users/roles)
- **Python** (script de migration, contrôles qualité, logs)
- **Docker / Docker Compose** (déploiement reproductible, volumes, configuration)

### Compétences démontrées
- Mise en œuvre NoSQL “production-like” : structure document, indexation, sécurité
- Migration de données : automatisation, robustesse, contrôles avant/après
- Reproductibilité & industrialisation : Docker, exécution “one command”
- Documentation technique : README clair, procédures, schéma, handover

### Résultats & valeur ajoutée (ce que ça apporte)
- Une migration **reproductible, sécurisée et documentée** : démarrage de l’environnement et chargement des données en quelques commandes.
- Une base MongoDB exploitable immédiatement (index + structure cohérente), avec un processus de validation qui réduit les risques de données corrompues.
- Une livraison “handover-ready” : facile à reprendre par une équipe (runbook + doc + scripts).

### Recul (apprentissage clé)
- La réussite d’une migration tient moins à “écrire le script” qu’à construire un **process fiable** : validations systématiques, logs, gestion des erreurs, et environnement stable/rejouable. Sans ça, on obtient une migration “qui marche une fois”, pas un livrable durable.
