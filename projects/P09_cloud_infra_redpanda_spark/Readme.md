## P9 — Modéliser une infrastructure cloud + streaming (Redpanda) + PySpark  
**Titre :** *Modélisez une infrastructure dans le cloud*  
**Repo :** `https://github.com/rudydesplan/data_engineer_openclassrooms/projects/P09_cloud_infra_redpanda_spark`

---

### Exercice 1 — Infrastructure hybride (design cloud + SI existant)

### Contexte
Une organisation souhaite moderniser son système data en s’appuyant sur le cloud, tout en conservant une partie de son SI existant (on-prem / legacy). L’enjeu est de concevoir une **architecture hybride** réaliste, sécurisée et exploitable, qui prend en compte les contraintes d’intégration (réseau, IAM, gouvernance, coûts et exploitation).

### Objectif
- Concevoir une **architecture cloud hybride** intégrable à un SI existant, en justifiant les choix de composants (stockage, entrepôt, streaming, sécurité) et les flux.
- Produire un **schéma d’architecture** clair : connexions, circulation des données, zones de sécurité, points de synchronisation, protocoles de transfert.
- Vérifier l’adéquation aux contraintes : **scalabilité**, **résilience**, **gouvernance**, **coûts**, **exploitation** (run/monitoring).

### Démarche
- **Cadrage** : identification des sources, des consommateurs, des exigences (sécurité, volumétrie, latence, SLA).
- **Choix des composants** : stockage objet, entrepôt, streaming, IAM, réseau, secrets, monitoring (avec justification).
- **Définition des flux** : ingestion (batch/stream), transformations, exposition, gestion des accès et chiffrement.
- **Architecture réseau & sécurité** : segmentation, règles d’accès, principes du moindre privilège, gestion des secrets.
- **Run & observabilité** : logs, métriques, alertes, points de reprise, stratégie incident.
- **Analyse des risques & coûts** : SPOF, dépendances, points de contention, estimation qualitative des coûts (stockage, transfert, compute).

### Outils
- Outils de diagrammes : **Mermaid**, draw.io, Lucidchart *(selon le choix)*
- Référentiels cloud **AWS / Azure / GCP** :
  - stockage objet, entrepôt, IAM, réseau (VPC/VNet), secrets,
  - monitoring/observabilité (metrics/logs/traces)

### Compétences démontrées
- Conception d’architecture cloud/hybride (composants, flux, responsabilités)
- Sécurité & gouvernance : IAM, segmentation réseau, secrets, chiffrement, accès
- Pensée “run” : observabilité, résilience, gestion incident, SPOF/blast radius
- Justification technique : trade-offs coûts/performance/simplicité/réversibilité

### Résultats & valeur ajoutée (ce que ça apporte)
- Une architecture hybride **documentée et défendable**, prête à être présentée à une DSI/équipe infra.
- Une vision claire des flux et des responsabilités (où vont les données, qui accède, comment c’est monitoré).
- Un socle de décision pour lancer une implémentation (MVP infra) avec priorités et risques identifiés.

### Recul (apprentissage clé)
- Le design “cloud” n’est pas qu’une liste de services : ce qui fait la différence, c’est la **sécurité des flux**, la **gestion des identités**, la **réversibilité**, et la **capacité d’exploitation** (observabilité + procédures).
- Les schémas doivent être orientés “run” : qui accède à quoi, où sont les logs, quels sont les SPOF, et comment limiter le blast radius en cas d’incident.

---

### Exercice 2 — Streaming + traitement (Redpanda + PySpark + export)

### Contexte
Dans un contexte orienté événements (tickets clients, logs applicatifs, transactions), l’organisation souhaite capter des données en temps (quasi) réel via une plateforme de streaming. L’objectif est de rendre opérationnel un pipeline complet : **production d’événements → ingestion streaming → traitement Spark → export** et documentation, avec une stack reproductible.

### Objectif
- Mettre en place un système de **streaming** opérationnel (Redpanda) avec un topic (ex. `client_tickets`) et un producteur (Python) pour générer/ingérer des événements.
- Développer un traitement **PySpark** (consommation Kafka/Redpanda), appliquer des transformations (nettoyage, typage, agrégations) et produire un dataset exploitable.
- Exporter les résultats (JSON/Parquet/CSV) et rendre le tout **reproductible** (Dockerfile(s) + docker-compose).
- Documenter l’ensemble : architecture, commandes d’exécution, démo/captures, et schéma (Mermaid intégré au README).

### Démarche
- **Mise en place Redpanda** :
  - démarrage de la stack,
  - création du topic,
  - vérification publish/consume.
- **Producer Python** :
  - génération d’événements structurés,
  - envoi au topic,
  - logs et gestion des erreurs.
- **Traitement PySpark** :
  - lecture du flux (Kafka API),
  - parsing/typage, nettoyage, enrichissement,
  - agrégations (KPI, stats), et écriture vers un sink.
- **Export** :
  - écriture en JSON/CSV/Parquet (selon besoin),
  - validation (comptages, schéma output, contrôles simples).
- **Reproductibilité & documentation** :
  - docker-compose (broker + producer + Spark/job + stockage),
  - README : commandes, architecture, schéma Mermaid, preuves (captures/démo).

### Outils
- **Redpanda** (Kafka API compatible) : topics, producer/consumer
- **Python** (producer, génération d’événements)
- **PySpark** (traitements, agrégations, writing sink)
- **Docker / Docker Compose** (reproductibilité de la stack)
- **Formats** : JSON / Parquet / CSV (selon le besoin)
- **Documentation** : Mermaid + README

### Compétences démontrées
- Mise en place d’une chaîne streaming end-to-end (producer → broker → consumer/processing)
- Traitement distribué Spark : parsing, transformations, agrégations, output
- Robustesse pipeline : logs, validation output, reproductibilité Docker
- Bonnes pratiques streaming : offsets, idempotence (concepts), contrats d’événements (schéma)

### Résultats & valeur ajoutée (ce que ça apporte)
- Un pipeline streaming fonctionnel et **rejouable** localement (stack Docker) pour démontrer une architecture temps réel.
- Un dataset agrégé exploitable (export) pouvant alimenter BI ou stockage analytique.
- Une documentation claire pour permettre à une équipe de reprendre et étendre (nouveaux topics, nouveaux consumers, nouveaux sinks).

### Recul (apprentissage clé)
- La difficulté réelle du streaming est l’**opérationnel** : gestion des offsets, idempotence, schema evolution, erreurs de parsing, et observabilité (logs/métriques).
- Le bon réflexe : définir tôt des **contrats d’événements** (schéma, champs obligatoires, types) pour éviter une dette technique côté Spark (casts fragiles, valeurs incohérentes).
- Dockeriser chaque brique (broker, producer, job spark, stockage) accélère énormément la validation et la revue (même environnement pour tout le monde).

