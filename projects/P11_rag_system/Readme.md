## P11 — Concevoir & déployer un système RAG (LangChain + Mistral + FAISS)  
**Titre :** *Concevez et déployez un système RAG*  
**Repo :** `https://github.com/rudydesplan/data_engineer_openclassrooms/projects/P11_rag_system`

### Contexte
Mission en scénario professionnel : en tant qu’ingénieur data freelance, vous travaillez pour **Puls-Events**, une plateforme web de découverte d’événements culturels. L’entreprise veut tester l’intégration d’un **chatbot intelligent** capable de recommander des événements et de produire des réponses **augmentées** grâce à un système **RAG** connecté à une base vectorielle, à partir de données publiques **Open Agenda**.

### Objectif
- Réaliser un **POC RAG fonctionnel** présenté aux équipes produit/marketing.  
- S’appuyer sur **Open Agenda** (périmètre géographique au choix) et limiter les événements à **moins d’un an**.  
- Implémenter un système RAG complet :
  - pré-processing + filtrage (région/période),
  - vectorisation + indexation **FAISS**,
  - retrieval + génération via **Mistral** orchestré avec **LangChain**,
  - capacité à **reconstruire l’index** à la demande.  
- Produire les livrables attendus : README reproductible, code versionné, **tests unitaires** (contrôle “< 1 an”), **rapport technique** (5–10 pages), présentation + démo live.

### Démarche
- **Préparation de l’environnement**
  - Création d’un environnement virtuel, installation et vérification de compatibilité des packages (LangChain, FAISS CPU, Mistral).
- **Ingestion & pré-processing Open Agenda**
  - Extraction via API Open Agenda.
  - Filtrage par localisation et période (événements < 1 an + événements à venir) et structuration d’un dataset propre.
  - Tests unitaires pour vérifier que les événements respectent bien les filtres (notamment “moins d’un an”).
- **Vectorisation & indexation (FAISS)**
  - Découpage des textes en **chunks** avant vectorisation.
  - Génération des embeddings et construction de l’index FAISS.
  - Stockage des **métadonnées** (date, lieu, description, etc.) pour permettre un retrieval utile.
  - Tests de recherche (sanity checks) et vérification que tout est indexé.
- **Chaîne RAG (LangChain + Mistral + FAISS)**
  - Orchestration des appels : retrieval FAISS → prompt → génération Mistral.
  - Scénarios de test d’interaction (robustesse, pertinence, formulation).
  - Création d’un **jeu de test annoté** Q/R pour évaluer qualitativement les réponses (comparaison au “même sens / mêmes infos”).
- **Packaging & restitution**
  - Documentation dans un README (objectif, architecture, instructions de reproduction, structure des dossiers).
  - Rapport technique (choix d’architecture, résultats POC, recommandations).
  - Slides (10–15) + démo live.

### Outils
- **Python 3**  
- **Open Agenda API** (source de données)  
- **Pandas** (pré-processing)  
- **Mistral** (embeddings + génération)  
- **FAISS** (index vectoriel)  
- **LangChain** (orchestration RAG)  
- **Tests unitaires Python** (contrôles pipeline & règle “< 1 an”)  

### Compétences démontrées
- Construction d’un pipeline RAG de bout en bout (ingestion → nettoyage → embeddings → index → retrieval → génération)  
- Gestion d’une base vectorielle : chunking, indexation, métadonnées, tests de recherche, reconstruction d’index  
- Qualité & robustesse : tests unitaires intégrés (conformité des données, filtres temporels)  
- Industrialisation “POC sérieux” : reproductibilité (env + README), documentation, présentation et démo  

### Résultats & valeur ajoutée (ce que ça apporte)
- Un **POC RAG fonctionnel** démontrant la faisabilité : recommandations d’événements + réponses augmentées basées sur les événements indexés.  
- Un pipeline **reproductible** (installation, exécution, reconstruction de l’index) facilitant l’évaluation par des équipes produit/marketing et la projection vers un déploiement à plus grande échelle.  
- Une base de discussion “next steps” : limites de FAISS à grande échelle, stratégie d’évaluation, monitoring/performance, recommandations pour une V1 industrialisée.

### Recul (apprentissage clé)
Un RAG “convaincant” dépend autant de la **qualité des données et du retrieval** (filtres, chunking, métadonnées, index bien vérifié) que du LLM. Sans garde-fous (tests, dataset annoté, sanity checks), on peut obtenir des réponses fluides mais peu fiables.
