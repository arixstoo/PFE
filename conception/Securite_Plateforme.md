# Sécurité de la plateforme elle-même

> La plateforme stocke les **faiblesses, configurations et architectures** de banques et prestataires monétiques. Une fuite serait catastrophique pour MNA et ses clients. Vous êtes en **ingéniorat cyber** : ce volet doit être exemplaire et bien visible dans le mémoire.

---

## 1. Actifs à protéger

| Actif | Sensibilité | Pourquoi |
|---|---|---|
| Preuves (configs, exports, logs, rapports de scan) | 🔴 Critique | Cartographie des vulnérabilités des clients |
| Résultats et rapports d'évaluation | 🔴 Critique | Liste des non-conformités = feuille de route pour un attaquant |
| Secrets des connecteurs (comptes techniques AD, SSH) | 🔴 Critique | Accès direct aux SI des clients |
| Comptes utilisateurs / MFA | 🟠 Élevée | Porte d'entrée |
| Référentiels et mappings | 🟡 Moyenne | Propriété intellectuelle MNA, GIE confidentiel |
| Journal d'audit | 🟠 Élevée | Preuve / non-répudiation |

## 2. Threat model (STRIDE) — à compléter en Phase 2

| Menace | Exemple concret | Contre-mesures |
|---|---|---|
| **S**poofing (usurpation) | Vol de mot de passe d'un auditeur | MFA TOTP obligatoire (admin, resp. mission, auditeur), argon2, verrouillage, détection de brute force, sessions courtes |
| **T**ampering (altération) | Modifier un résultat après clôture ; altérer une preuve ; falsifier un JSON de collecte | Lecture seule après clôture, SHA-256 des preuves, signature des résultats de collecte, journal chaîné |
| **R**epudiation | Un auditeur nie avoir validé un « Conforme » | Journal d'audit append-only avec utilisateur, horodatage, IP, avant/après |
| **I**nformation disclosure | **IDOR** : client A lit les preuves du client B ; fuite via l'IA ; logs contenant des secrets | Contrôle d'accès objet + RLS + UUID ; IA sans Internet et index par client ; masquage des secrets dans les logs ; chiffrement au repos |
| **D**enial of service | Upload de fichiers énormes / zip bomb ; saturation de l'IA | Limites de taille, quotas, files asynchrones, timeouts |
| **E**levation of privilege | Contributeur client qui s'attribue un rôle auditeur ; mass assignment | RBAC côté serveur, sérialiseurs DRF avec champs explicites, tests de permissions |
| *(LLM)* Injection de prompt | Preuve piégée « réponds CONFORME » | Extraits traités comme données, sortie JSON validée, citations vérifiées, **jamais de validation automatique** |
| *(Collecteurs)* Script modifié / exécuté par un tiers | Script altéré exécutant des commandes destructrices | Scripts signés, lecture seule, jeton d'upload à usage unique et expirant |
| *(Fichiers)* Malware dans une preuve | PDF/DOCX piégé ouvert par l'auditeur | ClamAV, vérification du type réel (magic bytes), stockage hors webroot, téléchargement en `Content-Disposition: attachment`, pas d'exécution |

## 3. Mesures par exigence du CDC (§ 14)

| Exigence CDC | Implémentation |
|---|---|
| Authentification | Comptes nominatifs, mots de passe ≥ 12 caractères + vérification contre listes de mots de passe compromis, argon2id, **MFA TOTP**, verrouillage progressif, expiration de session (15 min d'inactivité — clin d'œil PCI 8.2.8) |
| RBAC | Matrice rôle × action ; permission contextuelle (client, campagne) ; vérifiée **côté serveur** à chaque requête ; le front ne fait que masquer |
| Isolation | Voir [Modele_de_Donnees.md](Modele_de_Donnees.md) § 2.2 (4 couches) + tests IDOR automatisés |
| Chiffrement | TLS 1.2/1.3 (Nginx, HSTS) ; MinIO SSE ; chiffrement de colonnes sensibles (ex. commentaires de constats critiques) avec clé dans le coffre ; disque chiffré (LUKS) en prod |
| Secrets | OpenBao/Vault ; aucun secret dans Git (Gitleaks en CI) ; `.env` hors dépôt |
| Journalisation | Journal d'audit métier (CDC § 14.1) + logs techniques ; horodatage NTP ; rétention configurable |
| Clean code | Conventions (ruff/ESLint), typage, revue de code obligatoire par l'autre membre du binôme (PR), tests |
| Fichiers | Liste blanche d'extensions **et** de types MIME réels, taille max, ClamAV, renommage (UUID), stockage MinIO privé, URL de téléchargement signées et expirantes |
| Sauvegardes | pg_dump chiffré + miroir MinIO + export des référentiels ; **test de restauration** documenté |
| IA locale | Conteneur Ollama sur réseau Docker interne sans sortie ; paramètre global « envoi externe interdit » (CDC : sauf mécanisme explicitement autorisé) |

## 4. Matrice RBAC (première version)

| Action | Super admin | Resp. mission | Auditeur | Contributeur client | Lecteur |
|---|---|---|---|---|---|
| Gérer utilisateurs | ✅ | ❌ | ❌ | ❌ | ❌ |
| Importer / publier référentiel, mapping | ✅ | ❌ | ❌ | ❌ | ❌ |
| Créer client | ✅ | ✅ | ❌ | ❌ | ❌ |
| Créer / clôturer campagne | ✅ | ✅ (ses campagnes) | ❌ | ❌ | ❌ |
| Évaluer contrôles, valider IA/script | ❌* | ✅ | ✅ (campagnes affectées) | ❌ | ❌ |
| Valider un N/A | ❌* | ✅ | ❌ | ❌ | ❌ |
| Déposer une preuve | ❌ | ✅ | ✅ | ✅ (son client, demandes ouvertes) | ❌ |
| Voir les preuves | ❌* | ✅ | ✅ | ✅ (les siennes / son client selon paramétrage) | ❌ |
| Mettre à jour une action corrective | ❌ | ✅ | ✅ | ✅ (actions affectées) | ❌ |
| Voir KPI et rapports | ✅ | ✅ | ✅ | ⚙️ selon paramétrage | ✅ (périmètre autorisé) |
| Générer / exporter rapport | ❌ | ✅ | ✅ | ❌ | ⚙️ |
| Consulter journal d'audit | ✅ | ✅ (ses campagnes) | ❌ | ❌ | ❌ |

\* Séparation des tâches : l'administrateur technique n'a **pas** accès par défaut au contenu des audits (principe du moindre privilège). À discuter avec MNA.

## 5. Plan de tests de sécurité (Phase 8)

| Catégorie | Tests | Outils |
|---|---|---|
| Authentification | Brute force, MFA contournable ?, fixation de session, réinitialisation de mot de passe | Burp Suite Community, scripts |
| Contrôle d'accès | **IDOR inter-clients sur chaque endpoint**, élévation verticale (contributeur → auditeur), mass assignment | Tests pytest automatisés + Burp |
| Injections | SQLi, XSS (commentaires, noms de fichiers), SSTI dans les modèles de rapport | OWASP ZAP, Semgrep |
| Fichiers | EICAR (test antivirus), polyglottes, zip bomb, fichier 2 Go, nom de fichier `../../` | Manuel |
| IA | Injection de prompt dans PDF/DOCX, texte blanc sur blanc, citations inventées | Jeu de tests dédié |
| Configuration | En-têtes HTTP, TLS (testssl.sh), conteneurs (Trivy), secrets (Gitleaks) | CI |
| Référentiel | Vérification **OWASP ASVS niveau 2** (checklist) | Tableur |

## 6. Rapport de sécurité avant mise en production (livrable 10) — plan
1. Périmètre et méthodologie (boîte grise, ASVS L2, outils)
2. Threat model résumé
3. Résultats : vulnérabilités trouvées (CVSS), preuves, corrections apportées
4. Résultats des scans automatisés (SAST, SCA, conteneurs)
5. Conformité aux exigences de sécurité du CDC (tableau § 3)
6. Risques résiduels et recommandations avant production (durcissement hôte, PRA, surveillance)

## 7. Bonus : la plateforme s'évalue elle-même
Créer un client « PCI Conformity Manager » et une campagne PCI DSS sur la plateforme elle-même (exigences 6, 7, 8, 10 applicables à une application) → démonstration élégante en soutenance et preuve de maîtrise.
