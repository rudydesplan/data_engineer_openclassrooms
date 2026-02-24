- # Rapport de gestion de projet — Transformation du POC Puls-Events en MVP scalable (RAG + Chatbot)

  ## Introduction

  ### Contexte

  Puls-Events est une plateforme web permettant aux utilisateurs de **découvrir, filtrer et suivre des événements culturels en temps réel**, collectés depuis plusieurs sources (dont **OpenAgenda**) et **personnalisés** selon les préférences (lieu, période, thématiques).

  Le **POC** a déjà démontré la faisabilité :

  - d’un **moteur de recherche sémantique** (embeddings + base vectorielle),
  - d’un **chatbot RAG** (Retrieval-Augmented Generation) capable de recommandations contextualisées.

  ### Objectif du projet

  Industrialiser le POC en un **MVP robuste, scalable et observable**, déployable en production, avec priorités explicites :

  1. **Mémoire conversationnelle** (historique + personnalisation durable)
  2. **Contexte géographique optimisé** (pertinence locale)
  3. **Recherche web en temps réel** (outillage/agent)
  4. **Monitoring de performance** (qualité, latence, satisfaction)

  Contraintes/choix imposés (décision de cadrage) :

  - **AWS** pour l’infra principale, avec **Amazon Bedrock** (LLM managé) + possibilité de modèles open-source
  - **Zilliz Cloud** pour la base vectorielle
  - **Chainlit** pour l’interface chatbot (MVP)
  - Ajouts recommandés : **Guardrails** (fiabilité/sécurité), **observabilité** (LangSmith/Phoenix), **orchestration** (pipelines RAG modulaires)

  ------

  ## 1. Analyse et synthèse des besoins formulés par l’équipe

  ### 1.1 Synthèse : contexte, objectifs métiers, contraintes techniques

  #### Objectifs métiers (ce que le MVP doit prouver)

  - **Conversion & engagement** : l’utilisateur trouve plus vite des événements pertinents → plus de clics, favoris, suivis.
  - **Différenciation produit** : expérience conversationnelle “assistant culturel” vs moteur de recherche classique.
  - **Scalabilité & fiabilité** : éviter “démo qui marche” mais fragile (données incomplètes, hallucinations, latence).
  - **Boucle d’amélioration** : instrumentation permettant d’itérer (feedback, analytics, A/B tests).

  #### Objectifs techniques (ce que le MVP doit garantir)

  - **Pertinence** : bonne sélection d’événements (récence, distance, thèmes, disponibilité).
  - **Traçabilité** : expliquer “pourquoi cette reco ?” (sources, métadonnées, scoring). Les approches RAG indexées mettent en avant la **traçabilité et le retour à la source** comme bénéfice clé.
  - **Mémoire** : passer d’une conversation isolée à une relation continue. Les systèmes de mémoire d’agents nécessitent des mécanismes de **stockage + retrieval**, gestion du **contexte limité**, et souvent **checkpointing** pour persistance.
  - **Sécurité/Conformité** : gestion PII, contrôle accès, audit.
  - **Observabilité** : métriques LLM + produit. Bedrock s’intègre à CloudWatch/CloudTrail et expose des métriques (tokens, latence, invocations), + options réseau privées (VPC endpoints).

  #### Contraintes et hypothèses (à expliciter dès le MVP)

  - **Données** : OpenAgenda fournit un export riche (cf. section 4.3 “Schéma de données réaliste”).
  - **Temps réel** : “temps réel” = ingestion fréquente (ex. hourly/near-real-time) + prise en compte “dernière mise à jour”.
  - **Charge** : 3 scénarios (faible / moyen / élevé) définis en section coûts.
  - **Qualité LLM** : non-déterministe → besoin de guardrails + évaluation continue.

  ------

  ### 1.2 Analyse des utilisateurs cibles et cas d’usage

  #### Segments (MVP)

  1. **Explorateur local**
     - “Que faire ce soir près de moi ?”
     - Sensible à distance, horaires, accessibilité, prix/inscription.
  2. **Planificateur**
     - “Ce week-end : expo + concert, budget limité”
     - Besoin d’itinéraire, compatibilité calendrier, multi-contraintes.
  3. **Passionné thématique**
     - “Tout ce qui touche au jazz / théâtre contemporain”
     - Attentes fortes sur pertinence sémantique.
  4. **Touriste**
     - “À Paris 3 jours, des événements en anglais”
     - Localisation + langue + période.

  #### Cas d’usage prioritaires (MVP)

  - **Recherche conversationnelle** : question libre → shortlist d’événements.
  - **Recommandation personnalisée** : profil (thèmes + zones + périodes préférées) + historique.
  - **Q/R sur un événement** : détails, conditions, accessibilité, registration, lien.
  - **Comparaison** : “choisis entre A et B selon mes préférences”
  - **Web search “assistée”** : enrichir un événement (ex. infos de dernière minute, changement d’état).

  #### Critères de succès (mesurables)

  S’inspirer de mesures produit (NPS/CSAT/CES, churn, etc.).

  - métriques projet : indicateurs **leading/lagging** et métriques “SMART” (spécifiques, pertinentes, exploitables).

  ------

  ## 2. Plan de projet

  > Approche : **hybride agile** (itérations courtes, backlog ordonné) + jalons de “go/no-go” et livrables formels.

  ### 2.1 Jalons (macro)

  | Jalon                             | Objectif                                               | Critère d’acceptation                |
  | --------------------------------- | ------------------------------------------------------ | ------------------------------------ |
  | M0 — Kickoff & cadrage            | scope MVP + risques + architecture cible               | doc de cadrage validé + KPI cibles   |
  | M1 — Design MVP                   | design détaillé (archi, data, sécu, monitoring, coûts) | rapport complet + estimation         |
  | M2 — Fondations infra             | CI/CD, IaC, environnements dev/stage                   | déploiement automatisé OK            |
  | M3 — Data ingestion v1            | pipeline OpenAgenda → stockage + stg                   | données exploitables + tests         |
  | M4 — Vectorisation & retrieval v1 | embeddings + index + filtres                           | retrieval stable + latence cible     |
  | M5 — Chat RAG v1 (Chainlit)       | chat fonctionnel + citations sources                   | démos cas d’usage MVP                |
  | M6 — Mémoire + géoloc             | personnalisation durable + ranking local               | scénarios multi-sessions OK          |
  | M7 — Web search tool              | agent/tooling + garde-fous                             | résultats pertinents + rate limiting |
  | M8 — Observabilité & éval         | dashboards, traces, evaluation set                     | suivi qualité + alerting             |
  | M9 — Beta                         | tests utilisateurs, feedback, durcissement             | CSAT/latence > seuils                |
  | M10 — Go-Live MVP                 | release contrôlée                                      | runbook + rollback + monitoring      |

  ### 2.2 Échéancier (exemple 12 semaines)

  | Semaine | Focus                                                    |
  | ------- | -------------------------------------------------------- |
  | S1–S2   | M0–M1 : design, décisions cloud, backlog, coûts, risques |
  | S3      | M2 : IaC + CI/CD + environnements                        |
  | S4–S5   | M3 : ingestion + schéma + DQ checks                      |
  | S6      | M4 : embeddings + index + retrieval                      |
  | S7      | M5 : Chainlit + RAG + citations                          |
  | S8      | M6 : mémoire + géoloc + reranking                        |
  | S9      | M7 : web search tool + guardrails                        |
  | S10     | M8 : monitoring + LangSmith/Phoenix + evaluation         |
  | S11     | M9 : beta + feedback + tuning                            |
  | S12     | M10 : go-live + runbooks + handover                      |

  ![Ghaant](C:\Users\Admin\Desktop\DATA\Data Engineer\Master\Openclassrooms\Projet 13\Ghaant.png)

  ### 2.3 Livrables (par domaine)

  Référentiel d’artefacts recommandé (plans, baselines, backlog, risk register, etc.).

  **Livrables “design / doc”**
  
  - Architecture détaillée (diagrammes + composants + flux)
  - Data model (raw/stg/serving + vector metadata)
  - Stratégie mémoire (ST/LT + consolidation)
  - Stratégie géolocalisation (PostGIS + filtres)
  - Stratégie web search (tooling + garde-fous)
  - Stratégie observabilité & évaluation (KPI, dashboards, alerting)
  - Estimation coûts build & OPEX + optimisations

  **Livrables “code / infra”**
  
  - IaC (Terraform/CDK), CI/CD, environnements
  - Pipelines ingestion + vectorisation
  - Service retrieval + RAG orchestrator
  - App Chainlit + API backend
  - Guardrails + tests
  - Monitoring (OpenTelemetry, dashboards)

  ------

  ## 3. Macro backlog des fonctionnalités (format “type Excel”)

  ### 3.1 Principes de backlog & priorisation
  
  - Backlog = enregistrer, suivre, **ordonner** le travail restant ; attention à “tout est must” (piège MoSCoW) : si tout est critique, la vision est floue.
  - Refinement continu pour garantir des items “Ready” avant dev.
  - Décomposer les epics via **acceptance criteria** et livrer de la valeur à chaque slice (éviter découpe purement technique UI/DB).

  ### 3.2 Backlog (extrait structuré et actionnable)

  **Légende**
  
  - Priorité : Must / Nice
  - Complexité : S (1–3j), M (4–7j), L (8–15j), XL (16j+)
  - Risque : H/M/L
  - Mitigation : action préventive
  
  | Sprint | Epic          | Feature / User Story                                | Priorité | Complexité | Estimation | Dépendances  | Risque | Mitigation                    |
  | ------ | ------------- | --------------------------------------------------- | -------- | ---------- | ---------- | ------------ | ------ | ----------------------------- |
  | 1      | Fondation     | Repo + conventions + quality gates (lint/test)      | Must     | S          | 2j         | —            | L      | templates + CI                |
  | 1      | Fondation     | IaC AWS (VPC, IAM, secrets, logs)                   | Must     | M          | 5j         | —            | M      | module Terraform + revue sécu |
  | 1      | Fondation     | CI/CD (build, test, deploy dev/stage)               | Must     | M          | 5j         | IaC          | M      | environnements isolés         |
  | 2      | Data          | Ingestion OpenAgenda (extract)                      | Must     | M          | 6j         | Fondation    | M      | pagination + retries          |
  | 2      | Data          | Stockage raw (S3 + parquet + versioning)            | Must     | M          | 4j         | Ingestion    | L      | conventions partition/date    |
  | 2      | Data          | Normalisation stg (events/venues)                   | Must     | M          | 6j         | raw          | M      | tests + DQ checks             |
  | 2      | Data          | DQ checks (Soda/GreatExp)                           | Must     | M          | 5j         | stg          | M      | seuils + exceptions           |
  | 3      | Vector        | Modèle embeddings (choix + wrapper)                 | Must     | M          | 4j         | stg          | M      | abstraction provider          |
  | 3      | Vector        | Indexation Zilliz (collections + partitions)        | Must     | M          | 6j         | embeddings   | M      | POC perf topK                 |
  | 3      | Vector        | Hybrid retrieval (keyword+vector)                   | Must     | M          | 6j         | index        | M      | routing + fusion              |
  | 4      | Retrieval     | Reranking (MMR / règles)                            | Nice     | M          | 5j         | retrieval    | M      | A/B testing                   |
  | 4      | RAG           | Prompt templates + citations + format réponse       | Must     | M          | 5j         | retrieval    | M      | guardrails sortie             |
  | 4      | RAG           | Réponses “traçables” (source_id, url, dates)        | Must     | S          | 3j         | stg/index    | L      | metadata obligatoire          |
  | 5      | Chat          | App Chainlit (auth légère)                          | Must     | M          | 6j         | RAG          | M      | session mgmt                  |
  | 5      | Mémoire       | Short-term memory (session)                         | Must     | M          | 5j         | chat         | M      | Redis TTL                     |
  | 5      | Mémoire       | Long-term memory (profil + préférences)             | Must     | M          | 7j         | auth + db    | H      | minimiser PII + opt-in        |
  | 6      | Mémoire       | Consolidation/summarization                         | Nice     | M          | 6j         | LT memory    | M      | batch jobs                    |
  | 6      | Geo           | Géoloc user + rayon + ranking distance              | Must     | M          | 6j         | stg venues   | M      | PostGIS + fallback            |
  | 6      | Geo           | Filters multi-critères (dates, tags, accessibilité) | Must     | M          | 5j         | stg          | L      | index + query plan            |
  | 7      | Web           | Tool “web_search” via smolagents                    | Nice     | L          | 10j        | guardrails   | H      | allowlist + cache             |
  | 7      | Guardrails    | Output schema (Pydantic/JSON)                       | Must     | M          | 6j         | RAG          | M      | Guardrails/Instructor         |
  | 8      | Guardrails    | Policy & safety (NeMo)                              | Nice     | L          | 10j        | chat         | M      | règles minimales              |
  | 8      | Observabilité | Tracing LLM (LangSmith)                             | Must     | S          | 3j         | RAG          | L      | sampling                      |
  | 8      | Observabilité | Phoenix eval dashboard + dataset                    | Nice     | M          | 7j         | traces       | M      | gold set                      |
  | 9      | Produit       | Feedback utilisateur (👍/👎 + raisons)                | Must     | S          | 3j         | chat         | L      | stockage events               |
  | 9      | Produit       | CSAT/NPS/CES mini-sondage                           | Nice     | S          | 3j         | feedback     | L      | déclenchement                 |
  | 9      | Ops           | Alerting latence/taux erreurs                       | Must     | S          | 3j         | logs/metrics | L      | seuils + runbook              |
  | 10     | Sécurité      | IAM least privilege + audit CloudTrail              | Must     | M          | 5j         | IaC          | M      | revue + tests                 |
  | 10     | Release       | Beta fermée + itérations                            | Must     | M          | 7j         | tout         | M      | critères d’entrée/sortie      |

  ------

  ## 4. Architecture technique détaillée

  ### 4.1 Schéma d’architecture cloud scalable (AWS + Bedrock + Zilliz + Chainlit)

  #### Vue d’ensemble (cible MVP)

  ![image-20260213124417067](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\image-20260213124417067.png)

  

  

  
  
  
  
  

  

  
  
  #### Composants AWS recommandés (MVP pragmatique)
  
  **Compute**
  
  - **ECS Fargate** (containers) pour :
    - `api-backend` (FastAPI)
    - `rag-orchestrator` (peut être dans le backend au début, mais modularisable)
    - `ingestion-worker` (jobs planifiés)
  - Alternative : Lambda pour ingestion légère, mais ingestion + ETL + embeddings peuvent dépasser (durée/mémoire) → Fargate plus simple.
  
  **Réseau**
  
  - VPC + subnets privés
  - **VPC Endpoint / PrivateLink** pour Bedrock : trafic privé, pas d’internet public pour les appels modèle.
  
  **Stockage & bases**
  
  - **S3** : raw exports + snapshots + artefacts d’évaluation
  - **RDS PostgreSQL + PostGIS** :
    - tables `stg.events`, `stg.venues`, `user_profiles`, `user_feedback`
    - géo-queries (rayon, bounding box, index GiST)
  - **Zilliz Cloud** : vecteurs (event embeddings + éventuellement memory embeddings)
  - **Redis (ElastiCache)** : session memory (short-term), caching (semantic cache option)
    - la **semantic caching** est citée comme approche coût/perf, avec limites en multi-tours.
  
  **LLM**
  
  - **Option A (MVP rapide)** : **Amazon Bedrock** (LLM managé)
    - bénéfices : gouvernance, métriques, IAM, CloudTrail/CloudWatch.
  - **Option B (open-source Llama 3.1 70B)** : self-hosting (SageMaker / EKS / EC2 GPU)
    - bénéfices : contrôle coût par token (selon usage), souveraineté modèle
    - coûts/complexité supérieurs (GPU, scaling, MLOps) → option “phase 2” sauf exigence forte.
  
  **Observabilité**
  
  - CloudWatch (logs/metrics)
  - OpenTelemetry (traces)
  - **LangSmith** : tracing agents/chains
  - **Arize Phoenix** : eval + observabilité LLM (offline/online)
  
  ------
  
  ### 4.2 Choix du cloud provider & veille technique (justification)
  
  #### Pourquoi AWS (et pas GCP/Azure) — critères MVP
  
  1. **Bedrock** : service managé LLM avec gouvernance (IAM), audit (CloudTrail), métriques (CloudWatch) et options réseau privées (VPC endpoints).
  2. **Écosystème complet** : une app GenAI est plus qu’un modèle, elle requiert plusieurs composants (data, sécurité, déploiement, etc.).
  3. **Scalabilité standard** : ECS, RDS, S3, observabilité native.
  4. **Conformité** : choix de région, chiffrement, gestion clés.

  #### Veille sur options marché (vector DB / UI / tracing)

  - **Vector DB** : Zilliz Cloud (imposé), alternatives : Pinecone, Weaviate, pgvector (si on voulait tout dans Postgres).
  - **UI Chat** : Chainlit (imposé MVP), alternatives : Streamlit, Next.js.
  - **Tracing/Eval** : LangSmith, Phoenix, OpenTelemetry.

  ------
  
  ### 4.3 Schéma de données ( OpenAgenda)
  
  #### 4.3.1 Champs OpenAgenda (source)

  Le fichier OpenAgenda contient notamment : Identifiant, Slug, URL canonique, Titre, Description, Description longue, Mots clés, Image, Dernière mise à jour, Résumé horaires, dates début/fin, horaires détaillés, accessibilité, identifiant lieu, coordonnées, adresse, ville, région, pays, tags lieu, état événement, âge min/max, catégorie, registration, liens additionnels, etc.

  #### 4.3.2 Modèle de données proposé (3 couches)
  
  **But** : séparer brut / normalisé / serving + garantir traçabilité.
  
  ##### A) RAW (S3 + éventuellement Postgres raw)
  
  - `raw.openagenda_events` (append-only, versionné)
    - colonnes “telles quelles”
    - `ingested_at`, `source_file`, `hash_row`
  - `raw.openagenda_venues`
    - déduit de “Identifiant du lieu” (dédoublonnage)
  
  ##### B) STAGING (Postgres)
  
  - `stg.events`
    - `event_id` (Identifiant)
    - `slug`, `canonical_url`
    - `title`, `description_short`, `description_long`
    - `keywords`, `category`
    - `image_url`, `image_credits`
    - `updated_at` (Dernière mise à jour)
    - `schedule_summary`, `schedule_detailed`
    - `date_first_start`, `date_first_end`, `date_last_start`, `date_last_end`
    - `accessibility_code`, `accessibility_text`
    - `is_online`, `online_url`
    - `event_status`
    - `age_min`, `age_max`
    - `registration`
    - `additional_links` (JSONB)
    - `venue_id`
    - `source_agenda_title`, `source_agenda_uid`
    - `contributor_email` (⚠️ PII), `contributor_phone`, `contributor_name`, etc. (souvent à exclure/masquer)
  - `stg.venues`
    - `venue_id` (Identifiant du lieu)
    - `name`, `address`, `zipcode`, `city`, `region`, `country`
    - `geo_point` (PostGIS geography POINT)
    - `venue_phone`, `venue_website`, `venue_links` (JSONB)
    - `venue_tags`, `venue_description`, `access_route`
    - `venue_image_url`, `venue_image_credits`
  - `stg.event_tags` (si besoin de normaliser tags)
  - `stg.event_occurrences` (optionnel, si horaires détaillés explosés)
  
  ##### C) SERVING (Postgres)
  
  - `app.user_profile`
    - préférences : villes/zones, thèmes, horaires, contraintes (budget, accessibilité)
  - `app.user_events`
    - favoris, suivis, clics
  - `app.feedback`
    - thumbs up/down + raisons + “réponse utile ?”
  - `ops.rag_queries`
    - logs structurés : query, top_k ids, latence, score, modèle, tokens (pour coûts)
  
  #### 4.3.3 Vector store (Zilliz)
  
  - Collection `events_embeddings`
    - `event_id` (PK)
    - `embedding` (vector)
    - `metadata` (JSON) : dates, category, city, geo_hash, is_online, status, updated_at
    - champs pour filtrage rapide (selon capacités Zilliz)
  - Option (phase 2) : collection `memories_embeddings` (pour mémoire long terme)
  
  ------
  
  ### 4.4 Design RAG & Agents (scalable + fiable)
  
  #### 4.4.1 Pipeline RAG modulaire (recommandé)
  
  Un pipeline RAG se structure en composants/pipelines indépendants (collecte → préparation → embeddings → retrieval → génération), ce qui facilite le travail parallèle et la montée en charge.
  
  **Pipelines proposés**
  
  1. **Pipeline A — Data ingestion**
     - extraction OpenAgenda + normalisation + DQ checks
  2. **Pipeline B — Embeddings & index**
     - chunking (si nécessaire) + embeddings + upsert Zilliz
  3. **Pipeline C — Retrieval & ranking**
     - query routing + hybrid search + filtres (dates/geo)
  4. **Pipeline D — Generation**
     - prompt augmentation + citations + garde-fous
  5. **Pipeline E — Evaluation**
     - dataset de tests + métriques + feedback loop
  
  #### 4.4.2 Retrieval avancé : hybrid + routing + reranking
  
  - **Query routing** : décider “keyword vs semantic vs hybrid” selon le contenu de la requête.
  - **Hybrid search** : combiner keyword match (noms, lieux) et semantic (intent).
  - **Indexing & traceability** : les approches indexées augmentent vitesse, précision et traçabilité à la source.
  
  #### 4.4.3 Mémoire conversationnelle (design)
  
  Les systèmes convergent vers 3 formes long-terme : **épisodique**, **sémantique**, **procédurale**.
  Et il faut gérer la contrainte : le contexte du modèle est limité et coûteux → on externalise via RAG/mémoire.

  **Proposition MVP (simple mais “production-minded”)**

  - **Short-Term Memory (STM)** : Redis
    - contenu : derniers tours + variables session (ville courante, dates, thèmes)
    - TTL (ex. 24h), purge automatique
  - **Long-Term Memory (LTM)** : Postgres + (option) Zilliz
    - **profil sémantique** : préférences stables (tags, villes)
    - **épisodique** : “dernier weekend tu as aimé X”
    - consolidation : résumés périodiques
  - **Checkpointing** : snapshots de session (résumé + slots) pour reprise multi-sessions.
  - **Semantic caching** (Nice-to-have) : accélérer questions fréquentes (mais attention multi-tours).

  #### 4.4.4 Contexte géographique optimisé

  **Objectif** : ranking local “par défaut”, sans que l’utilisateur répète “près de moi”.

  - Stocker `user_location` (opt-in) et/ou ville préférée
  - Calcul :
    - candidates via filtres (dates, status)
    - scoring final = pertinence sémantique + bonus distance + bonus fraîcheur
  - Implémentation :
    - PostGIS pour calcul distance et filtrage radius
    - Zilliz pour retrieval sémantique ; on intersecte avec résultats PostGIS, ou on utilise metadata filters si suffisant.

  ------

  ### 4.5 Stratégies de déploiement, modularité, monitoring
  
  #### 4.5.1 Déploiement (MVP → scalable)
  
  - **IaC** : Terraform (modules : réseau, ECS, RDS, Redis, IAM)
  - **CI/CD** : GitHub Actions (build/test → deploy dev → approval → stage/prod)
  - **Environnements** : dev / stage / prod
  - **Secrets** : AWS Secrets Manager
  - **Release** : blue/green ou rolling ECS
  
  #### 4.5.2 Modularité (éviter monolithe )
  
  Découper par capacités :
  
  - `ingestion-service`
  - `embedding-service`
  - `retrieval-service`
  - `rag-orchestrator`
  - `chat-ui` (Chainlit)
  - `observability` (collectors, dashboards)
  
  > Bénéfice : on peut scaler embedding indépendamment du chat.

  #### 4.5.3 Monitoring (technique + produit)

  **Niveau infra**
  
  - latence API, erreurs, CPU/mem
  - RDS connections, slow queries
  - Redis hit rate

  **Niveau LLM/RAG**

  - tokens in/out, latence d’invocation Bedrock, invocations (CloudWatch).
  - retrieval latency, topK size, rerank time
  - taux réponses avec citations

  **Niveau produit**
  
  - adoption : utilisateurs actifs, sessions, rétention
  - satisfaction : CSAT/NPS/CES (si activé).
  - qualité : thumbs up/down, “complaint-like prompts”
  
  **Seuils & exception plans**
  
  - définir des tolérances (latence, budget, erreurs) + plan d’action si dépassement.
  
  ------
  
  ## 5. Estimation des coûts Build & OPEX (format “type Excel”)
  
  > ⚠️ Les prix cloud changent fréquemment. Le MVP doit livrer **un modèle de coûts** + scénarios, et valider les chiffres via AWS Pricing Calculator + offres Zilliz au moment du déploiement.
  > Ci-dessous : structure exhaustive + drivers + estimations relatives.
  
  | Poste                                    | Jours | TJM (indicatif) | Sous-total | Notes              |
  | ---------------------------------------- | ----- | --------------- | ---------- | ------------------ |
  | Design MVP (archi + data + sécu + costs) | 10    | 750 €           | 7 500 €    | livrable principal |
  | IaC + CI/CD                              | 8     | 800 €           | 6 400 €    | base production    |
  | Ingestion + normalisation + DQ           | 12    | 750 €           | 9 000 €    | robustesse data    |
  | Embeddings + Zilliz + retrieval          | 12    | 750 €           | 9 000 €    | perf + filtres     |
  | RAG + Chainlit + guardrails              | 12    | 750 €           | 9 000 €    | fiabilité outputs  |
  | Mémoire + géoloc                         | 10    | 750 €           | 7 500 €    | personnalisation   |
  | Web search tool                          | 6     | 750 €           | 4 500 €    | Nice-to-have       |
  | Observabilité + eval + dashboards        | 8     | 775 €           | 6 200 €    | pilotage qualité   |
  | Beta + hardening + runbooks              | 8     | 775 €           | 6 200 €    | go-live            |

  **Total charge** : ~86 jours (à ajuster selon périmètre Nice-to-have)

  ### Total Build (hors marge)

  **65 300 €**

  ### Provision recommandée (risques / imprévus)

  - **+15%** (tickets non anticipés, ajustements perf, sécurité, feedback utilisateurs) : **9 795 €**
  
  ✅ **Total Build (avec provision 15%) : 75 095 €**

  ------

  ### 5.2 Coût d’exploitation (OPEX) selon charges prévues

  ### 5.2.0 Principes de chiffrage (comment on évite les “surprises”)

  L’OPEX du MVP dépend principalement de **l’usage** (requêtes, tokens, volume d’indexation, trafic) et de **la capacité minimale** à maintenir (API, workers, BDD relationnelle, cache, logs/monitoring). 
  
  L’approche la plus fiable consiste à :
  
  1. **décomposer par “cost drivers”** (tokens, vCPU-seconde, GB-mois, métriques, etc.),
  2. **définir 3 scénarios de charge** (MVP → croissance → scale),
  3. **estimer via l’AWS Pricing Calculator** + un tableau “Excel” interne qui suit les hypothèses.
  
  > À noter : Bedrock fonctionne en “pay-per-use” (inférence) et propose aussi des options de capacité réservée/tiers ; les prix varient par **région** et **modèle**. ([Amazon Web Services, Inc.](https://aws.amazon.com/bedrock/pricing/))
  
  
  
  #### Variables clés (cost drivers)
  
  1. **LLM inference** (tokens in/out) — principal poste si forte adoption
  2. **Vector DB (Zilliz)** — stockage + requêtes/s
  3. **Compute ECS** — API + workers
  4. **RDS Postgres** — taille + IOPS
  5. **Redis** — mémoire + débit
  6. **Observabilité** — logs volumineux + traces

  #### Scénarios de charge (exemple)

  | Scénario    | Utilisateurs/jour | Requêtes chat/jour | Tokens moyens / req | QPS pic |
  | ----------- | ----------------- | ------------------ | ------------------- | ------- |
  | S1 (faible) | 200               | 1 000              | 1 500               | 2       |
  | S2 (moyen)  | 2 000             | 15 000             | 2 000               | 10      |
  | S3 (élevé)  | 20 000            | 150 000            | 2 500               | 50      |
  
  ------
  
  ### 5.2.1 Outil de référence : AWS Pricing Calculator (et comment l’utiliser)
  
  AWS recommande l’AWS Pricing Calculator pour construire un chiffrage mensuel par service, par région, et exporter en CSV/PDF. Le guide précise aussi qu’il n’y a **pas d’API** officielle du calculator (donc on versionne nos hypothèses côté projet). ([AWS Documentation](https://docs.aws.amazon.com/pricing-calculator/latest/userguide/getting-started.html))
  
  **Pages clés à présenter dans le rapport (c’est exactement le workflow demandé)** :
  
  - **Landing** → création d’estimation
  - **Add service** (page “ajouter un service”)
  - **Configure service** (région + paramètres)
  - **My estimate** (récap + export CSV/PDF) ([AWS Documentation](https://docs.aws.amazon.com/pricing-calculator/latest/userguide/getting-started.html))
  
  **Bonnes pratiques (à inclure dans le plan FinOps du MVP)**

  - 1 estimate par **environnement** (dev / staging / prod).
  - 1 estimate par **scénario de charge** (S1/S2/S3).
  - Groupes par “cost centers” (LLM / Data / Observabilité / Réseau). ([AWS Documentation](https://docs.aws.amazon.com/pricing-calculator/latest/userguide/getting-started.html))

  ------
  
  ### 5.2.2 Zilliz Cloud (Milvus managé) — options, modèle de coût, et Marketplace AWS

  #### Option A — Zilliz Cloud via site Zilliz (pricing)

  - **Free / Free tier** : 5 GB stockage + 2.5M vCUs / mois (utile dev/POC, très limité pour prod). ([docs.zilliz.com](https://docs.zilliz.com/docs/free-trials?utm_source=chatgpt.com))
  - **Standard** : à partir de **$0/mois (Serverless)** et **$99/GB/mois (Dedicated)** (positionnement “MVP → prod”). ([zilliz.com](https://zilliz.com/pricing?utm_source=chatgpt.com))
  - **Serverless** : pay-as-you-go sur consommation (vCUs) + stockage si applicable ; logique “tu ne payes presque rien quand c’est inactif, hors stockage”. ([docs.zilliz.com](https://docs.zilliz.com/docs/view-invoice?utm_source=chatgpt.com))

  #### Option B — “Subscribe on Marketplace (AWS)” (recommandé pour MVP en entreprise)

  Avantages : **facturation centralisée AWS**, facilité procurement, et consolidation FinOps.

  - La fiche AWS Marketplace indique : abonnement sans date de fin, annulable à tout moment, facturation à l’usage. ([Amazon Web Services, Inc.](https://aws.amazon.com/marketplace/pp/prodview-iqbidum7feuio))
  - **Dimension de facturation Marketplace** : “Zilliz Cloud Usage” → **$0.001 / unit**, et **1 unit = 0.1 cent** (donc 1$ = 1000 units). ([Amazon Web Services, Inc.](https://aws.amazon.com/marketplace/pp/prodview-iqbidum7feuio))
  - Mention importante : “Additional AWS infrastructure costs may apply” → on chiffre tout avec AWS Pricing Calculator + hypothèses Zilliz. ([Amazon Web Services, Inc.](https://aws.amazon.com/marketplace/pp/prodview-iqbidum7feuio))

  **Recommandation MVP (coût/risque)**

  - **MVP early** : Serverless (si charge très variable)
  - **MVP stable + SLA** : Dedicated (latence/throughput plus prédictibles)
  - **Scale / contraintes conformité** : BYOC (quand on veut isoler infra & réseau)
  
  ------

  ### 5.2.3 Amazon Bedrock — ce qu’on paie réellement (tokens, tiers, batch, provisioned)

  #### A) Inference On-demand (tokens)

  Bedrock facture l’inférence en fonction du **volume de tokens input/output** (dépend du modèle/provider), et propose plusieurs **tiers** (Standard/Flex/Priority/Reserved). ([Amazon Web Services, Inc.](https://aws.amazon.com/bedrock/pricing/))

  **Exemple de pricing officiel (Anthropic Claude 3.5 Sonnet – effective 1 Dec 2025)**
  
  - **$6.00 / 1M input tokens**
  - **$3.00 / 1M output tokens** ([Amazon Web Services, Inc.](https://aws.amazon.com/bedrock/pricing/))

  ➡️ **Formule “unit economics”** (à mettre dans votre table OPEX) :
  `Coût_LLM = (InputTokens/1,000,000 * PrixIn) + (OutputTokens/1,000,000 * PrixOut)` ([Amazon Web Services, Inc.](https://aws.amazon.com/bedrock/pricing/))
  
  #### B) Batch inference (quand possible)
  
  Bedrock indique que certains modèles ont un **prix batch ~50% moins cher** que l’on-demand (utile pour traitements non temps réel : ré-indexation, enrichissements, résumés offline). ([Amazon Web Services, Inc.](https://aws.amazon.com/bedrock/pricing/))
  
  #### C) Provisioned / Reserved / Custom Model Import (cas “LLama 3.1 70B” & capacité)
  
  Pour des besoins de capacité/latence garantis, on peut passer par des options de capacité (selon modèle). Bedrock documente aussi le **Custom Model Import** avec un dimensionnement en “Custom Model Units”.
  Ex : référence de capacité : **Llama 3.1 70B 128k nécessite 8 Custom Model Units**. ([Amazon Web Services, Inc.](https://aws.amazon.com/bedrock/pricing/))

  ------
  
  ### 5.2.4 Compute API & services (ECS Fargate) — coût, options, leviers

  #### Ce qu’on paie

  Fargate est facturé sur :
  
  - vCPU, mémoire, stockage éphémère, OS/arch, durée (arrondie à la seconde) ([Amazon Web Services, Inc.](https://aws.amazon.com/fargate/pricing/))
  - **20 GB de stockage éphémère inclus** ; on paie seulement l’extra. ([Amazon Web Services, Inc.](https://aws.amazon.com/fargate/pricing/))
  - durée : **1-minute minimum** (Windows : 5 minutes min). ([Amazon Web Services, Inc.](https://aws.amazon.com/fargate/pricing/))
  
  **Exemples chiffrés officiels (US East, Linux/x86)** :
  
  - CPU : **$0.000011244 / vCPU-second**
  - RAM : **$0.000001235 / GB-second**
  - Storage extra : **$0.0000000308 / GB-second** ([Amazon Web Services, Inc.](https://aws.amazon.com/fargate/pricing/))
  
  #### Options pour réduire l’OPEX
  
  - **Fargate Spot** : jusqu’à **-70%** (workloads interruptibles : indexing, batch jobs). ([Amazon Web Services, Inc.](https://aws.amazon.com/fargate/pricing/))
  - **Compute Savings Plans** : jusqu’à **-50%** pour usage stable. ([Amazon Web Services, Inc.](https://aws.amazon.com/fargate/pricing/))
  
  ------
  
  ### 5.2.5 Base SQL (RDS PostgreSQL) — composants de coût à modéliser
  
  Pour RDS PostgreSQL, les principaux postes sont :
  
  - **DB instance hours** (facturation à la seconde, minimum 10 minutes)
  - **stockage (GB-mois)**
  - **IO requests / provisioned IOPS** (selon stockage)
  - **backup storage**
  - **data transfer**
  - option : **Reserved Instances** pour réduire la facture si usage stable ([AWS Documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/User_DBInstanceBilling.html))

  RDS propose plusieurs types de stockage (ex. gp2/gp3/io1) avec impacts sur coûts/perfs. ([AWS Documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Storage.html))
  
  **Point d’attention** (souvent oublié en MVP) : si vous utilisez des instances “burstable” (T3/T4g) en mode unlimited, il existe un coût de **CPU credits**. Exemple cité : **$0.075 / vCPU-hour** pour les CPU credits. ([Amazon Web Services, Inc.](https://aws.amazon.com/rds/postgresql/pricing/))
  
  ------
  
  ### 5.2.6 Cache (ElastiCache Redis) — Serverless vs nodes
  
  ElastiCache a deux approches courantes :
  
  #### A) ElastiCache Serverless
  
  Facturation basée sur :
  
  - **données stockées (GB-hours)**
  - **ECPUs consommées** (par volume traité) ([Amazon Web Services, Inc.](https://aws.amazon.com/elasticache/pricing/))
  
  #### B) ElastiCache “node-based”
  
  - facturation “node-hours”, et (selon la doc) **arrondie à l’heure complète** ([Amazon Web Services, Inc.](https://aws.amazon.com/elasticache/pricing/))
  
  Backups : exemple de tarif indiqué : **$0.085 / GiB-month**. ([Amazon Web Services, Inc.](https://aws.amazon.com/elasticache/pricing/))
  
  ------

  ### 5.2.7 Stockage & data lake (S3) — ce qu’il faut chiffrer

  S3 se chiffre via :

  - **stockage (GB-mois)** selon classe (Standard, IA, Glacier, etc.)
  - **requests** (PUT/GET/LIST)
  - **data transfer** (intra/inter-region, internet egress)
  - **features** (ex. Transfer Acceleration) ([Amazon Web Services, Inc.](https://aws.amazon.com/s3/pricing/))
  
  Exemples de tarifs mentionnés sur S3 pricing (features réseau) :

  - Transfer Acceleration : **$0.04–$0.08 / GB** selon zone
  - Data routing : **$0.002–$0.004 / GB** selon zone ([Amazon Web Services, Inc.](https://aws.amazon.com/s3/pricing/))
  
  ------
  
  ### 5.2.8 Observabilité (CloudWatch) — métriques, logs, alarmes, dashboards
  
  CloudWatch se chiffre typiquement sur :
  
  - **logs ingérés + stockés**
  - **custom metrics**
  - **alarmes**
  - **dashboards** ([AWS Documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_billing.html))
  
  Exemples chiffrés (extraits pricing) :
  
  - **Composite alarm** : **$0.50 / month** (pricing listing) ([Amazon Web Services, Inc.](https://aws.amazon.com/cloudwatch/pricing/))
  - **Alarm metric (standard resolution)** : **$0.10 / alarm metric** (pricing listing) ([Amazon Web Services, Inc.](https://aws.amazon.com/cloudwatch/pricing/))
  - **Custom metrics** : exemple affiché **$0.30 per metric-month** (selon paliers) ([Amazon Web Services, Inc.](https://aws.amazon.com/cloudwatch/pricing/))
  
  ------
  
  ## 5.2.9 Tableau “type Excel” — Drivers OPEX à suivre (unités + prix + options)

  > Copiable tel quel dans Excel (à compléter avec vos hypothèses S1/S2/S3).

  | Poste                              | Unité de coût                                      | Exemple de prix (référence)                                  | Options / remarques FinOps                                   |
  | ---------------------------------- | -------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | **Bedrock (LLM inference)**        | 1M input/output tokens                             | Claude 3.5 Sonnet : $6 / 1M input ; $3 / 1M output           | Tiers (Standard/Flex/Priority/Reserved) + Batch possible (-50% sur modèles éligibles) ([Amazon Web Services, Inc.](https://aws.amazon.com/bedrock/pricing/)) |
  | **Bedrock (Custom Model Import)**  | Custom Model Unit (dimensionnement)                | Llama 3.1 70B 128k = 8 CMU (dimensionnement)                 | utile si vous importez/servez un modèle ; facturation en fenêtres (voir page pricing) ([Amazon Web Services, Inc.](https://aws.amazon.com/bedrock/pricing/)) |
  | **Zilliz Cloud (site)**            | GB/mois (Dedicated)                                | From $99/GB/month                                            | Free tier 5GB+2.5M vCU ; Serverless PAYG ; Dedicated plus prédictible ([zilliz.com](https://zilliz.com/pricing?utm_source=chatgpt.com)) |
  | **Zilliz Cloud (AWS Marketplace)** | “Usage unit”                                       | $0.001 / unit (1 unit=0.1 cent)                              | Abonnement annulable ; facturation AWS ; “additional infra costs may apply” ([Amazon Web Services, Inc.](https://aws.amazon.com/marketplace/pp/prodview-iqbidum7feuio)) |
  | **ECS Fargate**                    | vCPU-second / GB-second                            | $0.000011244 / vCPU-s ; $0.000001235 / GB-s (US East example) | Spot jusqu’à -70% ; Savings Plans jusqu’à -50% ; 20GB ephemeral inclus ([Amazon Web Services, Inc.](https://aws.amazon.com/fargate/pricing/)) |
  | **RDS PostgreSQL**                 | instance-hours + storage GB-mois + I/O + backups   | (dépend instance/région)                                     | billing à la seconde (min 10 min) + Reserved Instances possibles ([AWS Documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/User_DBInstanceBilling.html)) |
  | **ElastiCache Redis**              | GB-hours + ECPUs (serverless) / node-hours (nodes) | (dépend région)                                              | backups : $0.085/GiB-month (exemple) ([Amazon Web Services, Inc.](https://aws.amazon.com/elasticache/pricing/)) |
  | **S3**                             | GB-mois + requests + transfer                      | Transfer Acceleration $0.04–$0.08/GB (ex)                    | choisir classes (Standard/IA/Glacier) selon accès ; surveiller egress ([Amazon Web Services, Inc.](https://aws.amazon.com/s3/pricing/)) |
  | **CloudWatch**                     | logs + metrics + alarms                            | composite alarm $0.50/mo ; alarm metric $0.10                | logs + métriques explosent vite : quotas + sampling + rétention courte ([Amazon Web Services, Inc.](https://aws.amazon.com/cloudwatch/pricing/)) |

  ------

  

  Voici un **tableau chiffré mensuel concret (USD)** basé sur :

  - Pricing Bedrock exemple Claude 3.5 Sonnet
    → **$6 / 1M input tokens**
    → **$3 / 1M output tokens**
  - Répartition moyenne : **60% input / 40% output**
  - 30 jours par mois
  - Fargate Linux/x86 pricing officiel
  - Estimations réalistes pour RDS / Redis / Zilliz / CloudWatch
  
  ⚠️ Hypothèses cohérentes mais indicatives (à affiner avec AWS Calculator).
  
  ------
  
  # 📊 ESTIMATION OPEX MENSUELLE – CONFIGURATION COMPLÈTE
  
  ## 🔹 Hypothèses de base
  
  | Scénario | Req / jour | Tokens / req | Tokens / mois |
  | -------- | ---------- | ------------ | ------------- |
  | S1       | 1 000      | 1 500        | 45 M          |
  | S2       | 15 000     | 2 000        | 900 M         |
  | S3       | 150 000    | 2 500        | 11,25 B       |
  
  Répartition tokens :
  
  - 60% input
  - 40% output

  ------
  
  # 📈 TABLEAU EXCEL – OPEX MENSUEL PAR SCÉNARIO
  
  | Poste                           | S1 (faible) | S2 (moyen)  | S3 (élevé)   | Hypothèses                       |
  | ------------------------------- | ----------- | ----------- | ------------ | -------------------------------- |
  | **Bedrock (LLM)**               | **216 $**   | **4 320 $** | **54 000 $** | 60% input @ $6 / 40% output @ $3 |
  | **ECS Fargate (API + workers)** | 36 $        | 213 $       | 1 422 $      | Autoscaling 2 → 20 tasks         |
  | **RDS PostgreSQL**              | 150 $       | 400 $       | 1 200 $      | Instance + storage + backups     |
  | **Redis (ElastiCache)**         | 50 $        | 150 $       | 400 $        | Cache + sessions                 |
  | **Zilliz Cloud**                | 200 $       | 800 $       | 2 500 $      | Serverless → Dedicated scale     |
  | **S3 (stockage + requêtes)**    | 20 $        | 80 $        | 300 $        | Raw + logs + backups             |
  | **CloudWatch (logs + metrics)** | 50 $        | 200 $       | 600 $        | Sampling progressif              |

  ------

  # 💰 TOTAL OPEX MENSUEL ESTIMÉ

  | Scénario | Total mensuel       |
  | -------- | ------------------- |
  | **S1**   | **722 $ / mois**    |
  | **S2**   | **6 163 $ / mois**  |
  | **S3**   | **60 422 $ / mois** |

  ------

  # 🔍 Lecture stratégique
  
  ### 🟢 S1 – MVP Early Stage
  
  ≈ **700–1 000 $ / mois**
  
  - Très soutenable
  - LLM représente ~30% du coût
  - Infrastructure marginale
  
  👉 Idéal pour phase bêta.
  
  ------
  
  ### 🟡 S2 – Croissance maîtrisée
  
  ≈ **6 000 $ / mois**
  
  - LLM devient poste dominant
  - Nécessité de :
    - routing multi-modèle
    - optimisation tokens
    - caching
  
  ------
  
  ### 🔴 S3 – Scale important
  
  ≈ **60 000 $ / mois**
  
  - **LLM = 90% du coût**
  - Obligatoire :
    - multi-modèle
    - compression contexte
    - réduction top-k
    - éventuellement self-host Llama 70B
  
  ------
  
  # Ce que ce tableau démontre dans le rapport
  
  1. Le MVP est économiquement viable en phase initiale
  2. Le vrai facteur d’explosion budgétaire = tokens
  3. L’architecture doit être pensée “cost-aware”
  4. Les optimisations budgétaires ne sont pas optionnelles à scale
  
  ------
  
  ## 5.2.10 “Mini-mode d’emploi” : quoi renseigner dans l’AWS Pricing Calculator (par service)
  
  - **ECS Fargate** : nb services/tasks, vCPU/RAM, durée (24/7 ou bursts), storage, trafic sortant. ([Amazon Web Services, Inc.](https://aws.amazon.com/fargate/pricing/))
  - **RDS PostgreSQL** : type d’instance, Multi-AZ, stockage, IOPS, backups, data transfer. ([AWS Documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/User_DBInstanceBilling.html))
  - **S3** : volume stocké par classe, nb requêtes GET/PUT/LIST, data transfer. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html))
  - **CloudWatch** : logs ingestés/jour, rétention, nb custom metrics, nb alarmes/dashboards. ([AWS Documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_billing.html))
  - **Bedrock** : tokens moyens par requête (input/output), modèle(s), proportion batch vs realtime. ([Amazon Web Services, Inc.](https://aws.amazon.com/bedrock/pricing/))
  
  ------
  
  ### 5.2.11 Optimisations budgétaires recommandées
  
  L’OPEX d’un MVP RAG/Chatbot (AWS + Bedrock + Zilliz + Fargate) est dominé par 3 postes :
   **(1) coûts LLM/tokens**, 
  **(2) compute (serving + jobs batch)**, 
  **(3) stockage/observabilité**. Les optimisations ci-dessous visent à **réduire le coût par requête**, **stabiliser la latence**, et **éviter les explosions de facture** tout en gardant la qualité produit (pertinence + fiabilité).
  
  ------
  
  ## 5.2.11.1 Optimiser le coût LLM : réduire tokens, mieux choisir quand appeler un “gros” modèle
  
  Le levier le plus rentable est presque toujours de **diminuer le nombre de tokens** et de **réduire la fréquence d’appel** aux modèles coûteux.
  
  ### 1.1 Model routing (multi-modèle) : “le bon modèle au bon moment”
  
  Mettre en place une stratégie “router” :
  
  - **Petit modèle** (peu cher, rapide) pour :
    - classification d’intention (recherche / comparaison / Q&A / réservation / support)
    - extraction de contraintes structurées (ville, dates, rayon, catégories, online/offline)
    - reformulation de requête (query rewrite léger)
  - **Grand modèle** (plus cher) uniquement pour :
    - génération finale lorsque la requête est complexe
    - synthèse multi-événements, justification, réponses longues
  - **Fallback** :
    - si budget journalier dépassé → bascule sur modèle plus petit + réponse plus concise
    - si latence trop élevée → mode “fast response”
  
  Bénéfices :
  
  - baisse immédiate de la facture tokens
  - meilleure latence p95
  - capacité à “tenir” les pics
  
  ### 1.2 Réduction tokens (prompt & contexte) : compacter sans perdre la qualité
  
  Actions concrètes :
  
  - **Prompts courts et stables** : templates minimalistes, suppression des redondances, consignes compactes.
  - **Contexte RAG minimal** :
    - limiter `top-k` (ex. 5–8) et augmenter seulement si retrieval pauvre
    - limiter la longueur de chaque chunk (ou “event card”) injecté au modèle
    - préférer des “snippets” + URLs plutôt que des descriptions longues
  - **Compression historique / mémoire** :
    - au lieu de réinjecter tout l’historique, utiliser :
      - une **fenêtre glissante** (N derniers tours)
      - un **résumé de session** (1 paragraphe)
      - des **slots structurés** (ville=…, rayon=…, dates=…, thèmes=…)
    - cela réduit drastiquement les tokens “inutilement” envoyés à chaque tour.

  Pourquoi c’est essentiel : Bedrock facture l’inférence selon **tokens input + output** et propose des options/tier et du batch selon modèles — donc tout ce qui diminue le volume de tokens diminue mécaniquement le coût.
  
  ### 1.3 Contrôler la longueur de sortie (output tokens)
  
  Même si on optimise l’input, la facture peut exploser sur l’output si le modèle “parle trop”.
  
  - imposer une **réponse structurée** et **bornée** (ex. 5 événements max, 2 phrases de justification max par événement)
  - activer un mode “résumé court” par défaut, avec “Souhaites-tu plus de détails ?”
  - éviter les longues digressions en forçant un format (JSON + rendu UI).
  
  ### 1.4 Batch quand c’est possible (offline plutôt que realtime)
  
  Tout ce qui n’a pas besoin d’être instantané doit être “batché” :
  
  - résumés long-terme, consolidation mémoire
  - enrichissements d’événements
  - recalculs d’embeddings (re-indexation)
  - évaluations offline (scoring, tests)
  
  Bedrock précise que le pricing dépend tokens et options (incluant “Provisioned Throughput” selon modèles) et que certaines approches (ex. batch) peuvent réduire les coûts lorsqu’elles sont adaptées au besoin.

  ------

  ## 5.2.11.2 Optimiser le retrieval pour limiter le contexte (et donc les tokens) sans perdre la pertinence

  Dans un RAG, un retrieval “brouillon” coûte cher : il force à envoyer beaucoup de texte au modèle ou à rater la réponse.

  ### 2.1 Retrieval efficace : index + filtres + rerank ciblé
  
  - **Filtres metadata** (ville, dates, catégorie, status, online/offline) pour réduire l’espace de recherche avant vector search.
  - **Rerank léger** (MMR ou règles) après le top-k pour éviter d’envoyer des doublons (mêmes événements/lieux).
  - Ajuster `top-k` dynamiquement :
    - si la requête est très contrainte (“ce soir à Lyon jazz gratuit”) → `top-k` faible
    - si la requête est vague (“des idées de sortie”) → `top-k` un peu plus haut mais toujours borné.
  
  Résultat : moins de “passages” à injecter → moins de tokens → moins de coût et souvent meilleure latence.

  ### 2.2 Semantic caching (avec précautions multi-tours)
  
  Le **semantic cache** (cache par similarité) peut réduire drastiquement les coûts sur les questions répétitives :
  
  - “Que faire ce soir près de moi ?”
  - “Des expos gratuites ce week-end ?”
  - “Donne-moi 5 idées de sorties à Paris”

  Mais attention :
  
  - en multi-tours, deux requêtes “semblables” peuvent dépendre d’un contexte différent (ville/rayon/date)
  - il faut inclure les **slots** (ville/date/rayon/langue) dans la clé de cache
  - TTL court (ex. 1h–24h) pour rester à jour (événements changent).
  
  ------
  
  ## 5.2.11.3 Optimiser le compute : Fargate, Spot, et bonnes pratiques d’autoscaling
  
  Le compute se divise en 2 familles : **serving** (API/chat) et **batch** (ingestion, embeddings, reindex).
  
  ### 3.1 Fargate Spot pour les workloads interruptibles (gains majeurs)
  
  Pour tout ce qui peut être relancé sans impact utilisateur :
  
  - jobs d’ingestion
  - ETL/normalisation
  - embeddings & reindex
  - évaluations offline
  
  Fargate Spot peut offrir jusqu’à **~70%** de réduction selon AWS (et dépend de la disponibilité).
  
  ### 3.2 Savings Plans pour les services 24/7 (serving stable)
  
  Si l’API tourne en permanence, les **Compute Savings Plans** réduisent le coût quand on a un socle de consommation stable (toujours selon conditions AWS).
  
  ### 3.3 Autoscaling “propre” : éviter le surprovisionnement permanent
  
  - dimensionner une “base” minimale (1–2 tasks) + scale sur CPU/RAM + latence
  - limiter la mémoire pour éviter over-allocation
  - séparer “API” et “workers” pour ne pas scaler tout le cluster à cause d’un batch.
  
  ------
  
  ## 5.2.11.4 Optimiser l’observabilité : mesurer assez, sans payer trop
  
  L’observabilité est critique en GenAI (debug + qualité), mais peut coûter très cher (logs + traces).
  
  ### 4.1 Sampling intelligent (traces)
  
  - **Stage** : 100% des traces (debug/qualité)
  - **Prod** : 5–20% selon stabilité + “burst to 100%” pendant incident
  - garder les traces complètes pour les requêtes “à problème” (erreurs, low-quality, thumbs down)
  
  CloudWatch facture sur logs/metrics/alarms ; limiter les métriques custom et contrôler la rétention évite l’explosion de coût.
  
  ### 4.2 Logs : rétention courte + export S3
  
  - rétention CloudWatch courte (ex. 7–14 jours)
  - export des logs “froids” dans S3 (moins cher, audit possible)
  - filtrer/scrubber PII avant ingestion log.
  
  ### 4.3 Limiter les “custom metrics”
  
  - créer peu de métriques custom mais très utiles (p95 latence, tokens, coût estimé/req, satisfaction)
  - éviter d’émettre une métrique par type de requête/ville/tag (cardinalité énorme).
  
  ------
  
  ## 5.2.11.5 Optimiser Zilliz : choisir le bon mode et éviter de payer pour de l’index inutile
  
  La vector DB peut être très économique au début, mais grimper vite si :
  
  - index trop volumineux,
  - réindexations fréquentes,
  - top-k trop élevé, ou
  - metadata non maîtrisées.
  
  ### 5.1 Démarrer Serverless, basculer Dedicated selon SLO
  
  - **Serverless** : idéal si charge variable, MVP early
  - **Dedicated** : quand vous avez des objectifs de latence/throughput plus stricts, et des charges prévisibles
  - **Marketplace AWS** : utile si besoin procurement, facturation centralisée, gouvernance FinOps.
  
  Zilliz met en avant Serverless et Dedicated (“from $99/GB/month”) sur sa page pricing.
  
  ### 5.2 Réduire la taille indexée
  
  - n’embedder que ce qui sert (titre + résumé + éléments discriminants)
  - éviter d’embedder des champs longs et redondants (ex. descriptions très longues) si pas utiles
  - gérer les mises à jour incremental plutôt que reindex complet.
  
  ------
  
  ## 5.2.11.6 “Pack FinOps MVP” : garde-fous indispensables (anti-dérapage)
  
  À intégrer dès le MVP (sinon la facture devient le “bug #1”) :
  
  - **budget mensuel** (AWS Budgets) + alertes
  - “hard limits” applicatifs :
    - max tokens / réponse
    - max tool calls (web search)
    - max top-k retrieval
    - quota par utilisateur (free vs premium)
  - dashboard “coût par requête” + “coût par utilisateur actif”
  - mode dégradé : si budget atteint → réponses plus courtes + modèle plus petit + web search off.
  
  ------
  
  ## 6. Bilan
  
  ### 6.1 Rappel des étapes clés du POC au MVP
  
  - POC : démonstration faisabilité (semantic search + chatbot RAG)
  - MVP : industrialisation via :
    - pipelines data robustes (ingestion + DQ)
    - retrieval hybride + géo
    - mémoire multi-sessions (STM/LTM + checkpointing)
    - garde-fous (structure + policies)
    - observabilité + évaluation continue
  
  ### 6.2 Justification des choix techniques et méthodologiques
  
  - **Mémoire** : indispensable pour personnalisation ; nécessite gestion stockage/retrieval + limitation de contexte + checkpointing.
  - **RAG modulaire** : séparation en pipelines pour scaler et travailler en parallèle.
  - **Index/traceability** : exigence produit (“pourquoi ?”) et debug ; la traçabilité est un avantage majeur des approches indexées.
  - **AWS Bedrock** : sécurité/gouvernance/metrics intégrées (CloudWatch, CloudTrail, VPC endpoints).
  - **Backlog agile ordonné + refinement** : préparation continue, éviter stories “trop grosses”, découper par valeur démontrable.
  
  ### 6.3 Défis rencontrés (attendus) & solutions
  
  - **Hallucinations / incohérences** → guardrails + citations + eval set + feedback
  - **Latence** → cache + topK limité + reranking ciblé
  - **Qualité data** → DQ checks + versioning raw + monitoring anomalies
  - **PII / conformité** → minimisation des champs contributeurs, masquage, consentement
  - **Coûts tokens** → routing + réduction prompt + mémoire résumée
  
  ------
  
  ## Conclusion
  
  Ce design transforme le POC en un MVP **déployable, mesurable et itératif**, en adressant explicitement :
  
  - la **mémoire** (STM/LTM + checkpointing),
  - le **géographique** (PostGIS + ranking),
  - le **temps réel** (web tool + ingestion fréquente),
  - le **monitoring/LLMOps** (CloudWatch/CloudTrail + LangSmith + Phoenix + feedback).