

# CHAPITRE : CONCEPTION ARCHITECTURALE ET CHOIX TECHNOLOGIQUES BACKEND

## 1. Introduction et Cadre de l’Étude

Le développement de la plateforme de gouvernance et d'audit (`PCI Conformity Manager`) mobilise des modules à fortes contraintes fonctionnelles et techniques (`accounts`, `frameworks`, `campaigns`, `assessments`, `evidence`, `audit_log`). L'orchestration des processus de conformité implique l'exécution de pipelines d'Intelligence Artificielle (notamment *Docling* pour l'extraction documentaire), la génération de rapports dynamiques complexes et la synchronisation de collecteurs de données.

Pour une équipe de développement compacte (deux ingénieurs), la stack technique doit garantir une vélocité maximale tout en répondant aux exigences de performance et de sécurité (*by-design*). L’analyse des choix technologiques s'articule autour de l’adoption de **Django 5.2 (avec DRF)** adossé à un couplage asynchrone **Celery + Redis (ou Valkey)**.

---

## 2. Analyse Comparative et Justification du Framework Principal

Plusieurs options architecturales majeures ont été évaluées pour structurer le cœur applicatif du système.

### A. Django 5.2 + DRF (Django Rest Framework) — *Option Retenue*

Django adopte une philosophie « *batteries-included* » (tout inclus), fournissant nativement l’ensemble des briques nécessaires à un back-office d’entreprise : un ORM performant, un système de migrations transactionnelles, un module d'authentification robuste et un framework de gestion des permissions orienté rôle (RBAC). Le *Django Rest Framework* (DRF) complète cette architecture en exposant des API REST sécurisées.

* **Points forts :**
* **Intégration et Cohérence de l'Écosystème :** Python s’impose comme la *lingua franca* du traitement de données et de l’IA. Le même langage orchestre l’API, l’évaluation des jeux de test, l’intégration des modèles (Docling) et la génération de rapports.
* **L’Admin Django natif :** Il offre gratuitement une interface de back-office sécurisée permettant d'administrer et d'importer les référentiels réglementaires sans nécessiter le développement d'une interface spécifique.
* **Sécurité native :** Protections par défaut contre le Top 10 OWASP (injections SQL, failles CSRF, XSS, détournement de sessions).


* **Points faibles :**
* **Performance I/O brute modeste :** Le modèle synchrone par défaut de Django consomme davantage de ressources (threads/mémoire) sous forte charge concurrente par rapport aux environnements asynchrones purs.
* **Mitigation :** Ce verrou technologique est pallié par l'intégration d'un découplage asynchrone via Celery pour les tâches lourdes.



### B. FastAPI

Construit sur Starlette et Pydantic, FastAPI est un framework asynchrone ultra-rapide doté d'une génération de documentation OpenAPI automatique.

* **Évaluation :** Bien que ses performances en entrées/sorties (I/O) soient excellentes et sa popularité croissante, FastAPI ne propose aucun module d’administration natif, de gestion de permissions préétablie ou de système de migration intégré. L'assemblage de ces briques (SQLAlchemy, Alembic, systèmes de sécurité) aurait exigé l'écriture d'un volume important de code « de plomberie » sécuritaire, augmentant la surface d'attaque et la dette technique pour un binôme.
* **Positionnement stratégique :** FastAPI conserve toutefois toute sa pertinence s'il est confiné à un rôle de micro-service d’inférence IA isolé (comme envisagé dans les choix technologiques globaux).

### C. Spring Boot 4 (Java) & ASP.NET Core 10 (C#)

* **Évaluation :** Des solutions d'une grande robustesse, standardisées dans le secteur bancaire, dotées d'une sécurité d'entreprise mature (Spring Security).
* **Limites :** L'écosystème d'extraction documentaire et d'intelligence artificielle est historiquement moins natif et plus complexe à interconnecter en Java qu'en Python. De plus, la verbosité de Java impose une charge de développement disproportionnée pour une équipe de deux personnes, ces technologies n'étant retenues qu'en cas de contrainte réglementaire stricte imposant la JVM (ex: exigences du MNA).

### D. Rust (Axum ou Loco)

* **Évaluation :** Axum (couche fine sur Hyper/Tower) et Loco (inspiré de Rails) garantissent une sécurité mémoire absolue et des performances brutes de premier plan.
* **Limites :** L’écosystème Rust demeure jeune (versions `0.x` avec des APIs mouvantes). Aucun ORM ne rivalise avec la maturité de l’Admin Django. Surtout, la génération de rapports Word complexes — un livrable central du projet — repose sur des bibliothèques (`docx-rs`, `rdocx`) immatures face à la robustesse des moteurs Python (`docxtpl`, `WeasyPrint`). Rust a donc été écarté pour le monolithe applicatif afin de ne pas compromettre les délais de livraison.

| Critère d'Évaluation | Django 5.2 + DRF | FastAPI | Spring Boot 4 | Rust (Axum/Loco) |
| --- | --- | --- | --- | --- |
| **Vitesse de développement** | Maximale | Élevée | Modérée (Verbosité) | Faible (Complexité) |
| **Back-office / Admin** | Natif (Intégré) | Absent | À assembler | Absent / Partiel |
| **Écosystème IA / Docling** | Natif (Python) | Natif (Python) | Complexe (Passerelles) | Jeune / Complexe |
| **Performance I/O brute** | Modeste (Synchrone) | Excellente | Très élevée | Maximale |
| **Arbitrage pour le PFE** | **Retenu** | Écarté (Monolithe) | Écarté (Équipe 2 pers.) | Écarté (Immaturité doc) |


---

## 3. Gestion de l’Asynchronisme : Étude Comparative des Files de Tâches

La nature synchrone par défaut de Django implique qu’une requête HTTP lourde (telle que l’analyse de preuves textuelles par un modèle d’IA ou la compilation d’un rapport volumineux) monopolise un thread applicatif, entraînant des risques de saturation sous forte charge. Pour pallier cette limite d'I/O sans basculer vers une complexité micro-service prématurée, l'architecture recourt à une file d'attente de tâches asynchrones.

Quatre approches principales ont été évaluées pour l'environnement du projet :

### A. Étude comparative des solutions de tâches de fond

| Solution | Positionnement & Maturité | Avantages | Inconvénients / Limites pour le PFE | Arbitrage |
| --- | --- | --- | --- | --- |
| **Celery (+ Redis)** | Standard de de fait de l'écosystème Django. | Extrêmement robuste, gestion native des planifications complexes (*Celery Beat*), politiques de *retries* (rejeux) avancées, scalabilité horizontale mature. | Configuration initiale plus dense que des solutions minimalistes. | **Retenu** (Idéal pour des pipelines IA longs et planifiés). |
| **Dramatiq / RQ** | Alternatives plus légères et orientées simplicité de syntaxe. | Courbe d'apprentissage initiale plus douce, API épurée. | Moins riches en fonctionnalités natives (absence de planification robuste équivalente à Beat, écosystème d'extensions plus restreint). | **Écarté** (Fonctionnalités insuffisantes pour la complexité des campagnes d'audit). |
| **Django Tasks** | Module de tâches asynchrones natif introduit dans les versions récentes de Django. | Intégration transparente, pas de dépendance à un écosystème tiers complexe. | Encore en phase d'adoption ; **moins mature pour des pipelines IA longs**, traitements lourds et gestion fine de la concurrence. | **Écarté** (Risque technique trop élevé pour les processus critiques du PFE). |

### B. Justification du choix de Celery

Celery s'impose comme le choix idoine. Dans un contexte d'audit de conformité, l'application doit gérer des tâches de fond longues (exécution de scripts d'évaluation de la conformité, inférences Docling). Celery garantit un découplage total entre le cycle de vie de la requête HTTP et le traitement de la donnée, tout en permettant une planification rigoureuse des contrôles automatisés via *Celery Beat*.

---

## 4. Choix du Courtier de Messages (Message Broker) : Redis / Valkey

Pour interconnecter l'application Django (le producteur) et les workers Celery (les consommateurs), un courtier de messages est indispensable. L'architecture retient explicitement **Redis** (ou son alternative open-source compatible **Valkey**).

### A. Justification technique et fonctionnelle

1. **Architecture en mémoire vive (RAM) :** En stockant les structures de données en mémoire, Redis offre des temps de latence de l'ordre de la microseconde, garantissant un passage de témoin quasi-instantané entre l'application web et les workers d'arrière-plan.
2. **Mutualisation des rôles (Double usage) :** Redis ne sert pas uniquement de courtier pour Celery ; il fait également office de **système de cache haute performance** pour Django (accélération des requêtes sur les référentiels et les tableaux de bord multi-tenants). Cette mutualisation simplifie l'architecture globale sur le serveur de déploiement (VPS).
3. **Alternative moderne (Valkey) :** L'intégration potentielle de *Valkey* (fork open-source soutenu par la Linux Foundation) garantit la pérennité technologique, la compatibilité stricte avec l'API Redis et une gouvernance neutre face aux évolutions de licences de marché.

### B. Pourquoi écarter les alternatives ?

* **Bases de données relationnelles (ex: PostgreSQL comme broker) :** Utiliser la base de données principale pour gérer les files de messages de Celery génère un verrouillage excessif des tables (*table locks*), dégrade les performances globales de l'ORM et s'avère inadapté face à des flux de messages à haute fréquence.
* **RabbitMQ :** Bien qu'incontestablement supérieur sur la garantie de persistance absolue en environnements bancaires critiques, RabbitMQ introduit une surcharge opérationnelle (consommation mémoire plus élevée, complexité de configuration des *Exchanges* et *Bindings*) non justifiée pour le dimensionnement et la vélocité requises par un projet de fin d'études mené en binôme. Redis/Valkey offre le rapport simplicité/performance optimal.

---

## 5. Synthèse et Validation Globale de la Stack Backend

L'architecture validée repose sur le triptyque :


$$\text{Django 5.2 / DRF} \quad \longleftrightarrow \quad \text{Redis / Valkey} \quad \longleftrightarrow \quad \text{Celery Workers}$$

* **La couche synchrone (Django + DRF)** garantit la sécurité applicative (protection OWASP, RBAC, gestion des tenants) et la rapidité de gestion des interfaces d'administration et des API.
* **La couche asynchrone (Celery + Redis/Valkey)** absorbe l'ensemble des traitements lourds (analyse documentaire par Docling, calculs de scores d'évaluation, génération de rapports de conformité) en s'affranchissant des contraintes de performance d'I/O synchrone de Python.

Cette cohérence technique offre un équilibre parfait entre robustesse industrielle et agilité de développement, répondant rigoureusement aux exigences académiques et professionnelles du projet.






# 6. Validation de l’Interface Utilisateur (Frontend) : Next.js 15 / React 19 & TypeScript

## 6.1. Problématique Ergonomique et Architecturale

Dans le cadre d’une plateforme de gouvernance, d’audit et de conformité (*PCI Conformity Manager*), l’interface utilisateur ne se limite pas à de simples formulaires de saisie. Elle doit restituer des volumes importants de données analytiques et hiérarchiques : tableaux de bord de suivi des campagnes d'audit, matrices d'évaluation multi-critères (PCI-DSS / GIE-Monétique) dotées de filtres dynamiques, visualisation graphique de l'évolution des scores de conformité, ainsi que la gestion fluide des preuves documentaires (`evidence`).

Pour répondre à ces exigences d'interactivité tout en maintenant une séparation stricte entre la logique métier (exposée par l'API Django REST Framework) et la couche de présentation, l'architecture retient une approche découplée fondée sur le triptyque **Next.js 15, React 19 et TypeScript**.

## 6.2. Justification Technique de la Pile Frontend

L'articulation de ces trois technologies repose sur une complémentarité fonctionnelle rigoureuse :

* **React 19 (Moteur de rendu par composants) :** Permet de structurer l'interface en composants modulaires, isolés et réutilisables (grilles d'audit, indicateurs de conformité, sélecteurs de preuves). Son DOM virtuel garantit des mises à jour visuelles fluides et ciblées sans rechargement global de l'application.
* **Next.js 15 (Framework d'orchestration et de routage) :** Encadre React en fournissant un routage structuré, une gestion optimisée du cycle de vie des requêtes et un rendu hybride combinant le *Server-Side Rendering* (SSR) pour la rapidité du chargement initial et le *Client-Side Rendering* (CSR) pour la réactivité des interactions utilisateur.
* **TypeScript (Sécurisation par typage statique fort) :** Impose un contrat de données strict entre le frontend et les schémas JSON renvoyés par Django REST Framework. Le contrôle des types à la compilation réduit considérablement les erreurs d'exécution liées à la manipulation d'objets complexes (référentiels, résultats d'évaluation IA) et facilite la maintenabilité du code au sein du binôme.
* **Richesse de l’écosystème UI (Tableaux et Graphes analytiques) :** L'adoption de React ouvre l'accès aux bibliothèques standards de l'industrie pour la visualisation de données et l'ergonomie d'entreprise, telles que **TanStack Table** (pour le tri, la pagination et le filtrage avancé des grilles d'exigences), **Recharts** (pour la restitution graphique des taux de conformité et des écarts d'audit), ainsi que **Tailwind CSS** et **shadcn/ui** pour garantir une interface moderne, accessible et homogène.

## 6.3. Étude Comparative des Alternatives Frontend

Quatre approches architecturales ont été évaluées afin de valider la pertinence de ce choix :

| Critère d'Évaluation | Next.js 15 / React 19 + TS | Django Templates + HTMX + Alpine.js | Vue 3 / Nuxt 3 + TS | Angular 19 |
| --- | --- | --- | --- | --- |
| **Paradigme Architectural** | Découplé (*API-First*, Hybride SSR/CSR) | Monolithique (Rendu HTML serveur) | Découplé (*API-First*, Hybride SSR/CSR) | Découplé (*API-First*, SPA stricte) |
| **Richesse UI (Tableaux, Graphes)** | **Maximale** (TanStack, Recharts, shadcn/ui) | Modérée (Limité sur les états clients complexes) | Élevée (PrimeVue, Nuxt UI) | Élevée (Angular Material) |
| **Complexité d'Intégration** | Modérée (Gestion CORS, auth JWT/Cookies) | **Faible** (Sessions natives Django) | Modérée (Gestion CORS, auth) | Élevée (Courbe d'apprentissage RxJS) |
| **Arbitrage pour le PFE** | **Retenu** | Écarté | Écarté | Écarté |

### Justification du rejet des alternatives :

* **Django Templates + HTMX + Alpine.js :** Bien que cette approche monolithique offre une vélocité initiale élevée et simplifie l'authentification (absence de problématiques *Cross-Origin Resource Sharing* - CORS), elle montre ses limites dès lors que l'interface requiert une gestion d'état complexe côté client, notamment pour les tableaux de bord analytiques interactifs, les graphiques dynamiques et les matrices d'évaluation à filtres multiples.
* **Vue 3 / Nuxt 3 :** Offre des capacités techniques et une philosophie très proches de l'écosystème React/Next.js avec une syntaxe appréciée pour sa clarté. Toutefois, l'écosystème React demeure plus vaste en matière de composants spécialisés pour les applications de gouvernance et d'analyse de données, justifiant la préférence accordée à Next.js.
* **Angular 19 :** Bien que particulièrement structuré et répandu dans les grands comptes bancaires, son architecture rigide et sa verbosité (imposant notamment la maîtrise de la programmation réactive via RxJS) représentent une charge d'apprentissage et de développement disproportionnée pour une équipe de deux ingénieurs.






---

# CHAPITRE : PERSISTANCE DES DONNÉES ET ARCHITECTURE MULTI-TENANT

## 1. Exigences et Contraintes Métiers

La plateforme de gouvernance et d'audit (*PCI Conformity Manager*) manipule des données hautement sensibles et réglementées (référentiels PCI-DSS v4.0.1, GIE Monétique, comptes utilisateurs, campagnes d'évaluation, preuves documentaires et journaux d'audit immuables `audit_log`).

La couche de persistance doit répondre à trois impératifs majeurs :

1. **Intégrité transactionnelle stricte (ACID) :** Garantir qu'une campagne d'audit ou qu'une modification de score ne puisse pas se retrouver dans un état incohérent en cas de panne.
2. **Isolation rigoureuse des locataires (Multi-Tenancy) :** Dans une optique SaaS ou multi-organisationnelle, les données de chaque entité auditée doivent être hermétiquement cloisonnées afin d'éviter toute fuite d'informations inter-entreprises.
3. **Flexibilité de modélisation :** Capacité à stocker des structures de données semi-structurées (comme les métadonnées dynamiques extraites par l'IA *Docling* à partir des documents de preuve).

---

## 2. Choix du Système de Gestion de Base de Données (SGBD) : PostgreSQL

L’arbitrage technique s'est porté sur **PostgreSQL** comme SGBD relationnel principal du monolithe Django.

### A. Justification technique

* **Maturité et conformité relationnelle :** PostgreSQL est le SGBD de référence de l'écosystème Django, offrant une compatibilité native et optimisée via l'ORM Django.
* **Support natif des types JSON/JSONB :** Crucial pour stocker et indexer efficacement les structures de données flexibles (résultats d'analyse de l'IA, configurations de formulaires d'évaluation personnalisables) sans alourdir le schéma relationnel figé.
* **Sécurité et cloisonnement avancé :** PostgreSQL intègre des mécanismes puissants de contrôle d'accès au niveau des lignes (*Row-Level Security* - RLS) et une gestion granulaire des rôles et des schémas.

### B. Étude comparative des SGBD

| Critère d'Évaluation | PostgreSQL | MySQL / MariaDB | MongoDB (NoSQL) |
| --- | --- | --- | --- |
| **Intégrité ACID & Relationnel** | **Excellente** (Standard industriel) | Très bonne | Faible (Orienté documents / BASE) |
| **Support de données JSON** | **Avancé** (Type `JSONB` indexé) | Bon (Amélioré récemment) | Natif |
| **Intégration Django / ORM** | **Native et optimale** | Très bonne | Limitée (Nécessite des ODM tiers) |
| **Adaptabilité à l'audit de conformité** | **Maximale** (Garanties transactionnelles fortes) | Bonne | Inadaptée pour des relations strictes |

*Justification du rejet des alternatives :* MongoDB a été écarté en raison de l'absence de garanties transactionnelles ACID rigoureuses sur des structures relationnelles complexes (liaisons entre `frameworks`, `campaigns` et `assessments`), indispensables pour un outil d'audit réglementaire.

---

## 3. Stratégie d’Isolation Multi-Tenant

Pour répondre au besoin de segmentation des environnements clients, deux grandes approches d'architecture multi-tenant ont été comparées :

### A. Approche 1 : Base de données isolée par tenant (*Database-per-Tenant*)

* **Principe :** Chaque client dispose d'une base de données physique totalement séparée.
* **Avantages :** Sécurité et étanchéité maximales des données ; facilité de sauvegarde/restauration par client.
* **Inconvénients :** Surcharge opérationnelle lourde, complexité accrue des migrations de schémas (ex: appliquer une modification de table Django sur des dizaines de bases distinctes).

### B. Approche 2 : Isolation par Schéma Logique au sein d'une même base (*Schema-per-Tenant* / `django-tenants`)

* **Principe :** Une seule instance PostgreSQL, mais un **schéma SQL distinct par client** (ex: `schema_client_a`, `schema_client_b`), géré dynamiquement par le routeur de requêtes de Django.
* **Avantages :** Bon compromis entre isolation stricte (les tables sont physiquement séparées par des espaces de noms SQL) et mutualisation des coûts d'infrastructure.
* **Inconvénients :** Gestion des connexions et des migrations un peu plus complexe.

### C. Approche 3 : Table partagée avec discriminant de tenant (`tenant_id` / Shared Schema)

* **Principe :** Toutes les données résident dans les mêmes tables, filtrées en permanence par une clé étrangère `tenant_id` portée par chaque modèle.
* **Avantages :** Simplicité absolue de développement et de migration.
* **Inconvénients :** Risque critique de fuite de données en cas d'oubli d'un filtre `tenant_id` dans une requête ORM (faille de sécurité majeure dans un contexte d'audit).

### D. Arbitrage retenu pour le projet

Pour concilier la sécurité réglementaire (exigée par les normes d'audit) et la faisabilité technique pour un binôme, le projet s'oriente vers un **cloisonnement logique par schéma (`Schema-per-Tenant`)** ou une isolation applicative stricte via un filtrage centralisé par `tenant_id` renforcé par des politiques de sécurité au niveau des lignes de PostgreSQL (`Row-Level Security`), garantissant qu'aucun locataire ne puisse accéder aux données d'un tiers.

---

## 4. Synthèse Globale de l’Architecture du Projet

L’intégration de la base de données finalise la validation de la pile technique complète de la plateforme *PCI Conformity Manager* :

$$\text{Next.js / TS (Frontend)} \quad \longleftrightarrow \quad \text{Django 5.2 / DRF (Backend)} \quad \longleftrightarrow \quad \text{PostgreSQL (Persistance \& Schemas)}$$

$$\text{et découplage asynchrone via } \quad \longleftrightarrow \quad \text{Celery} + \text{Redis / Valkey}$$

Cette architecture confère au système la robustesse, la scalabilité et le niveau de sécurité indispensable pour un logiciel de gouvernance et de conformité de niveau professionnel.

---


---

# CHAPITRE : ARCHITECTURE DE STOCKAGE DES PREUVES ET GESTION DES ARTEFACTS DOCUMENTAIRES

## 1. Contexte et Enjeux de la Gestion des Preuves

La plateforme *PCI Conformity Manager* repose sur un module central dédié à la collecte et à l'analyse des preuves de conformité (`evidence`). Dans le cadre des normes de sécurité bancaire (PCI-DSS v4.0.1), chaque exigence réglementaire doit être étayée par des artefacts tangibles : documents de politiques de sécurité, rapports de vulnérabilités au format PDF, captures d’écran d’infrastructure ou fichiers de configuration brute.

Le système de stockage associé doit répondre à des contraintes strictes :

* **Étanchéité et Sécurité Multi-Tenant :** Empêcher absolument qu’un locataire (*tenant*) puisse accéder aux preuves documentaires d'un autre organisme.
* **Interopérabilité avec l'IA (*Docling*) :** Permettre un accès rapide et direct aux fichiers binaires pour les scripts d'extraction et d'analyse textuelle automatisée.
* **Scalabilité et Intégrité :** Dissocier les flux binaires lourds de la base de données relationnelle pour garantir les performances globales du système.

---

## 2. Analyse Comparative des Solutions de Stockage

Trois options majeures ont été évaluées pour l'hébergement des fichiers de preuves :

### A. Stockage local sur le système de fichiers du serveur (Local Filesystem)

* **Principe :** Les fichiers sont sauvegardés directement dans un répertoire du disque dur du VPS (ex: `/media/evidence/`).
* **Avantages :** Simplicité de mise en œuvre initiale, aucune infrastructure tierce requise.
* **Inconvénients :** Inadapté à une architecture extensible ou cloud, complexité de gestion des permissions en cas de montée en charge, et vulnérabilité en cas de migration ou de redémarrage de conteneur.

### B. Stockage Objet compatible S3 (ex: MinIO ou AWS S3) — *Option Retenue*

* **Principe :** Les fichiers sont stockés sous forme d'objets dans un espace bucket hautement sécurisé, accessible via une API standardisée (protocole S3). Dans le cadre d’un déploiement sur VPS souverain (auto-hébergé), **MinIO** constitue la solution open-source de référence.
* **Avantages :**
* Isolation logique parfaite des objets par bucket ou par préfixe de dossier lié au *tenant_id*.
* Génération d'URL présignées (*Pre-signed URLs*) : le backend génère un lien d'accès temporaire et sécurisé pour télécharger une preuve sans exposer directement le serveur de stockage.
* Parfaite adéquation avec les pipelines asynchrones Celery et l'IA *Docling*, qui peuvent télécharger les fichiers à la demande.


* **Inconvénients :** Légère surcharge d'infrastructure (déploiement d'un service conteneurisé supplémentaire).

### C. Stockage Binaire direct en Base de Données (Champs BLOB PostgreSQL)

* **Principe :** Insérer les fichiers sous forme de flux binaires directement dans les tables relationnelles.
* **Avantages :** Centralisation totale des données.
* **Inconvénients :** Engorgement rapide de la base de données, dégradation critique des performances des requêtes de l'ORM, et complexité accrue des sauvegardes (`pg_dump`). Solution vigoureusement rejetée pour des fichiers documentaires lourds.

| Critère d'Évaluation | Stockage Local (Disque VPS) | Stockage Objet S3 / MinIO | Stockage en Base (BLOB) |
| --- | --- | --- | --- |
| **Scalabilité & Évolutivité** | Faible | **Excellente** | Médiocre |
| **Sécurité & Liens Temporaires** | Complexe à coder | **Natif (URL présignées)** | Géré par l'application |
| **Intégration Pipeline IA (Docling)** | Moyenne | **Optimale (Accès flux)** | Lourde |
| **Impact sur la Base de Données** | Aucun | **Nul (Désacouplé)** | Critique (Surcharge) |
| **Arbitrage pour le PFE** | Écarté | **Retenu** | Écarté |

---

## 3. Synthèse de l'Architecture Globale et Flux de Traitement d’une Preuve

L’intégration du stockage objet finalise la chaîne de traitement fonctionnelle de la plateforme :

1. **Téléversement :** L'utilisateur soumet une preuve via l'interface Next.js. Le fichier transite par l'API sécurisée Django.
2. **Enregistrement :** Django stocke les métadonnées dans **PostgreSQL** (nom, taille, hachage SHA-256, lien vers le *tenant*) et transfère le fichier binaire vers le serveur **MinIO (S3)**.
3. **Traitement asynchrone :** Django émet une tâche via **Celery / Redis**, signalant au worker qu'un nouveau document est disponible.
4. **Analyse IA :** Le worker récupère le fichier depuis le stockage objet, l'injecte dans le moteur **Docling** pour l'extraction des clauses textuelles et met à jour le score de conformité de l'évaluation correspondante.

Cette architecture garantit la robustesse, la sécurité des données sensibles et la modularité requises pour un système d'information de gouvernance de niveau professionnel.

---


















---

## 6. Références Bibliographiques (Standards et Documentation Technique)

1. **Django Software Foundation.** *Django 5.2 Release Notes and Asynchronous Support Documentation*. Disponible sur : `docs.djangoproject.com` (Consulté en 2026).
2. **Celery Project.** *Celery: Distributed Task Queue Documentation*, version 5.x. Disponible sur : `docs.celeryq.dev`.
3. **The Linux Foundation.** *Valkey: An Open-Source High-Performance Data Store (Fork & Community Governance)*. Documentation technique officielle, 2025–2026. Disponible sur : `valkey.io`.
4. **OWASP Foundation.** *OWASP Top Ten: The Ten Most Critical Web Application Security Risks*. Standards de sécurité applicative.

### 7.2. Références Bibliographiques Complémentaires (Frontend)

5. **Vercel Inc.** *Next.js 15 Documentation: App Router, Server Components and Hybrid Rendering*. Disponible sur : `nextjs.org/docs` (Consulté en 2026).
6. **Meta Open Source.** *React 19 Official Documentation: Component Architecture and State Management*. Disponible sur : `react.dev` (Consulté en 2026).
7. **Microsoft Corporation.** *TypeScript Handbook: Static Type Checking for JavaScript Applications*. Disponible sur : `typescriptlang.org/docs`.
8. **W3C (World Wide Web Consortium).** *Fetch Standard & Cross-Origin Resource Sharing (CORS) Protocol Specification*. Standards du Web.

## 5. Références Bibliographiques (Persistance et Multi-Tenancy)

9. **PostgreSQL Global Development Group.** *PostgreSQL Documentation: JSON Types, Row Security Policies and Schema Management*. Disponible sur : `postgresql.org/docs`.
10. **Django Software Foundation.** *Django Database Access, Migrations and Multi-database Routing Guides*. Disponible sur : `docs.djangoproject.com`.
11. **Percona / Enterprise Database Standards.** *Architectural Patterns for Multi-Tenant Data Isolation in SaaS Applications*. Technical Whitepaper.


## 4. Références Bibliographiques (Stockage et Sécurité Documentaire)

12. **MinIO Inc.** *MinIO Object Storage Architecture and S3 API Compatibility Documentation*. Disponible sur : `min.io/docs`.
13. **OWASP Foundation.** *File Upload Security Cheat Sheet: Preventing Malicious File Execution and Unsafe Storage*. Standards de sécurité des applications web.
14. **Cloud Storage Standards Working Group.** *Object Storage vs. Block Storage Architectural Patterns for Enterprise Compliance Systems*. Technical Journal, 2025–2026.




