# Guide PCI DSS v4.0.1 — pour le projet

> Objectif : comprendre PCI DSS assez bien pour **modéliser** le référentiel dans la plateforme, **identifier ce qui est automatisable**, et **parler le langage des auditeurs** MNA.
> ⚠️ Ce guide résume ; la référence reste le document officiel *PCI DSS v4.0.1* (Document Library sur pcisecuritystandards.org). Vérifiez toujours les numéros et valeurs exactes dans le texte officiel avant de les coder en règles.

---

## 1. Les bases

- **Qui doit être conforme ?** Toute entité qui **stocke, traite ou transmet** des données de carte, ou qui peut **impacter la sécurité** de cet environnement : commerçants, banques acquéreuses/émettrices, processeurs, hébergeurs, prestataires.
- **Qui l'impose ?** Les **marques de cartes** (Visa, Mastercard…) via les contrats, pas une loi. Le PCI SSC rédige le standard ; les marques définissent qui doit faire un ROC ou un SAQ.
- **Versions :** v3.2.1 (retirée 31/03/2024) → v4.0 (mars 2022, retirée 31/12/2024) → **v4.0.1 (juin 2024, en vigueur)**. Les 51 exigences « future-dated » de la v4.0 sont obligatoires depuis le **31/03/2025**. Une consultation pour la prochaine version a eu lieu en juin–juillet 2026 (thèmes évoqués : nouvelles technologies, IA).
- **Données protégées :**

| Catégorie | Éléments | Stockage autorisé ? |
|---|---|---|
| CHD | PAN, nom du porteur, date d'expiration, code service | Oui, mais **PAN rendu illisible** (chiffrement fort, hachage, troncature, tokenisation) |
| SAD | Piste complète, CVV2/CVC2/CID, PIN/bloc PIN | **Non** après autorisation (même chiffrés) |

---

## 2. Structure du standard : 6 objectifs, 12 exigences

| Objectif | Exigence | Intitulé (résumé) | Potentiel d'automatisation |
|---|---|---|---|
| **Construire et maintenir un réseau et des systèmes sécurisés** | 1 | Installer et maintenir des contrôles de sécurité réseau (NSC : firewalls, ACL, cloud SG) | 🟡 Moyen (export de règles firewall, analyse IA) |
| | 2 | Appliquer des configurations sécurisées à tous les composants | 🟢 Fort (comptes par défaut, services, durcissement) |
| **Protéger les données de compte** | 3 | Protéger les données de compte stockées | 🟡 Moyen (découverte de PAN, crypto) |
| | 4 | Protéger les CHD par une cryptographie forte lors de la transmission sur réseaux publics | 🟢 Fort (scan TLS, versions de protocoles, certificats) |
| **Maintenir un programme de gestion des vulnérabilités** | 5 | Protéger systèmes et réseaux contre les logiciels malveillants | 🟢 Fort (antivirus actif, signatures à jour) |
| | 6 | Développer et maintenir des systèmes et logiciels sécurisés | 🟡 Moyen (correctifs, WAF ; SDLC = documentaire) |
| **Mettre en œuvre des mesures de contrôle d'accès fortes** | 7 | Restreindre l'accès selon le besoin d'en connaître | 🟡 Moyen (groupes privilégiés, revues = documentaire) |
| | 8 | Identifier les utilisateurs et authentifier l'accès | 🟢 **Très fort** (politique de mots de passe, MFA, comptes inactifs) — **idéal pour la démo** |
| | 9 | Restreindre l'accès physique aux CHD | 🔴 Faible (observation, manuel) |
| **Surveiller et tester régulièrement les réseaux** | 10 | Journaliser et surveiller tous les accès | 🟢 Fort (politique d'audit, rétention, NTP, SIEM) |
| | 11 | Tester régulièrement la sécurité des systèmes et réseaux | 🟡 Moyen (rapports de scan/pentest → IA ; dates → règles) |
| **Maintenir une politique de sécurité de l'information** | 12 | Soutenir la sécurité par des politiques et programmes organisationnels | 🔵 Documentaire → **IA locale** (politiques, inventaires, PRA, formation) |
| Annexes | A1 | Exigences supplémentaires pour les prestataires multi-tenant | — |
| | A2 | SSL / TLS précoce pour les terminaux POS POI | — |
| | A3 | DESV (validation complémentaire désignée, sur demande des marques) | — |

Chaque exigence principale se décline en sous-exigences hiérarchiques (ex. `8` → `8.3` → `8.3.6`). Au total, **plusieurs centaines** d'exigences détaillées (à recompter précisément lors de l'import du document officiel — c'est un bon KPI « nombre total de contrôles »).

---

## 3. Anatomie d'une exigence (ce qu'il faut modéliser)

Dans le document v4.0.1, chaque exigence comporte :

| Élément du standard | Champ dans la plateforme (CDC § 6.1) |
|---|---|
| Numéro (ex. 8.3.6) | Identifiant |
| Exigence principale de rattachement | Domaine / parent |
| Texte « Defined Approach Requirements » | Titre / description + critères d'évaluation |
| « Defined Approach Testing Procedures » (ex. 8.3.6.a, 8.3.6.b) | Critères / procédures de test + méthodes autorisées |
| « Customized Approach Objective » | Champ « objectif » (utile pour l'IA et le mapping) |
| « Applicability Notes » | Applicabilité |
| « Purpose », « Good Practice », « Definitions », « Examples » (Guidance) | Aide à l'auditeur, contexte pour l'IA, recommandation type |

**Les 4 types de procédures de test** se rapprochent des méthodes du CDC :
| Procédure PCI | Méthode CDC la plus naturelle |
|---|---|
| **Examine** (documents, configurations, paramètres) | Méthode 1 (script) pour les configs ; Méthode 2 (IA) pour les documents |
| **Test** (tester un système) | Méthode 1 |
| **Interview** (personnel) | Méthode 3 (manuel) |
| **Observe** (processus, locaux) | Méthode 3 (manuel) |

**Résultats possibles d'une exigence dans un ROC v4.x :** *In Place*, *Not Applicable*, *Not Tested*, *Not in Place* (avec mention si contrôle compensatoire ou approche personnalisée). → Les 7 statuts du CDC sont plus fins (ils incluent les états de travail « À vérifier », « En attente de preuve »). Prévoir une **correspondance statuts plateforme → statuts ROC** pour les exports (Conforme → In Place ; N/A → Not Applicable ; Non évalué → Not Tested ; Non conforme / Partiel → Not in Place).

> Remarque : PCI DSS **ne connaît pas** « partiellement conforme » — une exigence est en place ou non. Le statut « partiel » du CDC sert au pilotage interne et au GIE ; dans un export PCI, il doit être traité comme *Not in Place*. Bon point à mentionner au jury.

---

## 4. Exemples d'exigences automatisables (pour le Lot 2)

> Valeurs à revérifier dans le texte officiel avant de les coder.

| Exigence | Ce qu'elle demande (résumé) | Collecte possible | Règle (exemple) |
|---|---|---|---|
| 2.2.2 | Comptes par défaut des fournisseurs supprimés ou désactivés / mot de passe changé | AD, Linux (`/etc/passwd`), BDD | `default_accounts_enabled == 0` |
| 2.2.4 | Seuls les services/protocoles/fonctions nécessaires sont activés | Services Windows, `systemctl`, ports ouverts | services ∉ liste interdite (telnet, ftp…) |
| 4.2.1 | Cryptographie forte pour la transmission du PAN sur réseaux publics, certificats valides | Scan TLS (sslyze/testssl) | `tls_min_version >= 1.2`, certificat non expiré |
| 5.2 / 5.3.1 | Anti-malware déployé et maintenu à jour | Windows Defender (`Get-MpComputerStatus`) | `realtime == true` et signatures < N jours |
| 6.3.3 | Correctifs critiques/élevés installés dans le mois suivant leur publication | Date des derniers KB / `apt` | dernier patch critique ≤ 30 j |
| 7.2.x | Accès selon le besoin d'en connaître, comptes privilégiés limités | Membres des groupes admin AD | count(Domain Admins) ≤ seuil défini → sinon « À vérifier » |
| 8.2.6 | Comptes inactifs supprimés/désactivés sous 90 jours | AD `lastLogonTimestamp` | inactifs_actifs > 90 j == 0 |
| 8.3.4 | Verrouillage après au plus 10 tentatives, durée ≥ 30 min (ou jusqu'à déblocage admin) | AD lockout policy, PAM faillock | `threshold <= 10 && threshold > 0`, `duration >= 30` |
| 8.3.6 | Mot de passe ≥ 12 caractères (ou ≥ 8 si le système ne le supporte pas), lettres et chiffres | AD Default Domain Policy + FGPP, `pwquality` | `min_length >= 12`, `complexity == true` |
| 8.3.7 | Interdiction de réutiliser les 4 derniers mots de passe | AD `PasswordHistoryCount` | `history >= 4` |
| 8.3.9 | Si mot de passe seul facteur : changement au moins tous les 90 jours (ou analyse dynamique) | AD `MaxPasswordAge` | `max_age <= 90` → sinon « À vérifier » (MFA ?) |
| 8.2.8 | Ré-authentification après 15 min d'inactivité | GPO verrouillage d'écran | `screensaver_timeout <= 900` |
| 8.4.x | MFA pour accès administratif au CDE et pour tous les accès au CDE / à distance | Config VPN/IdP | souvent « À vérifier » + preuve |
| 10.2.x | Journaux d'audit activés pour les événements requis | Politique d'audit Windows (`auditpol`), `auditd` | sous-catégories requises == Success/Failure |
| 10.5.1 | Conservation des journaux ≥ 12 mois, dont 3 mois immédiatement disponibles | Config SIEM / rotation | `retention_days >= 365` |
| 10.6.x | Synchronisation horaire (NTP) | `w32tm`, `chrony` | source NTP configurée et synchronisée |
| 11.3.1 / 11.3.2 | Scans de vulnérabilités internes / externes (ASV) trimestriels | Rapports de scan (dates) | 4 rapports sur 12 mois, écart ≤ 3 mois → **IA + règle de date** |

**Exemples typiquement « IA locale » (documents) :** 12.1.1 (politique de sécurité établie, publiée, maintenue), 12.10.1 (plan de réponse aux incidents), 12.6 (programme de sensibilisation), 12.8 (gestion des prestataires), 6.2 (développement sécurisé), 1.2.1 (standards de configuration des NSC), 3.1/4.1/… (« politiques et procédures documentées, à jour, connues de tous » — **chaque exigence principale commence par cette sous-exigence x.1.1**, très bon candidat IA).

**Exemples typiquement « manuel » :** chapitre 9 (accès physique, badges, visiteurs, destruction des supports), entretiens du personnel, observation des processus.

---

## 5. Concepts d'audit à connaître

- **Scoping** : premier travail de l'auditeur → identifier le CDE, les flux de données de carte (diagrammes de flux, exigence 1.2.4), les systèmes connectés. Dans la plateforme : « périmètre et actifs » de la campagne.
- **Segmentation** : réduit le périmètre ; doit être testée (11.4.5 / 11.4.6).
- **Échantillonnage** : l'auditeur teste un **échantillon** représentatif de systèmes. → Le résultat d'un contrôle peut agréger plusieurs actifs (ex. 3 serveurs Linux sur 20). Idée de modélisation : `ControlAssessment` ↔ plusieurs `AssetResult`.
- **Contrôle compensatoire (CCW)** et **approche personnalisée** : doivent pouvoir être documentés (champ texte + preuve) — option « Conforme avec contrôle compensatoire ».
- **Fréquences** : beaucoup d'exigences sont périodiques (trimestriel, semestriel, annuel) → la plateforme peut alerter sur les preuves expirées (CDC § 11.4).
- **Analyse de risque ciblée (TRA)** : certaines fréquences sont définies par l'entité elle-même.

---

## 6. Où trouver les documents officiels (à télécharger vous-mêmes)

Sur le **Document Library** du PCI SSC (pcisecuritystandards.org) :
- PCI DSS v4.0.1 (le standard)
- Summary of Changes v4.0 → v4.0.1
- ROC Template v4.0.1 et ROC Reporting Instructions
- AOC, SAQ (A, D…)
- Glossaire PCI
- « PCI DSS Prioritized Approach » (fichier Excel : **très utile**, il liste toutes les exigences avec un jalon de priorité 1–6 → base idéale pour l'import et pour la criticité par défaut)
- Guides d'information (scoping & segmentation, etc.)

> Licence : téléchargement gratuit après acceptation de la licence d'utilisation. Vérifiez avec MNA que l'utilisation interne du texte dans la plateforme est couverte (sinon, importer identifiants + résumés reformulés + lien vers le document).

---

## 7. Fiche de révision express (à savoir par cœur pour la soutenance)

1. 12 exigences / 6 objectifs ; v4.0.1 en vigueur ; future-dated obligatoires depuis le 31/03/2025.
2. CHD vs SAD ; jamais stocker la SAD après autorisation.
3. CDE + scoping = point de départ de tout audit.
4. ROC (QSA) vs SAQ (auto-évaluation) ; AOC = attestation.
5. Approche définie vs personnalisée ; contrôle compensatoire.
6. Procédures de test : examiner / interroger / observer / tester.
7. Exigence 8 = la plus automatisable (≥ 12 caractères, verrouillage ≤ 10 essais, inactivité 90 j, MFA).
8. Pas de statut « partiel » en PCI : In Place / Not in Place / N/A / Not Tested.
