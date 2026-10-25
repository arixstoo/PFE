# GIE Monétique & contexte algérien

> ⚠️ Le référentiel de sécurité du GIE Monétique **n'est pas public**. Ce fichier rassemble ce qu'on sait de sources ouvertes ; **il doit être complété dès que MNA vous fournit le document officiel** (section 5 à remplir).

---

## 1. Le GIE Monétique en bref

- **Nature :** Groupement d'Intérêt Économique créé par les **banques algériennes**, **Algérie Poste** et la **Banque d'Algérie** pour organiser la monétique interbancaire.
- **Missions principales :**
  - gérer le **système de paiement par carte interbancaire CIB** (retraits GAB, paiements TPE, paiement en ligne) ;
  - assurer l'**interopérabilité** entre les acteurs (banques, Algérie Poste / carte EDAHABIA) ;
  - **normaliser et sécuriser** l'écosystème : cartes (EMV), terminaux, interfaces, routage, processing ;
  - **homologuer / certifier** : un TPE ne peut être commercialisé qu'après homologation du GIE ; procédures d'agrément des prestataires (ex. maintenance des TPE) ; approbation des cartes CIB ; exigences PCI PTS pour les GAB.
- **Site :** giemonetique.dz (rapports d'activité, procédures d'agrément publiées).
- **Actualité :** l'écosystème se développe (agréments de nouveaux acteurs en 2026, développement du e-paiement), ce qui augmente le nombre d'entités à évaluer → **marché réel** pour la plateforme de MNA.

## 2. Le référentiel de sécurité du GIE — ce qu'on sait

D'après des interviews de responsables « Risque, sécurité et conformité » du GIE Monétique (presse spécialisée Indjazat, 2022) :

| Information | Valeur rapportée |
|---|---|
| Volume | **Plus de 350 exigences de sécurité** (une source plus ancienne parlait de ~200) |
| Inspiration | **PCI DSS** et **ISO 27000** (27001/27002) |
| Objectif | Aligner **toutes les banques de la place sur un seul référentiel sécuritaire** |
| Domaines évoqués | Sécurité de la **carte**, du **GAB/ATM**, du **TPE**, des **interfaces**, du **routage** et du **processing** |
| Niveau de conformité | Membres à plus de 75 %, certains à 90 % (2022) |
| Contrôle | **Audits externes** par des cabinets, prévus à partir de fin 2023 ; cartographie des risques revue annuellement |

### Conséquences pour le projet
1. **Fort recouvrement attendu avec PCI DSS** (inspiration directe) → le mapping aura beaucoup de correspondances « complètes » ou « partielles ».
2. **Exigences spécifiques au contexte algérien / monétique** (GAB, TPE, interfaces avec le switch CIB, procédures GIE) → contrôles **sans équivalent PCI** (« Non mappé »).
3. **Exigences d'origine ISO 27001** (gouvernance, SMSI, gestion des risques) → souvent couvertes partiellement par l'exigence 12 de PCI DSS.
4. Le statut « Partiellement conforme » du CDC vient probablement de la pratique d'audit GIE (contrairement à PCI DSS).
5. Les cabinets d'audit externes (comme MNA) sont précisément les utilisateurs cibles de la plateforme.

## 3. Cadre légal et réglementaire algérien à citer dans le mémoire

| Texte / acteur | Lien avec le projet |
|---|---|
| **Loi n° 18-07 du 10 juin 2018** relative à la protection des personnes physiques dans le traitement des données à caractère personnel — modifiée et complétée par la **loi n° 25-11 du 24 juillet 2025** (selon le cabinet Gide ; détails à vérifier au JORADP) | Les preuves et données clients contiennent des données personnelles (noms, comptes utilisateurs, logs) → obligation de **mesures techniques et organisationnelles** de sécurité (chiffrement, contrôle d'accès, journalisation) |
| **ANPDP** — Autorité Nationale de Protection des Données à caractère Personnel (opérationnelle depuis 2023) | A appelé (2024) les responsables de traitement à intégrer la sécurité de l'hébergement et des systèmes dans leurs projets numériques → argument pour l'**hébergement on-premise** et l'**IA locale** |
| **Souveraineté numérique / cybersécurité** (cybersécurité qualifiée de question de souveraineté nationale par la Présidence, 2023 ; création d'un centre national de cybersécurité) | Justifie l'exigence « **aucune transmission vers un service d'IA public** » |
| **Banque d'Algérie** (règlements sur les systèmes et moyens de paiement) | Régulateur du secteur bancaire et des paiements, membre fondateur du GIE |
| **ASSI** — Agence de Sécurité des Systèmes d'Information (cadre national de sécurité des SI) | À mentionner si MNA ou les clients y sont soumis — **à vérifier avec l'encadrant** |

> 💡 Pour le mémoire : une section « Contexte réglementaire » d'une page qui explique pourquoi **l'IA doit être locale** (données sensibles + loi 18-07 + souveraineté) et pourquoi **l'on-premise** est exigé — c'est une justification forte de vos choix d'architecture.

## 4. Questions à poser à MNA sur le référentiel GIE

1. Quelle est la **version** officielle et sa date ? Existe-t-il plusieurs versions (historique) ?
2. Format disponible : PDF, Word, **Excel** (idéal pour l'import) ?
3. Structure : domaines / chapitres, numérotation des exigences, niveaux (exigence / sous-exigence) ?
4. Y a-t-il des **critères d'évaluation**, une **criticité**, des **preuves attendues** par exigence ?
5. Quelle **échelle de résultat** le GIE attend-il (conforme / partiel / non conforme / N/A ?) et quel format de **rapport** ?
6. Le GIE ou MNA a-t-il déjà un **mapping** (même partiel) avec PCI DSS ou ISO 27001 ?
7. Quelles entités sont auditées (banques, Algérie Poste, processeurs, prestataires, commerçants e-paiement) ?
8. **Confidentialité** : peut-on mettre le texte dans le dépôt Git ? Peut-on le citer dans le mémoire (annexes) ? Peut-on me (Claude) le soumettre pour aider à la structuration/au mapping ?
9. Fréquence des audits et exigences de **reconduction** (historique d'une campagne à l'autre).

## 5. Structure du référentiel GIE (à remplir quand vous l'aurez)

| Domaine | Nb d'exigences | Exemples | Équivalent PCI probable |
|---|---|---|---|
| … | … | … | … |

## 6. En attendant : référentiel GIE fictif pour développer

Pour ne pas bloquer le développement, créer un fichier `referentiels/gie_fictif_v0.json` avec ~40 exigences plausibles réparties en domaines (Gouvernance, Gestion des accès, Sécurité réseau, Sécurité des GAB/TPE, Cryptographie & HSM, Journalisation, Continuité, Prestataires), clairement marqué **« FICTIF – usage de développement »**. Claude 🤖 peut le générer. Il sera remplacé par le vrai référentiel via l'import (ce qui démontre au passage que la plateforme est générique).
