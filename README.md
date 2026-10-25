# PFE — Plateforme d'automatisation des contrôles de conformité PCI DSS & GIE Monétique

> **Intitulé officiel :** *Conception et implémentation d'une plateforme d'automatisation des contrôles de conformité : application aux référentiels PCI DSS et GIE Monétique*
> **Organisme d'accueil :** Mare Nostrum Advising Group (MNA Algérie)
> **Type :** Projet de Fin d'Études — Ingéniorat en cybersécurité — en binôme
> **Document source :** [PCI_DSS_PFE_CDC_V12102026.docx](PCI_DSS_PFE_CDC_V12102026.docx) (cahier des charges, version du 12/10/2026)
> **Nom de travail de la plateforme (vu dans la Figure 2 du CDC) :** *PCI Conformity Manager*

---

## Par où commencer ? (ordre de lecture conseillé)

| # | Fichier | Pourquoi le lire | Temps |
|---|---------|------------------|-------|
| 1 | [01_Sujet_Detaille.md](01_Sujet_Detaille.md) | Comprendre **tout** le sujet, section par section, avec explications et interprétation | 1 h |
| 2 | [02_Plan_Roadmap.md](02_Plan_Roadmap.md) | Le plan complet étape par étape, durées, livrables, et **ce que Claude peut / ne peut pas faire** | 1 h |
| 3 | [organisation/Questions_Encadrant.md](organisation/Questions_Encadrant.md) | Les questions à poser **dès la première semaine** à MNA (bloquant !) | 15 min |
| 4 | [connaissances/Glossaire.md](connaissances/Glossaire.md) | Tous les termes (PAN, CDE, QSA, ROC, SAQ, RBAC, RAG…) | à garder ouvert |
| 5 | [connaissances/PCI_DSS_Guide.md](connaissances/PCI_DSS_Guide.md) | Maîtriser PCI DSS v4.0.1 | 2–3 h |
| 6 | [connaissances/GIE_Monetique_et_Contexte_Algerien.md](connaissances/GIE_Monetique_et_Contexte_Algerien.md) | Écosystème monétique algérien + cadre légal | 45 min |
| 7 | [connaissances/Mapping_Croise.md](connaissances/Mapping_Croise.md) | Le cœur « intelligent » du projet : le moteur de mapping | 1 h |
| 8 | [connaissances/Methodes_Evaluation_et_IA_Locale.md](connaissances/Methodes_Evaluation_et_IA_Locale.md) | Les 4 méthodes : agents/scripts, IA locale, manuel, N/A | 1 h 30 |
| 9 | [connaissances/Etat_de_l_Art.md](connaissances/Etat_de_l_Art.md) | Outils existants (CISO Assistant, Eramba, OSCAL…) — chapitre 1 du mémoire | 1 h |
| 10 | [conception/Architecture_Technique.md](conception/Architecture_Technique.md) | Stack proposée, composants, déploiement | 1 h |
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
│   └── Etat_de_l_Art.md
├── conception/
│   ├── Architecture_Technique.md
│   ├── Modele_de_Donnees.md
│   ├── Securite_Plateforme.md
│   └── Backlog_User_Stories.md
└── organisation/
    ├── Questions_Encadrant.md
    ├── Organisation_Binome.md
    ├── Plan_Memoire.md
    └── Journal_de_Bord.md
```

## Le projet en 5 phrases

1. Aujourd'hui, les audits PCI DSS et GIE Monétique sont faits **à la main** (Excel, e-mails, Word) : c'est long, incohérent entre auditeurs et peu traçable.
2. On doit construire une **plateforme web sécurisée multi-clients** où un cabinet (MNA) gère ses clients, lance des **campagnes d'évaluation** et suit les résultats dans le temps.
3. Chaque contrôle est évalué selon **4 méthodes** : **script/agent automatique**, **preuve déposée analysée par une IA locale**, **saisie manuelle** de l'auditeur, ou **N/A justifié**.
4. Un **moteur de mapping** projette les résultats d'un référentiel vers l'autre (PCI DSS ↔ GIE) pour estimer la couverture et éviter de refaire les mêmes vérifications.
5. La plateforme calcule les **KPI**, garde l'**historique**, suit les **remédiations** et **génère le rapport** final (PDF / Word / Excel).

## Légende utilisée dans le plan

| Icône | Signification |
|-------|---------------|
| 🤖 | Claude peut le faire en grande partie (code, documents, schémas…) — vous relisez et validez |
| 🤝 | Claude aide partiellement (brouillon, explication, revue) — l'essentiel vient de vous ou de MNA |
| 🙋 | Claude ne peut pas le faire — action humaine obligatoire (accès, réunion, validation métier, matériel…) |

> ⚠️ Points à confirmer avec MNA avant de figer quoi que ce soit : durée exacte du stage, accès au référentiel GIE Monétique officiel, hébergement, matériel pour l'IA locale, stack imposée ou libre. Voir [Questions_Encadrant.md](organisation/Questions_Encadrant.md).
