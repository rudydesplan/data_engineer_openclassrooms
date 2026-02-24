## P11 — Concevoir & déployer un système RAG (LangChain + Mistral + FAISS)  
**Titre :** *Concevez et déployez un système RAG*  
**Repo :** `https://github.com/rudydesplan/data_engineer_openclassrooms/P11_rag_system` *(à remplacer)*

### Contexte
Mission en scénario professionnel : en tant qu’ingénieur data freelance, vous travaillez pour **Puls-Events**, une plateforme web de découverte d’événements culturels. L’entreprise veut tester l’intégration d’un **chatbot intelligent** capable de recommander des événements et de produire des réponses **augmentées** grâce à un système **RAG** connecté à une base vectorielle, à partir de données publiques **Open Agenda**. :contentReference[oaicite:0]{index=0}

### Objectif
- Réaliser un **POC RAG fonctionnel** présenté aux équipes produit/marketing. :contentReference[oaicite:1]{index=1}  
- S’appuyer sur **Open Agenda** (périmètre géographique au choix) et limiter les événements à **moins d’un an**. :contentReference[oaicite:2]{index=2}  
- Implémenter un système RAG complet :
  - pré-processing + filtrage (région/période),
  - vectorisation + indexation **FAISS**,
  - retrieval + génération de réponse via **Mistral** orchestré avec **LangChain**,
  - capacité à **reconstruire l’index** à la demande. :contentReference[oaicite:3]{index=3}  
- Produire les livrables attendus : README reproductible, code versionné, **tests unitaires** (contrôle “< 1 an”), **rapport technique** (5–10 pages), présentation + démo live.   

### Démarche
- **Préparation de l’environnement**
  - Création d’un environnement virtuel, installation et vérification de compatibilité des packages (LangChain, FAISS CPU, Mistral). :contentReference[oaicite:5]{index=5}  
- **Ingestion & pré-processing Open Agenda**
  - Extraction via API Open Agenda.
  - Filtrage par localisation et période (1 an d’historique + événements à venir) et structuration d’un dataset propre.
  - Tests unitaires pour vérifier que les événements respectent bien les filtres (notamment “moins d’un an”).   
- **Vectorisation & indexation (FAISS)**
  - Découpage des textes en **chunks** avant vectorisation.
  - Génération des embeddings (Mistral) et construction de l’index FAISS.
  - Stockage des **métadonnées** (date, lieu, description, etc.) pour permettre un retrieval utile.
  - Tests de recherche (sanity checks) et vérification que tout est indexé. :contentReference[oaicite:7]{index=7}  
- **Chaîne RAG (LangChain + Mistral + FAISS)**
  - Orchestration des appels : retrieval FAISS → prompt → génération Mistral.
  - Scénarios de test d’interaction (robustesse, pertinence, formulation).
  - Création d’un **jeu de test annoté** Q/R pour évaluer qualitativement les réponses (comparaison au “même sens / mêmes infos”).   
- **Packaging & restitution**
  - Documentation dans un README (objectif, architecture, instructions de reproduction, structure des dossiers).
  - Rapport technique (choix d’architecture, modèles, résultats POC, recommandations).
  - Slides (10–15) + démo live.   

### Outils
- **Python 3** :contentReference[oaicite:10]{index=10}  
- **Open Agenda API** (source de données) :contentReference[oaicite:11]{index=11}  
- **Pandas** (pré-processing) :contentReference[oaicite:12]{index=12}  
- **Mistral API / modèle Mistral** (embeddings + génération) :contentReference[oaicite:13]{index=13}  
- **FAISS** (base/index vectoriel) :contentReference[oaicite:14]{index=14}  
- **LangChain** (orchestration RAG) :contentReference[oaicite:15]{index=15}  
- **Tests unitaires Python** (contrôles pipeline & règles “< 1 an”) :contentReference[oaicite:16]{index=16}  

### Compétences démontrées
- Construction d’un pipeline RAG de bout en bout (ingestion → nettoyage → embeddings → index → retrieval → génération) :contentReference[oaicite:17]{index=17}  
- Gestion d’une base vectorielle : chunking, indexation, métadonnées, tests de recherche, reconstruction d’index :contentReference[oaicite:18]{index=18}  
- Qualité & robustesse : tests unitaires intégrés (conformité des données, filtres temporels) :contentReference[oaicite:19]{index=19}  
- Industrialisation “POC sérieux” : reproductibilité (env + README), documentation, présentation et démo   

### Résultats & valeur ajoutée (ce que ça apporte)
- Un **POC RAG fonctionnel** démontrant la faisabilité : recommandations d’événements + réponses augmentées basées sur les événements indexés. :contentReference[oaicite:21]{index=21}  
- Un pipeline **reproductible** (installation, exécution, reconstruction de l’index) facilitant l’évaluation par des équipes produit/marketing et la projection vers un déploiement à plus grande échelle.   
- Une base de discussion “next steps” : limites de FAISS à grande échelle, stratégie d’évaluation, monitoring/performance, recommandations pour une V1 industrialisée. :contentReference[oaicite:23]{index=23}  

### Recul (apprentissage clé)
- Un RAG “convaincant” dépend autant de la **qualité des données et du retrieval** (filtres, chunking, métadonnées, index bien vérifié) que du LLM. Sans garde-fous (tests, dataset annoté, sanity checks), on peut obtenir des réponses fluides mais peu fiables.   
