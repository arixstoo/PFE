# État de l'art — base du chapitre 1 du mémoire

> But : montrer au jury que vous connaissez l'existant et **pourquoi il ne répond pas** au besoin de MNA. Chaque outil cité doit idéalement avoir été **testé** (au moins CISO Assistant).

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
| **IA pour la conformité** | Fonctions IA intégrées aux GRC (souvent via API cloud), travaux de recherche sur LLM + RAG pour l'analyse de politiques | Analyse documentaire ; rarement locale et explicable |

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

### 2.4 NIST OSCAL
- 7 modèles (JSON/XML/YAML) : **Catalog** (le référentiel), **Profile** (sélection/tailoring), Component Definition, SSP, **Assessment Plan**, **Assessment Results**, **POA&M** (plan d'action).
- Intérêt pour vous : **inspirer le format d'import** et proposer un **export OSCAL** en perspective (interopérabilité, CDC § 15).
- Correspondance : Référentiel+version = Catalog ; périmètre de campagne/applicabilité = Profile ; résultats = Assessment Results ; plan d'action = POA&M.

### 2.5 NIST OLIR / SCF
- OLIR définit des **types de relations** entre éléments de référentiels (equal, subset, superset, intersects, not related) → justification académique des 4 types de mapping du CDC.

### 2.6 Outils techniques à citer pour le Lot 2
- **PingCastle**, **Purple Knight** : audit de sécurité AD (score de risque) — vous pouvez comparer vos collecteurs AD à leurs résultats.
- **OpenSCAP / CIS-CAT / Lynis / Wazuh SCA** : conformité de configuration système. Idée de perspective : **importer leurs résultats** comme preuves automatiques.

## 3. Tableau comparatif (à compléter après tests)

| Critère | Excel/Word | CISO Assistant | Eramba | Vanta/Drata | **Notre plateforme** |
|---|---|---|---|---|---|
| Multi-clients (cabinet) cloisonné | ❌ | ⚠️ (à vérifier) | ⚠️ | ❌ (mono-entreprise) | ✅ |
| PCI DSS v4.0.1 | manuel | ✅ | ⚠️ | ✅ | ✅ |
| Référentiel GIE Monétique | manuel | ❌ | ❌ | ❌ | ✅ |
| Référentiels génériques et versionnés | ❌ | ✅ | ⚠️ | ❌ | ✅ |
| Mapping avec règles de couverture strictes | ❌ | ⚠️ (auto-mapping) | ❌ | ⚠️ | ✅ |
| Collecte technique AD / Windows / Linux on-prem | ❌ | ❌ | ❌ | ⚠️ (cloud surtout) | ✅ |
| IA **locale** explicable (citations, confiance) | ❌ | ❌/⚠️ | ❌ | ❌ (cloud) | ✅ |
| On-premise / souveraineté | ✅ | ✅ | ✅ | ❌ | ✅ |
| Rapport d'audit au format du cabinet | manuel | ⚠️ | ⚠️ | ❌ | ✅ |
| Coût | faible | gratuit (AGPL) / payant pro | gratuit / payant | élevé | interne |

Légende : ✅ oui · ⚠️ partiel / à vérifier · ❌ non

## 4. Positionnement (phrase pour le mémoire)

> « Les solutions GRC existantes couvrent la gestion documentaire des référentiels internationaux, mais aucune ne combine, dans un déploiement on-premise adapté au contexte bancaire algérien, le référentiel du GIE Monétique, un mapping croisé à règles de couverture explicites, la collecte technique automatisée et l'analyse de preuves par une IA locale explicable sous validation humaine. »

## 5. Bibliographie de départ (à mettre dans Zotero)
- PCI SSC — *PCI DSS v4.0.1*, juin 2024 ; *Summary of Changes* ; *ROC Template* ; *Prioritized Approach*.
- NIST — *OSCAL* (pages.nist.gov/OSCAL) ; *NIST IR 8278 / 8278A* (OLIR).
- ISO/IEC 27001:2022 et 27002:2022.
- OWASP — *ASVS 4.0.3 / 5.0*, *Top 10*, *Top 10 for LLM Applications* (injection de prompt).
- intuitem — dépôt *ciso-assistant-community* (GitHub), documentation.
- Lewis et al., 2020 — *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks* (article fondateur du RAG).
- Loi algérienne n° 18-07 (2018) et loi n° 25-11 (2025) — JORADP.
- GIE Monétique — rapports d'activité (giemonetique.dz) ; interviews Indjazat (2022).
- Articles de recherche récents « LLM for regulatory compliance / policy analysis » (chercher sur Google Scholar, arXiv).
