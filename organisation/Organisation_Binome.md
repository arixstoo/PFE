# Organisation du binôme

## 1. Répartition proposée (par spécialité, avec recouvrement)

| Domaine | Membre A — « Back-end, sécurité, automatisation » | Membre B — « Front-end, IA, reporting » |
|---|---|---|
| Analyse | Cas d'utilisation, exigences non fonctionnelles et sécurité | État de l'art, user stories, maquettes |
| Conception | Architecture, modèle de données, threat model, moteur de règles | UX/UI, pipeline IA, modèles de rapport |
| Lot 1 | Auth/MFA, RBAC, multi-tenant, référentiels, campagnes, journal d'audit, API | Toutes les interfaces, dashboard KPI, génération de rapports |
| Lot 2 | Collecteurs AD/Windows/Linux, moteur de règles, coffre de secrets, lab | Écrans de collecte (observé/attendu, validation en lot) |
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
| Bibliographie | Zotero (groupe partagé) |
| Communication | WhatsApp/Discord pour le binôme, e-mail pour MNA |
| IA | Claude Code dans ce dossier (voir [02_Plan_Roadmap.md](../02_Plan_Roadmap.md) § 8) |

## 3. Rituels
| Quand | Quoi | Durée |
|---|---|---|
| Chaque matin | Point binôme (hier / aujourd'hui / blocages) | 10 min |
| Chaque vendredi | Journal de bord + mise à jour du Kanban | 30 min |
| Fin de sprint (toutes les 2 semaines) | Démo à l'encadrant MNA + rétrospective + planification | 1 h 30 |
| Chaque mois | Point avec l'encadrant académique | 1 h |

## 4. Conventions
- **Commits** : `feat(campaigns): add status state machine (US-402)` (Conventional Commits).
- **Definition of Done** d'une user story : code relu, tests passés en CI, critères d'acceptation vérifiés, journalisation des actions sensibles, documentation mise à jour, démo possible.
- **Nommage** : code en anglais, interface et documentation en français.
- **Données** : **aucune donnée réelle de client** sur vos machines personnelles ni dans Git (seulement des données fictives) sauf accord explicite de MNA.

## 5. Gestion des désaccords
1. On en parle le jour même au point quotidien.
2. Décision technique : on écrit un mini-ADR avec les options → on choisit selon les critères (sécurité > simplicité > performance).
3. Si blocage : arbitrage par l'encadrant MNA.
