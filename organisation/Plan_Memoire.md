# Plan du mémoire & de la soutenance

> Adapter au gabarit imposé par votre école. Volume indicatif : 80–120 pages hors annexes. **Rédiger au fil de l'eau**, pas à la fin.

## Plan proposé

**Pages liminaires** : page de garde, remerciements, résumé (FR / EN / AR), liste des figures, tableaux, abréviations, table des matières.

**Introduction générale** (3–4 p.) — contexte, problématique (*« Comment réduire la dépendance aux traitements manuels dans les évaluations de conformité PCI DSS et GIE Monétique tout en conservant la validation humaine et une traçabilité complète ? »*), objectifs, contributions, organisation du mémoire.

**Chapitre 1 — Contexte et état de l'art** (20–25 p.) · *rédiger en S2–S4*
1. Organisme d'accueil (MNA Algérie)
2. La monétique et l'écosystème algérien (GIE Monétique, CIB, EDAHABIA)
3. PCI DSS v4.0.1 (structure, processus d'évaluation, ROC/SAQ)
4. Le référentiel GIE Monétique
5. Cadre réglementaire algérien (loi 18-07 / 25-11, ANPDP, souveraineté)
6. Automatisation de la conformité : GRC, compliance-as-code, OSCAL, mapping (OLIR)
7. LLM et RAG pour l'analyse documentaire ; enjeux d'une IA locale
8. Étude comparative des solutions existantes et positionnement

**Chapitre 2 — Analyse et spécification des besoins** (15 p.) · *S3–S5*
1. Acteurs et rôles · 2. Besoins fonctionnels (EF) · 3. Besoins non fonctionnels et de sécurité · 4. Cas d'utilisation · 5. Scénarios (séquences) · 6. Backlog et planification (Scrum, Gantt)

**Chapitre 3 — Conception** (20 p.) · *S5–S7*
1. Architecture globale (C4) et choix technologiques (ADR) · 2. Modèle de données (versioning, multi-tenant) · 3. Moteur de règles · 4. Moteur de mapping (algorithme, table de décision) · 5. Pipeline IA locale · 6. Conception de la sécurité (threat model STRIDE, RBAC) · 7. Maquettes

**Chapitre 4 — Réalisation** (20 p.) · *au fil des sprints*
1. Environnement de travail et laboratoire · 2. Lot 1 Socle (captures) · 3. Lot 2 Collecteurs · 4. Lot 3 IA locale · 5. Lot 4 Mapping · 6. Déploiement

**Chapitre 5 — Tests, évaluation et sécurité** (12–15 p.) · *S22–S24*
1. Stratégie de tests (unitaires, intégration, E2E) et résultats · 2. **Évaluation de l'IA** (matrice de confusion, faux conformes, comparatif de modèles) · 3. **Taux d'automatisation** des contrôles PCI · 4. **Couverture croisée** obtenue · 5. Gain de temps estimé · 6. Tests de sécurité et rapport · 7. Recette avec MNA

**Conclusion générale et perspectives** (3 p.) — bilan, limites honnêtes, perspectives (autres référentiels : ISO 27001, SWIFT CSCF ; agents continus ; export OSCAL ; fine-tuning ; suggestions de mapping…).

**Bibliographie** · **Annexes** : extraits de référentiel importable, mapping, scripts, cahier de recette, guide utilisateur, rapport de sécurité.

## Soutenance (20–30 min + questions)
| Partie | Durée | Contenu |
|---|---|---|
| Contexte & problématique | 3 min | MNA, PCI DSS, GIE, limites du manuel |
| État de l'art & positionnement | 2 min | Tableau comparatif |
| Conception | 5 min | Architecture, modèle générique, mapping, IA locale, sécurité |
| **Démonstration** | 8 min | Scénario Figure 3 du CDC sur « Banque Démo » : client → campagne PCI → script AD → preuve IA → validation → KPI → mapping GIE → rapport |
| Résultats chiffrés | 3 min | % automatisation, précision IA, couverture, gain de temps, tests sécurité |
| Conclusion & perspectives | 2 min | — |

**Questions probables du jury** (Claude 🤖 peut vous entraîner) :
- Comment garantissez-vous qu'un client ne voit jamais les données d'un autre ?
- Que se passe-t-il si l'IA hallucine un « Conforme » ?
- Pourquoi une IA locale et pas une API cloud ? Quelles performances sur votre matériel ?
- Pourquoi un mapping partiel ne peut-il jamais donner « Conforme » ?
- Comment gérez-vous la sortie d'une nouvelle version de PCI DSS ?
- Les scripts de collecte sont-ils dangereux pour le SI du client ?
- Quelle est la différence entre un ROC et un SAQ ? Entre approche définie et personnalisée ?
- Quelle part du travail a été faite par chacun ?
- Quelles sont les limites de votre solution ?
