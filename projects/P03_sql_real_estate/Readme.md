## P3 — SQL & création de BDD (Laplace Immo)  
**Titre :** *Entraînez-vous avec SQL et créez votre BDD* (Laplace Immo)  
**Repo :** `https://github.com/rudydesplan/P03_sql_real_estate`

### Contexte
Laplace Immo souhaite disposer d’un **POC** de base relationnelle pour analyser les transactions immobilières : évolution du **prix au m²**, volumes de ventes, comparaisons par zones géographiques, et indicateurs de marché. Les données proviennent de fichiers structurés qu’il faut **documenter**, **modéliser**, puis **interroger** de manière fiable.

### Objectif
- Construire une base relationnelle exploitable pour l’analyse immobilière (transactions, localisation, caractéristiques).
- Produire un **dictionnaire de données** clair et complet, compatible avec un usage en équipe.
- Concevoir un **schéma normalisé (3NF)** avec intégrité référentielle (PK/FK) et contraintes.
- Charger les données (import CSV) avec contrôles de cohérence, puis répondre aux questions métier via des requêtes SQL.

### Démarche
- **Compréhension & dictionnaire de données** : identification des entités, attributs, granularité, champs sensibles (RGPD) et définitions univoques.
- **Modélisation** : conception du schéma relationnel en **3NF**, choix et justification des clés (PK/FK), gestion des dépendances fonctionnelles et de la redondance.
- **Implémentation** : création des tables, définition des types, contraintes (NOT NULL, CHECK, FK), préparation des fichiers, import CSV.
- **Contrôles post-import** : vérification des volumes, intégrité (orphan rows), validité des types, détection d’anomalies (valeurs hors domaines).
- **Requêtage analytique** : rédaction d’une requête par besoin métier (ex. prix/m², top régions, tendances), avec bonnes pratiques (alias, CTE/sous-requêtes, jointures maîtrisées).

### Outils
- **SQL** (selon environnement : SQLite / MySQL / PostgreSQL)
- **Tableur** (Excel / Google Sheets) pour dictionnaire de données et contrôles rapides
- **Outil de modélisation** (ex. SQL Power Architect / dbdiagram / draw.io) pour schémas et documentation

### Compétences démontrées
- Modélisation relationnelle : **normalisation 1NF/2NF/3NF**, identification des dépendances, réduction de la redondance
- Conception de schéma robuste : PK/FK, contraintes, typage, intégrité référentielle
- Import contrôlé (CSV → DB) + vérifications de cohérence et qualité
- SQL “production-ready” : requêtes lisibles, justifiées, structurées (CTE), jointures correctes

### Résultats & valeur ajoutée (ce que ça apporte)
- Une base de données **fiable et documentée**, prête à être reprise par une équipe (dictionnaire + schéma + requêtes).
- Des analyses reproductibles sur les transactions : prix/m², comparaisons géographiques, volumes et tendances.
- Un socle POC qui peut évoluer vers un environnement BI (vues matérialisées, index, data marts).

### Recul (apprentissage clé)
- Une modélisation “propre” (3NF + contraintes + contrôles post-import) évite les erreurs silencieuses : colonnes inversées, valeurs exclues, incohérences de codification (ex. 2A/2B), et garantit que les requêtes analytiques reposent sur des bases solides.
