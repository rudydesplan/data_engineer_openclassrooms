## P13 — Passer un système IA du POC au MVP + portfolio  
**Titre :** *Passez votre système IA du POC au MVP et réalisez votre portfolio de Data Engineer*  
**Repo :** `https://github.com/rudydesplan/data_engineer_openclassrooms/projects/P13_mvp_portfolio`

### Contexte
Dans la continuité d’un **POC** déjà validé chez **Puls-Events** (moteur de recherche sémantique + chatbot RAG), l’entreprise souhaite franchir un cap : transformer ce POC en **MVP scalable**, exploitable et démontrable. Le projet doit servir de vitrine professionnelle : capacité à concevoir une solution NLP/RAG “production-ready” (architecture, cloud, coûts, monitoring, plan de livraison) et à la présenter clairement à une audience métier/tech.

### Objectif
- Transformer un POC RAG en **MVP robuste et scalable**.
- Ajouter des fonctionnalités clés attendues par l’équipe technique :
  - **mémoire conversationnelle** (historique utilisateur, personnalisation),
  - **contexte géographique** pour adapter la pertinence des recommandations,
  - **recherche web en temps réel** (capacité d’actualisation),
  - **monitoring de performance** (qualité, latence, satisfaction, usage).
- Produire une **étude de design MVP** complète :
  - analyse & synthèse des besoins,
  - choix d’une solution **cloud** (veille + justification),
  - architecture technique détaillée,
  - **macro backlog**,
  - **estimation des coûts** (build + OPEX).
- Produire un **plan projet** : jalons, échéances, livrables + support de présentation.

### Démarche
- **Cadrage & besoins**
  - Formalisation des besoins (tech + produit) et priorisation (MVP vs nice-to-have).
  - Définition des critères de succès : performance, pertinence, coût, robustesse, maintenabilité.
- **Design d’architecture MVP**
  - Décomposition en composants : ingestion/index, retrieval, LLM gateway, mémoire, recherche web, API/app, observabilité.
  - Stratégie de scalabilité : mise en cache, async, files/queues, séparation online/offline, gestion des versions d’index.
  - Stratégie de sécurité : secrets, IAM, isolation, protection des données utilisateurs.
- **Choix cloud & veille**
  - Comparatif des options (compute, stockage vectoriel, observabilité, coûts).
  - Choix argumenté (trade-offs coût/complexité/performance/lock-in).
- **Macro backlog + plan de delivery**
  - Backlog structuré par “épics” (mémoire, geo-context, web-search, monitoring, plateforme).
  - Jalons (MVP 0 → MVP 1), dépendances, risques, critères d’acceptation.
- **Chiffrage**
  - Estimation **build** (développement + déploiement initial) et **OPEX** (hébergement, monitoring, stockage, appels LLM, scaling).
  - Hypothèses explicites (volumétrie, trafic, fréquence de réindexation).

### Outils
- **Python** (services, pipeline, tests)
- **LangChain** (orchestration RAG)
- **LLM & embeddings** : Mistral (ou équivalent)
- **Vector store** : FAISS (POC) puis option MVP (managed vector DB ou service dédié)
- **API** : FastAPI
- **Observabilité** : logs + métriques + traces (OpenTelemetry, Grafana/Prometheus, CloudWatch, Datadog…)
- **CI/CD** : GitHub Actions
- **Conteneurisation** : Docker
- **Cloud** : AWS / GCP / Azure (selon choix)
- **Recherche web** : intégration d’un module/tooling de web search (selon design)

### Compétences démontrées
- Passage POC → MVP : cadrage, priorisation, scalabilité, run/ops
- Architecture data/IA : composants, flux, stockage, sécurité, versioning
- Project management : macro backlog, jalons, estimation, risques, communication
- Observabilité & qualité : monitoring, SLI/SLO, tests, mesure de performance
- Professionnalisation GitHub : documentation, runbooks, reproductibilité

### Résultats & valeur ajoutée (ce que ça apporte)
- Une vision claire et actionnable d’un MVP : architecture détaillée, backlog priorisé, plan de livraison et chiffrage.
- Un cadre d’exploitation réaliste : monitoring, métriques de performance, gestion des coûts et de la scalabilité.
- Un portfolio “recruteur-friendly” : projet complet démontrant capacité à industrialiser un système IA/RAG.

### Recul (apprentissage clé)
Le passage POC → MVP est surtout une transformation **produit & exploitation** : la performance perçue, la fiabilité, la sécurité, le monitoring et le coût deviennent aussi importants que la qualité des réponses. Un MVP crédible exige des choix d’architecture et des garde-fous (mesure, logs, alertes, limites, versioning) — pas seulement “un RAG qui répond bien”.
