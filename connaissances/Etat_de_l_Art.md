# Étude de l'existant — base du chapitre 1 du mémoire

> **Consigne MNA (réunion du 05/11/2026) :** l'étude de l'existant est une **grande partie** du projet, **y compris sur les technologies**. Durée indicative 4–5 semaines, **à terminer avant janvier** (S1–S5, jusqu'au 13/12/2026 au plus tard) ; plus courte si possible. Toute affirmation doit être **référencée** (articles, normes, docs officielles — **pas Wikipédia**) → [Bibliographie.md](Bibliographie.md).
>
> But : montrer au jury (et à MNA) que vous connaissez l'existant **métier, outils et technologies**, et **pourquoi il ne répond pas** au besoin. Chaque outil cité doit idéalement avoir été **testé** (au moins CISO Assistant).

---

## 0. Les quatre volets de l'étude

| Volet | Question à laquelle il répond | Méthode | Responsable | Semaines |
|---|---|---|---|---|
| **V1 — Processus d'audit actuel (MNA)** | Comment MNA réalise-t-elle un audit PCI DSS / GIE aujourd'hui ? Où perd-elle du temps ? | Entretiens avec Noor / Rabah / auditeurs ; **observation d'audits côté MNA** (autorisée, D1) ; analyse de modèles anonymisés ; démarche ISO 19011 [S9] | Rostom + Rahim | S1–S4 |
| **V2 — Référentiels** | Que contiennent PCI DSS v4.0.1 et le GIE ? Qu'est-ce qui a changé depuis PCI DSS 3.2 (base du GIE) ? | Lecture [S1], [S2], [S3] ; tableau 3.2.1 → 4.0 | Rostom + Rahim | S1–S3 |
| **V3 — Solutions existantes** | Quels outils (GRC, automatisation, scanners) existent ? Que couvrent-ils ? | Tests réels + grille comparative (§ 3) | Profil B | S2–S4 |
| **V4 — Technologies** | Quelles technologies pour construire la plateforme (deux modes : service et SaaS) ? | Comparatifs pondérés + références → [Choix_Technologiques.md](../conception/Choix_Technologiques.md) | Profil A (Rostom pour la stack, Rahim pour le dimensionnement) | S1–S4 |
| **+ État de l'art scientifique** | Que dit la recherche sur l'IA pour la conformité, la conformité continue, le mapping, le SaaS multi-tenant ? | Fiches de lecture des articles [A1]–[A14] | Rostom + Rahim | S2–S5 |

### 0.1 Grille d'observation d'un audit (V1) — à imprimer
| Rubrique | À noter |
|---|---|
| Contexte | Référentiel, type d'entité auditée, phase de l'audit (cadrage, collecte, analyse, restitution) |
| Étapes observées | Ordre réel des étapes, qui fait quoi |
| Périmètre | Comment le périmètre / CDE est défini et validé (lien avec la réponse D6) |
| Preuves | Types demandées, format, canal (mail, partage), délais, relances |
| Décision | Comment l'auditeur conclut conforme / partiel / non conforme ; qui valide ; désaccords |
| Outils | Excel, Word, modèles, scripts éventuels |
| Temps | Temps passé par contrôle ou par domaine (estimation) |
| Douleurs | Répétitions, erreurs, pertes d'information, preuves obsolètes |
| Idées | Ce que la plateforme pourrait automatiser ou structurer |

> ⚠️ Confidentialité (D7) : ne noter **aucune information identifiante** sur un client ou une vulnérabilité réelle ; ces notes ne vont ni dans Git ni dans le mémoire sans anonymisation validée par MNA.

---

## 1. Familles de solutions

| Famille | Exemples | Ce qu'elles font |
|---|---|---|
| **Tableurs + documents** (existant MNA) | Excel, Word, partage de fichiers | Flexible mais manuel, non traçable, pas de KPI ni historique fiable |
| **Plateformes GRC open source** | **CISO Assistant** (intuitem, France), **Eramba** (community), SimpleRisk, Gapps | Référentiels, contrôles, audits, risques, preuves, parfois mappings |
| **GRC commerciales d'entreprise** | ServiceNow IRM, RSA Archer, MetricStream, OneTrust | Très complètes, chères, lourdes, souvent cloud |
| **Automatisation de conformité SaaS** | Vanta, Drata, Secureframe, Sprinto | Connecteurs cloud (AWS, Okta, GitHub…) + collecte automatique de preuves ; SaaS US, peu adaptées à l'on-prem et aux SI bancaires algériens |
| **Outils de conformité technique (scanners)** | CIS-CAT, OpenSCAP, Wazuh SCA, Lynis, PingCastle / Purple Knight (AD), Nessus compliance audits | Vérifient des configurations contre des benchmarks (CIS, STIG) → **techniques** mais pas de gestion de campagne / rapport d'audit PCI / mapping |
| **Standards d'échange** | **NIST OSCAL**, NIST OLIR, Secure Controls Framework (SCF) | Formats et méthodes pour représenter catalogues, profils, résultats et correspondances |
| **IA pour la conformité** | Fonctions IA intégrées aux GRC (souvent via API cloud), travaux de recherche sur LLM + RAG pour l'analyse de politiques [A2]–[A5] | Analyse documentaire ; rarement locale et explicable |

## 2. Focus sur les plus pertinents

### 2.1 CISO Assistant (intuitem) — **le plus proche, à installer et tester**
- Open source (licence **AGPL-3.0**), éditeur français, self-hosted (Docker), stack **Django + SvelteKit**.
- « One-stop-shop » GRC : risques, AppSec, audits ; **+70 référentiels** avec **auto-mapping** entre référentiels ; PCI DSS figure dans sa bibliothèque (v4.0 / v4.0.1 selon les versions).
- Concepts intéressants à étudier : bibliothèque de référentiels en **YAML**, « requirement mapping sets », audits (« compliance assessments »), preuves, plans d'action.
- **Ce qui manque pour MNA** (à vérifier en le testant) : référentiel GIE Monétique, collecteurs techniques (AD/Linux/Windows) intégrés au résultat des contrôles, analyse de preuves par **IA locale** explicable, règles de couverture strictes du CDC (partiel ≠ conforme, multiple, « À vérifier »), workflow cabinet ↔ contributeur client, modèle de rapport MNA.
- ⚠️ AGPL : si vous réutilisez son code, toute la plateforme devient AGPL → discuter avec MNA. On peut s'en **inspirer** (concepts, format) sans copier le code.

### 2.2 Eramba
- GRC open source historique (édition community + enterprise), PHP. Contrôles, politiques, audits internes, risques, exceptions. Interface ancienne, pas d'IA, automatisation limitée.

### 2.3 Vanta / Drata (référence « compliance automation »)
- Popularisent l'idée de **tests continus automatisés** via connecteurs et de collecte de preuves automatique — exactement le Lot 2, mais orientés SOC 2 / ISO / cloud, SaaS, payants, données hébergées hors d'Algérie → incompatibles avec la contrainte on-premise et la sensibilité bancaire.

### 2.4 Travaux de recherche les plus proches (IA + conformité)
- **CTRAG** [A2] (2026) : RAG qui extrait des questions de contrôle d'un texte réglementaire et les confronte aux documents d'une entreprise ; évalué dans un cabinet « Big Four » (F1 ≈ 78 %, rappel ≈ 85 %). → **Très proche de notre Méthode 2** ; montre que la validation humaine reste indispensable.
- **RAG pour l'analyse d'écarts ISO 27001 / NIS2** [A5] (mémoire Chalmers) : protocole d'évaluation réutilisable.
- **PrivComp-KG** [A4] et **RAGulating Compliance** [A3] : graphes de connaissances + LLM pour la conformité (pistes d'évolution).
- **Risques** : hallucinations [A7] et injection de prompt indirecte [A6], [S11] → justifient nos garde-fous (citations vérifiées, pas de chatbot, validation auditeur).
- **Conformité continue** [A13] : cadre conceptuel du Lot 2.
- **Constat terrain PCI DSS** [A12] : faible taux de conformité complète (32,4 % en 2022 selon les auteurs) → besoin d'outils de pilotage.

### 2.5 NIST OSCAL
- 7 modèles (JSON/XML/YAML) : **Catalog** (le référentiel), **Profile** (sélection/tailoring), Component Definition, SSP, **Assessment Plan**, **Assessment Results**, **POA&M** (plan d'action).
- Intérêt pour vous : **inspirer le format d'import** et proposer un **export OSCAL** en perspective (interopérabilité, CDC § 15).
- Correspondance : Référentiel+version = Catalog ; périmètre de campagne/applicabilité = Profile ; résultats = Assessment Results ; plan d'action = POA&M.

### 2.6 NIST OLIR / SCF
- OLIR [S5], [S6] définit des **types de relations** entre éléments de référentiels (equal, subset, superset, intersects, not related) → justification académique des 4 types de mapping du CDC.

### 2.7 Outils techniques à citer pour le Lot 2
- **PingCastle**, **Purple Knight** : audit de sécurité AD (score de risque) — vous pouvez comparer vos collecteurs AD à leurs résultats.
- **OpenSCAP / CIS-CAT / Lynis / Wazuh SCA** : conformité de configuration système. Idée de perspective : **importer leurs résultats** comme preuves automatiques.

### 2.8 Modèles SaaS et licences (mode B, D11)
- Comment les éditeurs GRC vendent leurs offres : par utilisateur, par module/référentiel, par organisation, auto-hébergé vs SaaS (comparer CISO Assistant community/pro, Eramba community/enterprise, offres SaaS).
- Architecture multi-tenant : modèles pool / bridge / silo [D2], [D3] ; risques de maintenance et d'isolation [A14].

## 3. Tableau comparatif (à compléter après tests)

| Critère | Excel/Word | CISO Assistant | Eramba | Vanta/Drata | **Notre plateforme** |
|---|---|---|---|---|---|
| Multi-clients (cabinet) cloisonné — mode A | ❌ | ⚠️ (à vérifier) | ⚠️ | ❌ (mono-entreprise) | ✅ |
| SaaS multi-organisation sous licence — mode B | ❌ | ⚠️ (à vérifier) | ⚠️ | ✅ (SaaS) | ✅ |
| PCI DSS v4.0.1 | manuel | ✅ | ⚠️ | ✅ | ✅ |
| Référentiel GIE Monétique | manuel | ❌ | ❌ | ❌ | ✅ |
| Référentiels génériques et versionnés | ❌ | ✅ | ⚠️ | ❌ | ✅ |
| Mapping avec règles de couverture strictes | ❌ | ⚠️ (auto-mapping) | ❌ | ⚠️ | ✅ |
| Collecte technique AD / Windows / Linux on-prem | ❌ | ❌ | ❌ | ⚠️ (cloud surtout) | ✅ |
| IA **locale** explicable (statut + justification + citations, pas de chatbot) | ❌ | ❌/⚠️ | ❌ | ❌ (cloud) | ✅ |
| On-premise / souveraineté | ✅ | ✅ | ✅ | ❌ | ✅ |
| Rapport d'audit au format du cabinet | manuel | ⚠️ | ⚠️ | ❌ | ✅ |
| Coût / licence | faible | gratuit (AGPL) / payant pro | gratuit / payant | élevé | interne (service) / licence (SaaS) |

Légende : ✅ oui · ⚠️ partiel / à vérifier · ❌ non

## 4. Positionnement (phrase pour le mémoire)

> « Les solutions GRC existantes couvrent la gestion documentaire des référentiels internationaux, mais aucune ne combine, dans une plateforme utilisable à la fois **comme outil de cabinet d'audit et comme SaaS sous licence** — déployable on-premise pour le contexte bancaire algérien —, le référentiel du GIE Monétique, un mapping croisé à règles de couverture explicites, la collecte technique par commandes contrôlées et l'analyse de preuves par une IA locale explicable sous validation humaine. »

## 5. Bibliographie

➡️ Déplacée et complétée dans **[Bibliographie.md](Bibliographie.md)** (références vérifiées, avec niveau de fiabilité et usage). Règle : **pas de Wikipédia** ; privilégier articles évalués par les pairs, normes et documentation officielle.

## 6. Livrable de fin d'étude (J1, au plus tard le 13/12/2026)
1. Synthèse du processus d'audit MNA (V1), anonymisée.
2. Fiches PCI DSS v4.0.1 + tableau des changements 3.2.1 → 4.0 (V2).
3. Grille comparative des outils testés + positionnement (V3).
4. Étude technologique et décisions (V4) — reprend [Choix_Technologiques.md](../conception/Choix_Technologiques.md).
5. État de l'art scientifique (fiches de lecture).
6. Brouillon du chapitre 1 du mémoire.
