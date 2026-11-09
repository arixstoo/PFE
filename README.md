# PFE — Plateforme d'automatisation des contrôles de conformité PCI DSS & GIE Monétique

> **Intitulé officiel :** *Conception et implémentation d'une plateforme d'automatisation des contrôles de conformité : application aux référentiels PCI DSS et GIE Monétique*
> **Organisme d'accueil :** Mare Nostrum Advising Group (MNA Algérie)
> **Type :** Projet de Fin d'Études — Ingéniorat en cybersécurité — en binôme (**Rostom** et **Rahim**, USTHB)
> **Encadrants MNA :** Noor et Rabah · **Stage :** du 09/11/2026 à début juin 2027 (fin à confirmer) · présence chez MNA le **lundi** · réunion d'avancement **tous les 15 jours**
> **Document source :** [PCI_DSS_PFE_CDC_V12102026.docx](PCI_DSS_PFE_CDC_V12102026.docx) (cahier des charges, version du 12/10/2026)
> **Nom de travail de la plateforme (vu dans la Figure 2 du CDC) :** *PCI Conformity Manager*

---

## 🔔 Dernières nouvelles (09/11/2026)

- Réunion de lancement du **05/11/2026** → [compte rendu](organisation/Comptes_Rendus/CR_2026-11-05_Reunion_lancement.md). Le CDC est un **brouillon flexible**.
- **URGENT avant le 16/11/2026 :** estimation du VPS (**Rahim**) → [Estimation_Ressources_VPS.md](conception/Estimation_Ressources_VPS.md) · choix de la stack (**Rostom**) → [Choix_Technologiques.md](conception/Choix_Technologiques.md).
- **Deux modes** : A. service (MNA audite ses clients) · B. **SaaS sous licence** (audits internes d'entreprises).
- **Références obligatoires** (articles, pas Wikipédia) → [Bibliographie.md](connaissances/Bibliographie.md).
- **Étude de l'existant** = grande partie du travail, avant janvier → [Etat_de_l_Art.md](connaissances/Etat_de_l_Art.md).

## Par où commencer ? (ordre de lecture conseillé)

| # | Fichier | Pourquoi le lire | Temps |
|---|---------|------------------|-------|
| 1 | [01_Sujet_Detaille.md](01_Sujet_Detaille.md) | Comprendre **tout** le sujet, section par section, avec explications et interprétation | 1 h |
| 2 | [02_Plan_Roadmap.md](02_Plan_Roadmap.md) | Le plan complet étape par étape, durées, livrables, et **ce que Claude peut / ne peut pas faire** | 1 h |
| 3 | [organisation/Comptes_Rendus/](organisation/Comptes_Rendus/) + [Questions_Encadrant.md](organisation/Questions_Encadrant.md) | Ce que MNA a décidé, et les questions encore ouvertes | 15 min |
| 4 | [connaissances/Glossaire.md](connaissances/Glossaire.md) | Tous les termes (PAN, CDE, QSA, ROC, SAQ, RBAC, RAG…) | à garder ouvert |
| 5 | [connaissances/PCI_DSS_Guide.md](connaissances/PCI_DSS_Guide.md) | Maîtriser PCI DSS v4.0.1 | 2–3 h |
| 6 | [connaissances/GIE_Monetique_et_Contexte_Algerien.md](connaissances/GIE_Monetique_et_Contexte_Algerien.md) | Écosystème monétique algérien + cadre légal | 45 min |
| 7 | [connaissances/Mapping_Croise.md](connaissances/Mapping_Croise.md) | Le cœur « intelligent » du projet : le moteur de mapping | 1 h |
| 8 | [connaissances/Methodes_Evaluation_et_IA_Locale.md](connaissances/Methodes_Evaluation_et_IA_Locale.md) | Les 4 méthodes : agents/scripts, IA locale, manuel, N/A | 1 h 30 |
| 9 | [connaissances/Etat_de_l_Art.md](connaissances/Etat_de_l_Art.md) | **Étude de l'existant** (processus MNA, outils, technologies, recherche) — chapitre 1 du mémoire | 1 h |
| 9b | [connaissances/Bibliographie.md](connaissances/Bibliographie.md) | Toutes les références vérifiées (articles, normes, docs officielles) | 30 min |
| 10 | [conception/Architecture_Technique.md](conception/Architecture_Technique.md) | Composants, deux modes de déploiement (service / SaaS) | 1 h |
| 10b | [conception/Choix_Technologiques.md](conception/Choix_Technologiques.md) | Comparatif pondéré des technologies + décision (Rostom) | 45 min |
| 10c | [conception/Estimation_Ressources_VPS.md](conception/Estimation_Ressources_VPS.md) | Dimensionnement du serveur (CPU/GPU/RAM/stockage) (Rahim) | 20 min |
| 11 | [conception/Modele_de_Donnees.md](conception/Modele_de_Donnees.md) | Entités, relations, versioning, multi-tenant | 1 h |
| 12 | [conception/Securite_Plateforme.md](conception/Securite_Plateforme.md) | Sécurité by design de la plateforme elle-même + threat model | 1 h |
| 13 | [conception/Backlog_User_Stories.md](conception/Backlog_User_Stories.md) | Backlog priorisé (MoSCoW) prêt pour les sprints | 45 min |
| 14 | [organisation/Organisation_Binome.md](organisation/Organisation_Binome.md) | Répartition du travail entre vous deux, outils, rituels | 20 min |
| 15 | [organisation/Plan_Memoire.md](organisation/Plan_Memoire.md) | Structure du mémoire et de la soutenance | 20 min |
| 16 | [organisation/Journal_de_Bord.md](organisation/Journal_de_Bord.md) | Modèle de suivi hebdomadaire à remplir | — |

## Arborescence

```
PFE/
├── README.md                         ← vous êtes ici
├── PCI_DSS_PFE_CDC_V12102026.docx    ← cahier des charges original
├── 01_Sujet_Detaille.md
├── 02_Plan_Roadmap.md
├── connaissances/
│   ├── Glossaire.md
│   ├── PCI_DSS_Guide.md
│   ├── GIE_Monetique_et_Contexte_Algerien.md
│   ├── Mapping_Croise.md
│   ├── Methodes_Evaluation_et_IA_Locale.md
│   ├── Etat_de_l_Art.md              ← étude de l'existant
│   └── Bibliographie.md
├── conception/
│   ├── Architecture_Technique.md
│   ├── Choix_Technologiques.md
│   ├── Estimation_Ressources_VPS.md
│   ├── Modele_de_Donnees.md
│   ├── Securite_Plateforme.md
│   └── Backlog_User_Stories.md
└── organisation/
    ├── Comptes_Rendus/               ← un CR par réunion
    │   ├── Modele_CR.md
    │   └── CR_2026-11-05_Reunion_lancement.md
    ├── Questions_Encadrant.md
    ├── Organisation_Binome.md
    ├── Plan_Memoire.md
    └── Journal_de_Bord.md
```

## Le projet en 5 phrases

1. Aujourd'hui, les audits PCI DSS et GIE Monétique sont faits **à la main** (Excel, e-mails, Word) : c'est long, incohérent entre auditeurs et peu traçable.
2. On doit construire une **plateforme web sécurisée multi-clients** où un cabinet (MNA) gère ses clients, lance des **campagnes d'évaluation** et suit les résultats dans le temps — et qui pourra aussi être **vendue en SaaS sous licence** à des entreprises pour leurs audits internes.
3. Chaque contrôle est évalué selon **4 méthodes** : **commandes de collecte bien définies**, **preuve déposée analysée par une IA locale** (statut + justification, pas de chatbot), **saisie manuelle** de l'auditeur, ou **N/A justifié**.
4. Un **moteur de mapping** projette les résultats d'un référentiel vers l'autre (PCI DSS v4.0.1 ↔ GIE, ce dernier étant basé sur PCI DSS 3.2) pour estimer la couverture et éviter de refaire les mêmes vérifications.
5. La plateforme calcule les **KPI**, garde l'**historique**, suit les **remédiations** et **génère le rapport** final (PDF / Word / Excel).

## Légende utilisée dans le plan

| Icône | Signification |
|-------|---------------|
| 🤖 | Claude peut le faire en grande partie (code, documents, schémas…) — vous relisez et validez |
| 🤝 | Claude aide partiellement (brouillon, explication, revue) — l'essentiel vient de vous ou de MNA |
| 🙋 | Claude ne peut pas le faire — action humaine obligatoire (accès, réunion, validation métier, matériel…) |

> ⚠️ Points encore à confirmer avec MNA : date de fin de stage et de soutenance, réception du référentiel GIE, acquisition du VPS (GPU ?), antivirus, format des maquettes, niveau du rapport de sécurité, modèle de licence SaaS. Voir [Questions_Encadrant.md](organisation/Questions_Encadrant.md).
