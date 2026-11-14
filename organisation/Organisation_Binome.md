# Organisation du binôme

| | |
|---|---|
| **Binôme** | **Rostom** et **Rahim** (USTHB) |
| **Encadrants MNA** | **Noor** et **Rabah** — contact par **e-mail direct ou téléphone** |
| **Présence chez MNA** | **Chaque lundi** |
| **Réunion d'avancement** | **Toutes les 2 semaines**, avec compte rendu ([Comptes_Rendus/](Comptes_Rendus/)) |

**Responsabilités déjà attribuées (réunion du 05/11/2026) :**
| Sujet | Responsable | Échéance |
|---|---|---|
| Estimation des ressources du VPS | **Rahim** | 16/11/2026 |
| Choix de la stack technique | **Rostom** | 16/11/2026 |
| Envoi des comptes rendus | Rostom (à alterner ensuite) | 48 h après chaque réunion |

> Les profils A / B ci-dessous sont à attribuer entre Rostom et Rahim selon vos préférences (le choix de stack par Rostom suggère A = Rostom, mais ce n'est pas obligatoire).

## 1. Répartition proposée (par spécialité, avec recouvrement)

| Domaine | Membre A — « Back-end, sécurité, automatisation » | Membre B — « Front-end, IA, reporting » |
|---|---|---|
| Étude de l'existant | Technologies (stack, SaaS multi-tenant, IA locale), outils techniques (scanners) | Outils GRC (CISO Assistant…), processus d'audit MNA, état de l'art scientifique |
| Analyse | Cas d'utilisation, exigences non fonctionnelles et sécurité, licences/organisations | User stories, maquettes |
| Conception | Architecture, modèle de données, threat model, moteur de règles | UX/UI, pipeline IA, modèles de rapport |
| Lot 1 | Auth/MFA, RBAC, multi-tenant, référentiels, campagnes, journal d'audit, API | Toutes les interfaces, dashboard KPI, génération de rapports |
| Lot 2 (allégé) | Commandes fixes AD/Linux, moteur de règles, lab | Écrans de collecte (observé/attendu, validation en lot) |
| Lot 3 | Sécurité du service IA (isolation réseau, injection de prompt) | Extraction, RAG, prompts, score de confiance, jeu d'évaluation, benchmark |
| Lot 4 | Moteur de calcul de couverture + tests | Administration du mapping, visualisation de la couverture |
| Mapping expert | Domaines techniques (1, 2, 4, 5, 6, 8, 10, 11) | Domaines organisationnels (3, 7, 9, 12) |
| Qualité | CI/CD, SAST, pentest, rapport de sécurité | Tests E2E, documentation utilisateur, recette |
| Mémoire | Chapitres conception technique, sécurité, automatisation | État de l'art, IA, résultats, interfaces |

> ⚠️ **Règle d'or :** chacun doit pouvoir **expliquer tout le projet** devant le jury. D'où : revue de code croisée obligatoire (chaque PR relue par l'autre), et chacun fait au moins une tâche dans le domaine de l'autre par sprint.

## 2. Outils
| Besoin | Outil |
|---|---|
| Code | Git + GitHub/GitLab privé, branches `feature/US-xxx`, PR avec revue |
| Tâches | GitHub Projects / Trello / Notion (tableau Kanban : Backlog → À faire → En cours → Revue → Fini) |
| Documents | Ce dossier `.md` dans le dépôt (docs-as-code) + Word/LaTeX (Overleaf) pour le mémoire |
| Diagrammes | Mermaid (dans les .md), PlantUML, draw.io |
| Maquettes | Figma ou Penpot |
| Bibliographie | Zotero (groupe partagé) — **articles et sources officielles, pas Wikipédia** (consigne MNA) ; voir [Bibliographie.md](../connaissances/Bibliographie.md) |
| Communication | WhatsApp/Discord pour le binôme ; **e-mail direct ou téléphone** avec Noor et Rabah (MNA) |
| IA | Claude Code dans ce dossier (voir [02_Plan_Roadmap.md](../02_Plan_Roadmap.md) § 8) |

## 3. Rituels
| Quand | Quoi | Durée |
|---|---|---|
| Chaque matin | Point binôme (hier / aujourd'hui / blocages) | 10 min |
| Chaque vendredi | Journal de bord + mise à jour du Kanban | 30 min |
| Chaque lundi | Présence chez MNA : questions, validations, relances (référentiel GIE, mapping) | journée |
| Toutes les 2 semaines | **Réunion d'avancement** avec Noor et Rabah (démo + état d'avancement) + **compte rendu** envoyé par e-mail | 1 h 30 |
| Chaque mois | Point avec l'encadrant académique | 1 h |

## 4. Conventions
- **Commits** : `feat(campaigns): add status state machine (US-402)` (Conventional Commits).
- **Definition of Done** d'une user story : code relu, tests passés en CI, critères d'acceptation vérifiés, journalisation des actions sensibles, documentation mise à jour, démo possible.
- **Nommage** : code en anglais, interface et documentation en français.
- **Données** : **aucune donnée réelle de client** sur vos machines personnelles ni dans Git (seulement des données fictives) sauf accord explicite de MNA.
- **Propriété / partage (réunion 05/11, D7)** : ne mettre que des **informations non sensibles** dans le dépôt et le mémoire (pas de texte du référentiel GIE, pas de document MNA interne) ; la propriété du code et le partage avec l'USTHB seront rediscutés.

## 5. Gestion des désaccords
1. On en parle le jour même au point quotidien.
2. Décision technique : on écrit un mini-ADR avec les options → on choisit selon les critères (sécurité > simplicité > performance).
3. Si blocage : arbitrage par l'encadrant MNA.
