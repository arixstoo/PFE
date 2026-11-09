# Choix technologiques — étude comparative

> **Responsable : Rostom** · **Échéance : 16/11/2026** (réunion du 05/11, décision D5 : stack laissée au binôme).
> Statut : 🟡 **brouillon de travail** — les recommandations ci-dessous sont des propositions argumentées ; la décision finale est notée au § 12 et reportée dans [Architecture_Technique.md](Architecture_Technique.md).
> Ce document est aussi la base de la partie « étude de l'existant — technologies » du mémoire (consigne MNA C3). Références : [Bibliographie.md](../connaissances/Bibliographie.md).

---

## 1. Contraintes qui pilotent le choix

| # | Contrainte | Source |
|---|---|---|
| K1 | **Deux modes de déploiement** : (A) *service* — MNA audite ses clients ; (B) *SaaS sous licence* — des entreprises font leurs audits internes | Réunion 05/11, D11 |
| K2 | **On-premise / environnement privé possible**, fonctionnement **sans Internet** (IA locale, pas de CDN) | CDC § 14–15 |
| K3 | **IA locale** : aucune preuve envoyée à une API publique | CDC § 10.2, § 14 ; loi 18-07 [S12] |
| K4 | **Multi-tenant strict** : aucune fuite entre clients — et, en SaaS, entre **organisations licenciées** | CDC § 14 ; [A14], [D2] |
| K5 | **Référentiels = données** (rien de PCI/GIE dans le code) | CDC § 6 |
| K6 | **Sécurité by design**, bonnes pratiques | Réunion 05/11, D12 ; [S10] |
| K7 | **Produit vendable** → licences des dépendances compatibles avec une distribution commerciale (éviter l'AGPL dans le cœur, sauf accord MNA) | D11 + D7 |
| K8 | **Binôme, ~7 mois** → technologies maîtrisables rapidement, documentation abondante | Planning |

## 2. Grille de décision (pondérée)

| Critère | Poids |
|---|---|
| Sécurité (fonctions natives, historique de vulnérabilités, maturité) | 25 % |
| Adéquation au besoin (IA, multi-tenant, rapports, collecteurs) | 20 % |
| Productivité / courbe d'apprentissage pour le binôme | 20 % |
| Déploiement on-prem hors ligne + SaaS | 15 % |
| Licence compatible produit commercial | 10 % |
| Écosystème, communauté, pérennité | 10 % |

Notes de 1 (mauvais) à 5 (excellent). Score = Σ (note × poids). **À ajuster par Rostom selon vos compétences réelles** (le critère « productivité » dépend de vous !).

---

## 3. Back-end

| Option | Sécu. | Adéq. | Prod. | Déploi. | Lic. | Éco. | **Score** | Commentaire |
|---|---|---|---|---|---|---|---|---|
| **Python + Django 5 + DRF** | 5 | 5 | 5 | 4 | 5 | 5 | **4,85** | Protections natives CSRF/XSS/SQLi/clickjacking [D4], ORM + migrations, admin intégré, auth/permissions ; Python = même langage que l'IA, l'extraction, les rapports ; stack de CISO Assistant [D8] |
| Python + FastAPI | 4 | 5 | 4 | 4 | 5 | 4 | 4,30 | Rapide, async natif, OpenAPI auto ; mais auth, admin, permissions, migrations à assembler soi-même → plus de code de sécurité à écrire |
| Java + Spring Boot | 5 | 3 | 3 | 4 | 5 | 5 | 4,05 | Très robuste en entreprise/banque ; mais IA et parsing documentaire moins naturels, plus verbeux |
| Node.js + NestJS | 4 | 3 | 4 | 4 | 5 | 4 | 3,90 | Un seul langage avec le front ; écosystème IA/document moins riche qu'en Python |
| .NET 8 (ASP.NET Core) | 5 | 3 | 3 | 4 | 5 | 4 | 3,95 | Bon pour l'intégration AD ; moins adapté au pipeline IA |

**Recommandation : Django + DRF.** Variante : un **micro-service IA en FastAPI** isolé (voir § 9) si l'on veut séparer le service IA.

## 4. Front-end

| Option | Score indicatif | Commentaire |
|---|---|---|
| **React + TypeScript + Vite** (+ shadcn/ui ou MUI, TanStack Query, Recharts) | **4,6** | Le plus répandu, beaucoup de composants (tableaux, graphiques), facile à recruter pour MNA ensuite |
| Vue 3 + TypeScript | 4,3 | Plus simple à apprendre ; écosystème un peu plus petit |
| Angular | 4,0 | Cadre complet, apprécié en entreprise ; plus lourd à apprendre |
| SvelteKit | 3,9 | Léger (utilisé par CISO Assistant) ; communauté plus réduite |
| Templates Django + HTMX | 3,8 | Très simple et sûr (pas d'API publique à sécuriser) ; moins riche pour les dashboards |

**Recommandation : React + TypeScript**, sauf si l'un de vous maîtrise déjà Vue/Angular (le critère productivité l'emporte alors).

## 5. Base de données et isolation multi-tenant

| Option | Commentaire |
|---|---|
| **PostgreSQL 16/17 + pgvector** | JSONB (règles, résultats de collecte), **Row-Level Security** [D1], recherche vectorielle dans la même base (RAG) → **recommandé** |
| MySQL / MariaDB | Pas de RLS natif, pas d'équivalent pgvector aussi mature |
| MongoDB | Schéma flexible mais relations N–N (mapping, preuves ↔ contrôles) et transactions moins naturelles |

**Stratégie d'isolation (mode SaaS, K1/K4)** — d'après [D2], [D3], [A14] :

| Modèle | Principe | + | − | Usage proposé |
|---|---|---|---|---|
| **Pool** (base partagée + `tenant_id` + RLS) | Toutes les organisations dans la même base, filtrage applicatif **et** RLS | Simple, économique, une seule migration | Une erreur de filtrage = fuite → tests IDOR obligatoires | **Par défaut** (MVP, SaaS mutualisé) |
| **Bridge** (un schéma PostgreSQL par organisation, ex. `django-tenants`) | Même base, schémas séparés | Isolation plus forte, export/suppression d'un tenant facile | Migrations × N schémas | Option si MNA l'exige |
| **Silo** (une instance/base par organisation) | Déploiement dédié | Isolation maximale, adapté aux banques exigeantes | Coût et exploitation | Mode A on-prem et clients SaaS « premium » |

→ **Concevoir le code pour le modèle Pool**, mais **packager** la plateforme (Docker Compose) pour pouvoir aussi la livrer en **Silo**. Les deux modes de la réunion sont ainsi couverts sans deux bases de code.

## 6. Tâches asynchrones

| Option | Commentaire |
|---|---|
| **Celery + Redis (ou Valkey)** | Standard Django, planification (beat), retries → **recommandé** |
| Dramatiq / RQ | Plus simples, moins de fonctions |
| Django Tasks (intégré aux versions récentes de Django) | À surveiller ; moins mature pour des pipelines IA longs |

## 7. Stockage des preuves (objet)

⚠️ **Changement depuis la première version de l'architecture :** MinIO Community Edition est passé en **mode maintenance** fin 2025 (plus de binaires officiels, correctifs de sécurité seulement) [G2]. Pour un produit vendu, ce n'est plus un bon choix par défaut.

| Option | Licence | Commentaire |
|---|---|---|
| **Abstraction S3 (`django-storages` / boto3)** + **SeaweedFS** | Apache 2.0 | API S3, production-ready → **recommandé** |
| Garage | AGPL-3.0 | Léger ; AGPL et fonctions limitées (pas de versioning) |
| Système de fichiers chiffré (LUKS) + chiffrement applicatif | — | Le plus simple pour le MVP et le mode on-prem mono-serveur |
| Stockage S3 du fournisseur du VPS | — | Possible en SaaS, à éviter si données hors Algérie |

→ Le code ne parle **qu'à l'API S3** : on peut changer de stockage sans réécriture. MVP : système de fichiers chiffré **ou** SeaweedFS.

## 8. Sécurité : auth, secrets, antivirus, proxy

| Besoin | Recommandation | Alternatives / remarques |
|---|---|---|
| Authentification + MFA | Django auth + `django-otp` (TOTP) + **argon2id** | **Keycloak** (OIDC, MFA, SSO) : très utile en **mode SaaS** (chaque organisation peut brancher son annuaire) → à prévoir en Should |
| Secrets | **OpenBao** (MPL 2.0) [D6] | HashiCorp Vault est sous licence **BSL** depuis 2023 → problème pour un produit vendu |
| Antivirus | **ClamAV** (clamd) [D5] | ⏳ En attente de la réponse MNA (question n°5 de la réunion 2) : antivirus fourni par MNA ? |
| Reverse proxy | **Nginx** (TLS 1.2/1.3, en-têtes, limites d'upload) | Caddy (TLS auto), Traefik |

## 9. IA locale

### 9.1 Runtime
| Option | Atout | Limite | Usage |
|---|---|---|---|
| **Ollama** | Installation triviale, catalogue de modèles, API HTTP | Débit faible sous charge concurrente (41 vs 793 tokens/s pour vLLM dans [G1]) | **Développement**, mode A mono-cabinet |
| **vLLM** | PagedAttention, débit ×2–4 [A8], API compatible OpenAI | Nécessite un GPU NVIDIA, plus de VRAM pré-allouée | **Production SaaS** avec GPU |
| **llama.cpp (server)** | Très léger, CPU ou GPU, format GGUF quantifié | Moins de fonctions de service | Serveur **sans GPU** |

→ Le code appelle une **API compatible OpenAI** en local : on change de runtime par configuration.

### 9.2 Modèles candidats (à départager par **notre propre benchmark**, tâche du Lot 3)
| Taille | Candidats | Licence | Remarque |
|---|---|---|---|
| 4–8 B | Qwen3-4B / 8B [A9], Llama 3.1 8B, Mistral 7B | Apache 2.0 / Llama | Faisable sur CPU (lent) |
| 14 B | Qwen3-14B | Apache 2.0 | Bon compromis sur GPU 16 Go |
| 24–32 B | Mistral Small 24B, Qwen3-32B, Qwen3-30B-A3B (MoE) | Apache 2.0 | Meilleure qualité en français, GPU 24 Go |

Sortie attendue : **statut (conforme / partiel / non conforme) + justification + citations + confiance**, en **JSON structuré** — **pas de chatbot** (réunion 05/11, D10).

### 9.3 Embeddings et extraction
| Besoin | Recommandation | Remarque |
|---|---|---|
| Embeddings | **BGE-M3** [A10] (multilingue FR/EN/AR) | Alternative : multilingual-e5 |
| Extraction PDF/DOCX | **Docling** (MIT) [A11] | ⚠️ **PyMuPDF est AGPL** [D9] → à éviter dans un produit vendu ; pypdf / pdfplumber (licences permissives) en secours |
| Office | python-docx, openpyxl | — |
| OCR (bonus) | Tesseract | — |

## 10. Rapports

| Format | Recommandation |
|---|---|
| PDF | **WeasyPrint** (HTML/CSS → PDF, BSD) |
| Word | **docxtpl** (modèle Word MNA éditable sans code) |
| Excel | **openpyxl** |
| CSV / JSON | natif |

## 11. Qualité, CI, sécurité du code (sécurité by design)

ruff + mypy, pytest, ESLint + Vitest, Playwright (E2E), **Semgrep + Bandit** (SAST), **pip-audit / npm audit** (dépendances), **Gitleaks** (secrets), **Trivy** (images), **OWASP ZAP** (DAST). Vérification **OWASP ASVS 5.0 niveau 2** [S10] comme référentiel du rapport de sécurité.

---

## 12. Décision (à remplir par Rostom avant le 16/11)

| Couche | Recommandation | Décision finale | Justification courte |
|---|---|---|---|
| Back-end | Django 5 + DRF (Python 3.12+) | | |
| Front-end | React + TypeScript + Vite | | |
| Base de données | PostgreSQL + pgvector + RLS (modèle Pool) | | |
| Asynchrone | Celery + Redis/Valkey | | |
| Stockage preuves | API S3 → SeaweedFS (ou FS chiffré au MVP) | | |
| Auth / MFA | Django + django-otp (Keycloak en Should) | | |
| Secrets | OpenBao | | |
| Antivirus | ClamAV (sous réserve MNA) | | |
| IA runtime | Ollama (dev) / vLLM (prod GPU) / llama.cpp (CPU) | | |
| Modèle LLM | Benchmark Qwen3-8B/14B vs Mistral Small 24B | | |
| Embeddings | BGE-M3 | | |
| Extraction | Docling | | |
| Rapports | WeasyPrint + docxtpl + openpyxl | | |
| Déploiement | Docker Compose (on-prem / Silo) ; même images en SaaS | | |

> Chaque ligne importante → un **ADR** d'une page (contexte, options, décision, conséquences), voir [Architecture_Technique.md](Architecture_Technique.md) § 8.
