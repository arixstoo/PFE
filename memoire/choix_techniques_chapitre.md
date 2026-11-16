# CHAPITRE — CHOIX TECHNOLOGIQUES ET ARCHITECTURE DE LA PLATEFORME

<p align="center"><img src="nostra_logo.png" alt="Logo NOSTRA" width="360"></p>

> Plateforme : **NOSTRA** (*Mesurez · Scorez · Maîtrisez*) — gestion des campagnes d'audit (spécialement PCI DSS v4.0.1 / GIE Monétique), avec analyse des preuves par IA locale.

---

## 1. Contraintes qui pilotent les choix

Chaque technologie retenue répond à au moins une de ces contraintes :

| # | Contrainte | Conséquence technique |
|---|---|---|
| K1 | **Deux modes** : service (MNA audite ses clients) et SaaS sous licence | Un seul code, livré en **une instance dédiée par organisation** (modèle Silo) |
| K2 | **On-premise, sans Internet** | Tout en conteneurs Docker, aucun CDN ni API cloud |
| K3 | **IA locale** : aucune preuve envoyée à une API publique (loi 18-07) | LLM, embeddings et extraction exécutés sur le serveur |
| K4 | **Isolation stricte** entre organisations | Isolation **physique** : base, stockage et conteneurs propres à chaque organisation |
| K5 | **Sécurité by design** | Frameworks avec protections natives, antivirus, secrets en coffre |
| K6 | **Produit vendable** | Licences permissives (éviter l'AGPL dans le cœur) |
| K7 | **Binôme, ~7 mois** | Technologies matures, bien documentées, rapides à maîtriser |

### Méthode de décision

Chaque option a été notée de 1 à 5 sur six critères pondérés :

```mermaid
pie showData title Poids des critères de décision (%)
    "Sécurité" : 25
    "Adéquation au besoin" : 20
    "Productivité du binôme" : 20
    "Déploiement on-prem + SaaS" : 15
    "Licence commerciale" : 10
    "Écosystème / pérennité" : 10
```

---

## 2. Vue d'ensemble de la stack retenue

```mermaid
mindmap
  root((NOSTRA))
    Interface
      Vite + React
      TypeScript
      shadcn/ui, TanStack, Recharts
    Cœur métier
      Django 5.2 LTS
      Django REST Framework
    Données
      PostgreSQL 16
      pgvector
      Modèle Silo
    Asynchrone
      Celery + Beat
      Redis
    Fichiers
      API S3
      SeaweedFS
    Sécurité
      Nginx
      ClamAV
      OpenBao
      Connexion sans mot de passe (OTP e-mail)
    IA locale
      Docling
      BGE-M3
      Ollama / vLLM
      Qwen 14B / classe 27B
    Rapports
      docxtpl
      WeasyPrint
      openpyxl
    Déploiement
      Docker Compose
```

| Couche | Choix retenu | Principale alternative écartée | Raison du choix |
|---|---|---|---|
| Back-end | **Django 5.2 LTS + DRF** | FastAPI | Auth, permissions, admin, migrations et protections OWASP déjà fournis |
| Front-end | **Vite + React + TypeScript** (SPA) | Next.js, Django Templates + HTMX | Tableaux de bord riches ; build statique servi par Nginx, sans serveur Node |
| Base de données | **PostgreSQL 16 + pgvector** | MySQL, MongoDB | ACID, JSONB et vecteurs dans une seule base |
| Multi-tenant | **Silo** : une instance complète par organisation | Pool (base partagée), Bridge (schéma par client) | Isolation physique, aucune fuite possible entre organisations |
| Tâches de fond | **Celery + Redis** | Dramatiq, RQ, Django Tasks | Planification (Beat), rejeux, maturité |
| Stockage preuves | **SeaweedFS** (API S3) | MinIO, disque local, BLOB en base | Licence Apache 2.0, compatible S3, URL présignées, une instance par Silo |
| Reverse proxy | **Nginx** | Caddy, Traefik | Standard, contrôle fin des en-têtes et limites d'upload |
| Antivirus | **ClamAV** (sous réserve MNA) | Antivirus fourni par MNA | Open source, intégrable en tâche Celery |
| Secrets | **`.env` (dev) → OpenBao (prod)** | HashiCorp Vault | Vault est sous licence BSL depuis 2023 |
| Extraction | **Docling** | PyMuPDF | MIT, conserve la structure (pages, tableaux) ; PyMuPDF est AGPL |
| Embeddings | **BGE-M3** | multilingual-e5 | Multilingue FR / EN / AR, 1024 dimensions |
| Runtime LLM | **Ollama** (dev) / **vLLM** (prod GPU) | API cloud | IA locale ; API compatible OpenAI → changement par configuration |
| Rapports | **docxtpl + WeasyPrint + openpyxl** | ReportLab | Modèle Word MNA éditable sans code |
| Déploiement | **Docker Compose** | Kubernetes | Suffisant pour un serveur, livrable on-prem |

---

## 3. Back-end : Django 5.2 LTS + Django REST Framework

**Pourquoi Django ?** Il fournit en natif tout ce qu'un back-office d'audit exige : ORM et migrations, authentification, permissions par rôle (RBAC), interface d'administration (import des référentiels sans écran à développer) et protections contre les failles OWASP courantes (injection SQL, CSRF, XSS, clickjacking). Python est aussi le langage de l'IA, de l'extraction documentaire et des rapports : **un seul langage pour tout le back-end**. La version 5.2 est **LTS** (support de sécurité jusqu'en avril 2028), ce qui couvre la durée du projet et sa mise en production.

**Point faible et parade :** Django est synchrone par défaut, donc peu adapté aux traitements longs dans une requête HTTP → ces traitements sont déportés dans des workers **Celery** (§ 6).

```mermaid
xychart-beta
    title "Back-end : score pondéré (sur 5)"
    x-axis ["Django + DRF", "FastAPI", "Spring Boot", "ASP.NET Core", "NestJS"]
    y-axis "Score" 0 --> 5
    bar [4.85, 4.30, 4.05, 3.95, 3.90]
```

| Critère | Django + DRF | FastAPI | Spring Boot (Java) | Rust (Axum / Loco) |
|---|---|---|---|---|
| Vitesse de développement | **Maximale** | Élevée | Modérée (verbeux) | Faible |
| Admin / auth / permissions | **Natifs** | À assembler | Spring Security (mature) | Absents / partiels |
| Écosystème IA et documents | **Natif (Python)** | Natif (Python) | Passerelles nécessaires | Jeune |
| Performance I/O brute | Modeste | Excellente | Très élevée | Maximale |
| **Décision** | **Retenu** | Écarté pour le cœur | Écarté (charge pour un binôme) | Écarté (bibliothèques Word immatures) |

---

## 4. Front-end : Vite + React + TypeScript

L'interface doit afficher des **tableaux de bord de campagne**, des **matrices d'évaluation filtrables** (PCI DSS / GIE), l'**évolution des scores** et la gestion des preuves.

| Brique | Rôle |
|---|---|
| **React** | Interface en composants réutilisables (grille d'audit, indicateurs, sélecteur de preuves) |
| **Vite** | Outil de build et serveur de développement (rechargement instantané). En production, `vite build` produit des **fichiers statiques** (HTML, JS, CSS) servis directement par Nginx, **sans serveur Node ni CDN** (K2) |
| **React Router** | Routage côté client de l'application monopage (SPA) |
| **TypeScript** | Contrat de types avec les réponses JSON de l'API DRF → moins d'erreurs à l'exécution |
| **shadcn/ui + Tailwind CSS** | Composants accessibles et cohérents |
| **TanStack Table / Query** | Tri, filtres, pagination des exigences ; cache des appels API |
| **Recharts** | Graphiques de conformité et d'écarts |

| Critère | **Vite + React + TS** | Next.js (React) | Django Templates + HTMX | Vue 3 + Vite | Angular |
|---|---|---|---|---|---|
| Richesse UI (tableaux, graphes) | **Maximale** | Maximale | Limitée pour les états complexes | Élevée | Élevée |
| Déploiement | **Fichiers statiques (Nginx)** | Serveur Node en plus | Intégré à Django | Fichiers statiques | Fichiers statiques |
| Courbe d'apprentissage | Modérée | Élevée (SSR, Server Components) | **Faible** | Modérée | Élevée (RxJS) |
| Écosystème de composants | **Le plus vaste** | Le plus vaste | Réduit | Bon | Bon |
| **Décision** | **Retenu** | Écarté (SSR inutile, complexité de déploiement) | Écarté | Écarté (écosystème plus petit) | Écarté (trop lourd pour un binôme) |

---

## 5. Données : PostgreSQL 16 + pgvector + modèle Silo

### 5.1 Choix du SGBD

PostgreSQL réunit dans **une seule base** les trois besoins du projet :

1. **Transactions ACID** : une campagne ou un score ne reste jamais dans un état incohérent.
2. **JSONB indexé** : règles d'automatisation, résultats de collecte, sorties de l'IA.
3. **pgvector** : stockage et recherche des vecteurs du RAG (§ 9), sans base vectorielle séparée.

| Critère | **PostgreSQL** | MySQL / MariaDB | MongoDB |
|---|---|---|---|
| ACID et relations N–N (contrôles ↔ preuves ↔ mapping) | **Excellent** | Très bon | Faible |
| JSON | **JSONB indexé** | Bon | Natif |
| Recherche vectorielle | **pgvector** | Limitée | Atlas (cloud) |
| Intégration Django | **Native** | Très bonne | ODM tiers |
| **Décision** | **Retenu** | Écarté | Écarté |

### 5.2 Stratégie multi-tenant : modèle Silo

**Décision : chaque organisation dispose de sa propre instance complète** (front, API, PostgreSQL, SeaweedFS, Redis, workers, IA). Aucune donnée n'est partagée entre deux organisations : l'isolation est **physique**, pas seulement logique.

```mermaid
flowchart TB
    IMG["Une seule image Docker<br/>(même code pour tous)"]
    IMG --> I1
    IMG --> I2
    IMG --> I3
    subgraph I1["Instance MNA"]
        direction TB
        A1["Mode service<br/>React + Django + workers"]
        A2[("PostgreSQL")]
        A3[("SeaweedFS")]
        A4["Clients audités :<br/>Banque X · Processeur Y"]
        A1 --- A2
        A1 --- A3
        A1 --- A4
    end
    subgraph I2["Instance Org. 1"]
        direction TB
        B1["SaaS hébergé par MNA<br/>React + Django + workers"]
        B2[("PostgreSQL")]
        B3[("SeaweedFS")]
        B1 --- B2
        B1 --- B3
    end
    subgraph I3["Instance Banque Z"]
        direction TB
        C1["On-premise chez la banque<br/>React + Django + workers"]
        C2[("PostgreSQL")]
        C3[("SeaweedFS")]
        C1 --- C2
        C1 --- C3
    end
```

| Modèle | Isolation | Coût d'exploitation | Décision |
|---|---|---|---|
| Pool : base partagée + `organization_id` | Logique : un filtre oublié = fuite | Faible | Écarté (risque de fuite inacceptable pour des données d'audit bancaire) |
| Bridge : un schéma par organisation (`django-tenants`) | Forte mais base commune | Migrations × N schémas | Écarté |
| **Silo** : une instance par organisation | **Physique** | Plus élevé (une pile par client) | **Retenu** |

**Pourquoi Silo ?**
- **Sécurité maximale** : une erreur de code ne peut pas exposer les données d'une autre organisation, puisqu'elles ne sont pas dans la même base.
- **Souveraineté** : une banque peut héberger son instance chez elle (on-premise, loi 18-07), sans changement de code.
- **Code plus simple** : pas de colonne `organization_id` ni de routage de schéma dans chaque requête.
- **Exploitation indépendante** : sauvegarde, restauration, mise à jour ou suppression d'un client sans toucher aux autres.

**Contrepartie :** chaque nouvelle organisation demande le déploiement d'une pile. Elle est automatisée par un script (`docker-compose.prod.yml` + `.env` propre au client + initialisation).

**À l'intérieur d'une instance** (cas de MNA qui audite plusieurs clients), la séparation entre entités auditées reste **applicative** : chaque utilisateur n'accède qu'aux campagnes et entités qui lui sont affectées (RBAC côté serveur, managers Django filtrés), vérifiée par des tests d'accès croisé (IDOR).

---

## 6. Traitements asynchrones : Celery + Redis

Analyse IA d'une preuve, collectes, génération de rapports : ces traitements prennent de quelques secondes à plusieurs minutes. Ils ne doivent pas bloquer une requête HTTP.

```mermaid
sequenceDiagram
    autonumber
    actor U as Utilisateur
    participant API as Django / DRF
    participant R as Redis
    participant W as Worker Celery
    U->>API: Action lourde (analyser, générer un rapport)
    API->>R: Dépose la tâche
    API-->>U: 202 Accepté (réponse immédiate)
    R->>W: Distribue la tâche
    W->>W: Traitement (IA, rapport…)
    W->>API: Résultat enregistré en base
    API-->>U: Notification (statut « À vérifier » / rapport prêt)
```

| Solution | Points forts | Limites | Décision |
|---|---|---|---|
| **Celery** | Standard Django, **Beat** (tâches planifiées), rejeux automatiques, files séparées par type de worker | Configuration plus dense | **Retenu** |
| Dramatiq / RQ | API simple | Pas de planification équivalente à Beat | Écarté |
| Django Tasks (`django.tasks`, Django 6.0) | Intégré à Django | Récent, pas de worker de production intégré | Écarté (à surveiller) |

**Courtier : Redis.** En mémoire, donc très rapide, et il sert **aussi de cache** Django : un seul service pour deux rôles. Redis est utilisé **tel quel, comme service séparé** sur le réseau interne, sans modification de son code. RabbitMQ est plus robuste en persistance mais plus lourd à exploiter, sans gain réel à notre échelle. Utiliser PostgreSQL comme courtier chargerait inutilement la base principale.

---

## 7. Stockage des preuves : SeaweedFS (API S3)

Les preuves (PDF, DOCX, exports CSV, configurations, captures) sont **hors de la base** : PostgreSQL ne garde que les métadonnées (nom, taille, **SHA-256**, entité auditée, contrôles liés).

| Critère | Disque local du serveur | **Stockage objet SeaweedFS (S3)** | BLOB en base |
|---|---|---|---|
| Évolutivité | Faible | **Excellente** | Médiocre |
| Liens de téléchargement temporaires | À coder | **URL présignées natives** | Via l'application |
| Accès par les workers IA | Moyen | **Direct** | Lourd |
| Impact sur la base | Aucun | **Aucun** | Critique (taille, sauvegardes) |
| **Décision** | Secours MVP (disque chiffré) | **Retenu** | Écarté |

- **SeaweedFS** est un stockage objet open source compatible S3, auto-hébergé : léger (écrit en Go), démarrable en un seul conteneur (`weed server -s3`), adapté au déploiement on-premise et au modèle Silo (**un SeaweedFS par instance**).
- **Licence Apache 2.0** : permissive, compatible avec la vente du produit sous licence (K6). MinIO, référence historique du domaine, a été écarté pour cette raison : il est sous **AGPL-3.0** et son édition communautaire est passée en mode maintenance fin 2025.
- **Le code ne parle qu'à l'API S3** (`django-storages` / `boto3`) : le stockage reste remplaçable par configuration, sans réécrire l'application.
- Les objets sont rangés **par entité auditée et par campagne** (`entite_<id>/campagne_<id>/…`) et ne sont jamais exposés directement : le back-end délivre une **URL présignée** à durée courte après contrôle des droits.
- Chiffrement des données au repos activé, bucket privé, accès réservé au réseau Docker interne.

---

## 8. Sécurité : défense en profondeur

```mermaid
flowchart LR
    C[Navigateur] -->|HTTPS TLS 1.2/1.3| N["① Nginx<br/>TLS, en-têtes de sécurité,<br/>taille max d'upload, rate limit"]
    N --> A["② Django<br/>connexion sans mot de passe (OTP e-mail),<br/>session HttpOnly, RBAC, CSRF"]
    A --> T["③ Isolation<br/>instance Silo + RBAC par entité"]
    A --> F["④ Fichiers<br/>type MIME réel, taille,<br/>ClamAV, SHA-256"]
    A --> S["⑤ Secrets<br/>.env en dev, OpenBao en prod"]
    A --> L["⑥ audit_log<br/>journal append-only"]
```

| Pilier | Choix | Justification |
|---|---|---|
| **Authentification** | **Connexion sans mot de passe** : l'utilisateur saisit son adresse e-mail et reçoit un **code à usage unique (OTP)** par e-mail ; puis session Django en **cookie `HttpOnly`, `Secure`, `SameSite=Lax`** | Aucun mot de passe à voler, réutiliser ou deviner ; le jeton de session n'est jamais accessible au JavaScript (contrairement à un JWT en `localStorage`, vulnérable au XSS) |
| **Autorisation** | RBAC côté serveur, par organisation et par campagne | Aucune décision de droit côté front |
| **Secrets** | `.env` validé au démarrage (dev) → **OpenBao** (prod) | Aucun secret dans Git ; OpenBao (MPL 2.0) plutôt que Vault (BSL) |
| **Fichiers** | Liste blanche d'extensions, **type MIME réel**, taille max, **ClamAV** en tâche Celery | Une preuve non analysée n'atteint jamais Docling ; rejet consigné dans `audit_log` |
| **Reverse proxy** | **Nginx** | Standard industriel, contrôle fin des en-têtes (CSP, HSTS, X-Frame-Options). Caddy (TLS automatique) reste une alternative pour un serveur de démo |
| **IA** | Conteneur LLM sur un **réseau Docker interne sans route Internet** ; extraits traités comme des **données**, jamais comme des instructions | Démontrable au jury ; limite l'injection de prompt indirecte |
| **Qualité du code (CI)** | ruff, mypy, pytest, ESLint, Vitest, Playwright, Semgrep, Bandit, pip-audit, Gitleaks, Trivy, OWASP ZAP | Vérification selon **OWASP ASVS 5.0 niveau 2** |

### Connexion sans mot de passe par code e-mail

```mermaid
sequenceDiagram
    autonumber
    actor U as Utilisateur
    participant F as SPA React
    participant API as Django / DRF
    participant M as Serveur SMTP
    U->>F: Saisit son adresse e-mail
    F->>API: POST /api/auth/otp/demande
    API->>API: Génère un code à 6 chiffres<br/>(stocké haché, valable 10 min)
    API->>M: Envoie le code par e-mail
    API-->>F: Réponse identique que le compte existe ou non
    U->>F: Saisit le code reçu
    F->>API: POST /api/auth/otp/verification
    API->>API: Vérifie code, expiration,<br/>nombre d'essais
    API-->>F: Cookie de session HttpOnly + journalisation audit_log
```

| Règle | Mise en œuvre |
|---|---|
| Code imprévisible | 6 chiffres générés par le module `secrets` (générateur cryptographique) |
| Durée de vie courte | Expiration après **10 minutes**, code **à usage unique** (invalidé dès sa vérification) |
| Stockage | Code **haché** en base, jamais en clair ; lié à la demande de connexion en cours |
| Anti-force brute | **5 essais** maximum par code, limitation du nombre de demandes par adresse et par IP (Django + Nginx) |
| Anti-énumération | Même réponse que l'adresse corresponde à un compte ou non |
| Traçabilité | Demandes, succès et échecs consignés dans `audit_log` |
| Sécurité de la session | Rotation de l'identifiant de session à la connexion, expiration après inactivité |

**Pourquoi ce choix ?** Il supprime toute la gestion des mots de passe (politique de complexité, réinitialisation, stockage haché, fuites et réutilisation entre sites) et la gêne d'une application TOTP à installer pour des contributeurs clients occasionnels. La sécurité repose alors sur la **boîte e-mail de l'utilisateur**, qui doit elle-même être protégée par MFA côté organisation. En on-premise sans Internet, l'envoi passe par le **relais SMTP interne** du client.

---

## 9. Intelligence artificielle locale : extraction, embeddings, RAG

**Objectif :** pour un contrôle donné (ex. PCI DSS 8.3.6), l'IA lit les preuves déposées et **propose** un statut (conforme / partiel / non conforme / insuffisant) avec **justification, citations et score de confiance**. L'auditeur valide toujours. La sortie est un JSON structuré, mesurable.

### 9.1 Les briques

| Étape | Outil | Ce qu'il fait | Pourquoi lui |
|---|---|---|---|
| Extraction | **Docling** (MIT, IBM Research) | Convertit PDF / DOCX / XLSX / PPTX / images en document structuré : titres, paragraphes, **tableaux**, **numéros de page** ; OCR pour les scans | Garde la structure, local, licence permissive. |
| Fichiers texte (configs, CSV) | Lecture directe (Python, `csv`) | Pas besoin de conversion | Plus simple et exact |
| Découpage | Chunker de Docling (`HybridChunker`) | Coupe le document en passages (*chunks*) en respectant titres et tableaux | Un passage = une idée, avec sa page |
| Embeddings | **BGE-M3** | Transforme chaque passage en **vecteur de 1024 nombres** qui représente son sens | Multilingue (FR / EN / AR), entrées longues (jusqu'à 8192 tokens) |
| Index vectoriel | **pgvector** (index HNSW) | Stocke les vecteurs et trouve les plus proches (similarité cosinus) | Dans PostgreSQL : mêmes droits et mêmes sauvegardes que les données |
| Génération | LLM local via **Ollama** (dev) / **vLLM** (prod GPU) | Lit les extraits et rend un JSON de décision | API compatible OpenAI : on change de runtime par configuration |
| Contrôle de sortie | Schéma JSON + vérification des citations | Rejette un JSON invalide ; vérifie que chaque citation existe vraiment dans le texte | Limite les hallucinations |

### 9.2 Runtime et modèle

| Runtime | Atout | Limite | Usage |
|---|---|---|---|
| **Ollama** | Installation triviale, catalogue de modèles | Débit faible en requêtes concurrentes | Développement, mode service |
| **vLLM** | Débit très supérieur sous charge (PagedAttention) | GPU NVIDIA obligatoire | Production SaaS avec GPU |

**Modèles candidats** : un premier benchmark (Qwen2.5 7B / 14B, 11 Mo de preuves synthétiques, 8 contrôles PCI DSS) montre que le **RAG est indispensable** (14B + RAG : 70 % d'exactitude, 100 % de citations valides ; en lecture complète, le 14B ne termine que 1 cas sur 10) et qu'un **GPU est obligatoire** (45 h pour 300 contrôles en CPU seul). La classe **~14 B** (Qwen2.5 / Qwen3-14B, Apache 2.0) sert de base et tient dans **24 Go de VRAM** avec un contexte de 16K ; la classe **~27 B** demande **32 Go**. Le serveur demandé à MNA en découle : 8–12 vCPU, 48–64 Go de RAM, 512 Go de SSD, GPU 24–32 Go. Le choix final se fera sur GPU et sur un jeu de preuves annotées (Lot 3).

---

## 10. Génération des rapports

| Format | Outil | Raison |
|---|---|---|
| **Word** (.docx) | **docxtpl** (Jinja2 dans un modèle Word) | MNA garde la main sur la charte : le modèle se modifie dans Word, sans code |
| **PDF** | **WeasyPrint** (HTML/CSS → PDF) | Rendu fidèle, licence BSD |
| **Excel** | **openpyxl** | Exports de matrices d'évaluation |
| CSV / JSON | Natif Python | Échanges et archivage |

Les rapports sont générés par un **worker Celery dédié** puis déposés dans le stockage S3.

---

## 11. Déploiement : Docker Compose

- **Un seul paquet Docker, une pile par organisation (Silo)** : instance MNA (mode service), instance par organisation licenciée (SaaS) hébergée par MNA, ou instance installée chez le client (on-premise).
- **Ajouter un client = lancer une nouvelle pile** avec son propre `.env`, sa base, son SeaweedFS et ses secrets, via un script d'initialisation.
- **Deux réseaux Docker** : `frontend` (Nginx, qui sert la SPA React et relaie vers l'API) et `backend` **interne** (`internal: true`) pour la base, Redis, le stockage et le LLM → le LLM n'a **aucune route vers Internet**.
- **Modèles IA pré-téléchargés** dans l'image ou un volume : fonctionnement hors ligne.
- Livrables : `docker-compose.prod.yml`, `.env.example`, script d'initialisation (organisation, admin, import des référentiels), sauvegarde / restauration (`pg_dump` + stockage objet).
- Kubernetes est écarté : inutile pour un serveur unique, et plus lourd à sécuriser.

---

## 12. Architecture globale de la plateforme

```mermaid
flowchart TB
    subgraph USERS["Utilisateurs"]
        direction LR
        U1["Auditeur / Resp. mission"]
        U2["Contributeur client"]
        U3["Direction / lecteur"]
    end

    NGINX["Nginx<br/>HTTPS · en-têtes de sécurité · limites d'upload"]
    USERS -->|HTTPS| NGINX
    U2 -.->|"résultat JSON signé<br/>(script de collecte)"| NGINX

    subgraph APP["Réseau Docker « frontend »"]
        direction LR
        FE["SPA React + TS (build Vite)<br/>fichiers statiques servis par Nginx<br/>tableaux de bord · matrices"]
        API["Django 5.2 + DRF<br/>campaigns · assessments · evidence<br/>findings · reporting · audit_log · ai"]
        FE -->|"appels /api"| API
    end
    NGINX -->|"/"| FE
    NGINX -->|"/api"| API

    subgraph BACK["Réseau interne — sans Internet"]
        direction TB
        RD[("Redis<br/>file de tâches + cache")]
        subgraph WORK["Workers Celery + Beat"]
            direction LR
            W1["Sécurité<br/>scan des preuves"]
            W2["IA<br/>Docling · chunks · BGE-M3 · RAG"]
            W3["Rapports<br/>docxtpl · WeasyPrint"]
            W4["Collectes<br/>moteur de règles"]
        end
        subgraph DATA["Données"]
            direction LR
            PG[("PostgreSQL 16<br/>données · JSONB · pgvector")]
            S3[("SeaweedFS (S3)<br/>preuves · rapports")]
            BAO["OpenBao<br/>secrets"]
        end
        AV["ClamAV"]
        LLM["Runtime IA local<br/>Ollama / vLLM<br/>LLM + BGE-M3"]
        RD --> WORK
        W1 --> AV
        W2 --> LLM
        WORK --> DATA
    end

    API --> RD
    API --> DATA
```

**Lecture du schéma :**
1. Tout le trafic entre par **Nginx** (HTTPS), qui sert directement les fichiers statiques de la SPA React et relaie `/api` vers Django : front et API partagent le même domaine.
2. L'**API Django** porte toute la logique métier et les contrôles de droits. Elle ne fait jamais de traitement lourd : elle dépose une tâche dans **Redis**.
3. Les **workers Celery** spécialisés (sécurité, IA, rapports, collectes) consomment les tâches. **Celery Beat** lance les tâches planifiées.
4. **PostgreSQL** garde les données et les vecteurs ; **SeaweedFS** garde les fichiers ; **OpenBao** garde les secrets.
5. Le **runtime IA** est sur le réseau interne : aucune donnée ne sort du serveur.
6. Ce schéma représente **une instance** : en modèle Silo, chaque organisation possède sa propre copie complète de cette pile.

---

## 13. Pipeline de préparation et d'analyse des preuves

C'est le cœur du Lot 3. Il comporte **deux phases distinctes** :
- **Indexation** : une seule fois par preuve déposée (réception → antivirus → Docling → chunks → embeddings → pgvector).
- **Analyse** : pour chaque contrôle à évaluer (recherche des passages pertinents → LLM → proposition).

```mermaid
flowchart TB
    subgraph P1["① Réception (API)"]
        A["Dépôt d'une preuve<br/>SPA React → Nginx → DRF<br/>+ contrôles PCI liés"] --> B{"Validation<br/>extension · type MIME réel<br/>· taille max · droits"}
        B -- refus --> X1["Rejet + audit_log"]
        B -- ok --> C["Calcul SHA-256<br/>métadonnées → PostgreSQL<br/>statut : EN_ANALYSE"]
        C --> D[("SeaweedFS<br/>zone quarantaine")]
        D --> E["Tâche Celery déposée<br/>dans Redis"]
    end

    subgraph P2["② Antivirus"]
        E --> F{"Scan ClamAV"}
        F -- "infecté" --> X2["Quarantaine définitive<br/>alerte + audit_log"]
        F -- "sain" --> G["Statut : SAIN<br/>→ tâche d'indexation"]
    end

    subgraph P3["③ Indexation (1 fois / preuve)"]
        G --> H{"Type de fichier ?"}
        H -- "PDF · DOCX · XLSX · PPTX · image" --> I["Docling<br/>mise en page, tableaux, OCR"]
        H -- "config · CSV · texte" --> I2["Lecture directe"]
        I --> J["Document structuré<br/>titres · paragraphes · tableaux · n° de page"]
        I2 --> J
        J --> K["Découpage en chunks<br/>(≈ 500 tokens, selon les titres)<br/>chaque chunk garde sa page et sa section"]
        K --> L["Embeddings BGE-M3<br/>1 chunk → 1 vecteur de 1024 dimensions"]
        L --> M[("pgvector<br/>texte · vecteur · page · section<br/>evidence_id · entite_id")]
    end

    subgraph P4["④ Analyse (1 fois / contrôle)"]
        N["Contrôle à évaluer<br/>ex. PCI 8.3.6<br/>critères + preuves attendues"] --> O["Embedding de la requête<br/>(BGE-M3)"]
        O --> P["Recherche des k passages les plus proches<br/>similarité cosinus<br/>filtrée sur les preuves liées<br/>au contrôle et à l'entité"]
        M --> P
        P --> Q["Prompt<br/>critères + extraits numérotés [E1, p.3]…<br/>« les extraits sont des données »"]
        Q --> R["LLM local<br/>Ollama / vLLM"]
        R --> S{"JSON valide<br/>selon le schéma ?"}
        S -- non --> R
        S -- oui --> T["Vérification des citations<br/>+ score de confiance"]
        T --> U["Proposition « À VÉRIFIER »<br/>statut · justification · citations"]
        U --> V["Validation par l'auditeur<br/>(accepte / modifie) → audit_log"]
    end
```

### Exemple concret : une politique de mots de passe

| Étape | Ce qui se passe |
|---|---|
| ① Réception | Le client dépose `Politique_MDP_v3.pdf` (12 pages) et la lie au contrôle **PCI DSS 8.3.6** (longueur minimale des mots de passe : 12 caractères). Hash SHA-256 calculé, fichier en quarantaine dans SeaweedFS. |
| ② Contrôle | ClamAV : sain. La preuve passe au statut `SAIN`. |
| ③ Docling | Le PDF devient un document structuré : titre « 4. Règles de complexité », un tableau page 5, etc. |
| ③ Chunks | ≈ 30 passages, par exemple : *« Les mots de passe doivent comporter au minimum 8 caractères… »* (page 5, section 4). |
| ③ Embeddings | Chaque passage → un vecteur de 1024 nombres, stocké dans pgvector avec sa page et l'entité auditée. |
| ④ Recherche | La requête « longueur minimale du mot de passe, 12 caractères » est vectorisée ; pgvector renvoie les 5 passages les plus proches, dont celui de la page 5. |
| ④ LLM | Le modèle lit les critères et les 5 extraits, puis répond : `{"statut_propose": "NON_CONFORME", "justification": "La politique impose 8 caractères, l'exigence en demande 12", "citations": ["E1"], "auto_confiance": 0.86}` |
| ④ Contrôles | Le JSON est valide ; la citation E1 existe bien dans le texte page 5 → score de confiance calculé. |
| ④ Validation | L'auditeur voit la proposition, l'extrait surligné et la page ; il valide. Le constat de non-conformité est créé et journalisé. |

---

## 14. Synthèse et point ouvert

La stack retenue repose sur un **monolithe modulaire Django** (sécurité et productivité), un **front React + TypeScript** riche construit avec Vite et servi en fichiers statiques, **PostgreSQL** comme socle unique (données et vecteurs), une **isolation physique par organisation (modèle Silo)** avec **SeaweedFS** pour les preuves, un **découplage asynchrone Celery / Redis** pour tout traitement lourd, une **connexion sans mot de passe** par code e-mail, et une **IA entièrement locale** (Docling → BGE-M3 → pgvector → LLM local). Toutes les briques sont open source, et l'ensemble se déploie avec Docker Compose, en ligne comme hors ligne.

| Point ouvert | Impact | Échéance |
|---|---|---|
| Relais SMTP disponible pour l'envoi des codes de connexion (SaaS et on-premise) | Sans SMTP, aucune connexion possible | Réunion MNA n°2 |

---

## Références

**Documentation officielle**

1. Django Software Foundation, *Django 5.2 documentation* et *Security in Django*. https://docs.djangoproject.com/
2. Encode, *Django REST Framework*. https://www.django-rest-framework.org/
3. Celery Project, *Celery 5 documentation*. https://docs.celeryq.dev/
4. Redis, *Redis documentation*. https://redis.io/docs/
5. *Vite documentation*. https://vite.dev/guide/ — Meta, *React documentation*. https://react.dev/ — Microsoft, *TypeScript Handbook*. https://www.typescriptlang.org/docs/
6. PostgreSQL Global Development Group, *Row Security Policies*. https://www.postgresql.org/docs/current/ddl-rowsecurity.html
7. pgvector, *Open-source vector similarity search for Postgres*. https://github.com/pgvector/pgvector
8. AWS, *SaaS Tenant Isolation Strategies* (livre blanc, modèles Silo / Pool / Bridge). https://docs.aws.amazon.com/whitepapers/latest/saas-tenant-isolation-strategies/
9. SeaweedFS, *SeaweedFS documentation* (wiki, API S3). https://github.com/seaweedfs/seaweedfs/wiki
10. Cisco Talos, *ClamAV Documentation*. https://docs.clamav.net/
11. OpenBao (Linux Foundation), documentation. https://openbao.org/docs/
12. NGINX / F5, documentation. https://nginx.org/en/docs/
13. Ollama, https://github.com/ollama/ollama — vLLM, https://docs.vllm.ai/ — llama.cpp, https://github.com/ggml-org/llama.cpp
14. docxtpl, https://docxtpl.readthedocs.io/ — WeasyPrint, https://doc.courtbouillon.org/weasyprint/

**Standards de sécurité**

15. OWASP, *Application Security Verification Standard 5.0*, 2025. https://owasp.org/www-project-application-security-verification-standard/
16. OWASP, *File Upload Cheat Sheet*. https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html
17. OWASP GenAI Security Project, *Top 10 for LLM Applications 2025*. https://genai.owasp.org/llm-top-10/

**Articles scientifiques**

18. P. Lewis *et al.*, « Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks », *NeurIPS 2020*. arXiv:2005.11401
19. C. Auer *et al.*, « Docling Technical Report », 2024. arXiv:2408.09869
20. J. Chen *et al.*, « BGE M3-Embedding: Multi-Lingual, Multi-Functionality, Multi-Granularity Text Embeddings Through Self-Knowledge Distillation », 2024. arXiv:2402.03216
21. W. Kwon *et al.*, « Efficient Memory Management for Large Language Model Serving with PagedAttention », *ACM SOSP 2023*. arXiv:2309.06180
22. Qwen Team, « Qwen3 Technical Report », 2025. arXiv:2505.09388
23. K. Greshake *et al.*, « Not what you've signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection », *ACM AISec 2023*. arXiv:2302.12173
24. C.-P. Bezemer, A. Zaidman, « Multi-tenant SaaS applications: maintenance dream or nightmare? », *IWPSE-EVOL 2010*. DOI 10.1145/1862372.1862393
