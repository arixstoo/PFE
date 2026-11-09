# Glossaire du projet

> Classé par thème. Gardez ce fichier ouvert pendant vos lectures.

## 1. Paiement & monétique

| Terme | Définition |
|---|---|
| **Monétique** | Ensemble des technologies et processus de paiement électronique par carte (cartes, terminaux, réseaux, serveurs d'autorisation). |
| **Porteur (cardholder)** | Personne titulaire de la carte. |
| **Émetteur (issuer)** | Banque qui délivre la carte au porteur. |
| **Acquéreur (acquirer)** | Banque du commerçant, qui reçoit les transactions. |
| **Commerçant (merchant)** | Accepte les paiements par carte. |
| **Prestataire de services (service provider)** | Entité qui stocke/traite/transmet des données de carte pour le compte d'autres (processeur, hébergeur, centre de traitement). |
| **Switch / routage** | Système qui achemine les transactions entre acquéreur et émetteur (rôle du GIE Monétique pour le réseau CIB). |
| **Processing** | Traitement des transactions (autorisation, compensation). |
| **Autorisation / compensation / règlement** | Validation en temps réel / échange des transactions entre banques / mouvement effectif des fonds. |
| **TPE / POS** | Terminal de Paiement Électronique (chez le commerçant). |
| **GAB / DAB / ATM** | Guichet / Distributeur Automatique de Billets. |
| **EMV** | Standard des cartes à puce (Europay-Mastercard-Visa). |
| **HSM** | Hardware Security Module : boîtier sécurisé qui protège les clés cryptographiques (PIN, etc.). |
| **CIB** | Carte Interbancaire algérienne, gérée par le GIE Monétique. |
| **EDAHABIA** | Carte de paiement d'Algérie Poste. |
| **GIE** | Groupement d'Intérêt Économique (forme juridique). Ici : GIE Monétique, organisme interbancaire algérien. |
| **E-paiement** | Paiement en ligne (sur Internet), en Algérie via la plateforme CIB / EDAHABIA. |

## 2. PCI DSS

| Terme | Définition |
|---|---|
| **PCI SSC** | PCI Security Standards Council : organisme qui publie PCI DSS (fondé par Visa, Mastercard, Amex, Discover, JCB). |
| **PCI DSS** | Payment Card Industry Data Security Standard. Version en vigueur : **v4.0.1**. |
| **Account data** | Données de compte = CHD + SAD. |
| **CHD (Cardholder Data)** | PAN + nom du porteur + date d'expiration + code service. |
| **PAN** | Primary Account Number : le numéro de carte (16 chiffres en général). Donnée centrale à protéger. |
| **SAD (Sensitive Authentication Data)** | Piste magnétique complète, CVV2/CVC2, PIN / bloc PIN. **Interdit de les stocker après autorisation.** |
| **CDE** | Cardholder Data Environment : personnes, processus et technologies qui stockent/traitent/transmettent des CHD/SAD, + systèmes connectés. |
| **Scoping (périmètre)** | Détermination de ce qui est dans le CDE. Étape la plus importante d'un audit PCI. |
| **Segmentation** | Isolation réseau du CDE pour réduire le périmètre. |
| **Defined approach** | Méthode classique : appliquer l'exigence telle qu'écrite + procédures de test. |
| **Customized approach** | Nouveauté v4 : atteindre l'**objectif** de l'exigence par un moyen différent, avec analyse de risque ciblée. |
| **TRA (Targeted Risk Analysis)** | Analyse de risque ciblée exigée par certaines exigences v4 (ex. fréquence de certaines activités). |
| **Compensating control** | Contrôle compensatoire quand une exigence ne peut pas être respectée pour une raison technique/business légitime. Documenté dans une **CCW** (Compensating Control Worksheet). |
| **Future-dated requirements** | Exigences v4.0 qui étaient « bonnes pratiques » jusqu'au 31/03/2025, obligatoires depuis. |
| **Testing procedures** | Procédures de test associées à chaque exigence : *examine* (examiner docs/config), *interview*, *observe*, *test*. |
| **QSA** | Qualified Security Assessor : auditeur certifié par le PCI SSC, travaillant pour une société QSA. |
| **ISA** | Internal Security Assessor : auditeur interne certifié. |
| **ASV** | Approved Scanning Vendor : prestataire agréé pour les scans de vulnérabilités **externes** trimestriels. |
| **ROC** | Report on Compliance : rapport d'audit détaillé rédigé par le QSA (modèle officiel « ROC Template »). |
| **AOC** | Attestation of Compliance : attestation synthétique de conformité. |
| **SAQ** | Self-Assessment Questionnaire : auto-évaluation pour les petites entités (SAQ A, A-EP, B, B-IP, C, C-VT, P2PE, D…). |
| **Niveaux marchands / prestataires** | Niveaux 1–4 selon le volume de transactions annuel (définis par les marques de cartes), déterminent ROC vs SAQ. |
| **Résultats d'évaluation ROC** | « In Place », « Not Applicable », « Not Tested », « Not in Place » (+ mention du contrôle compensatoire / approche personnalisée). |
| **PCI PTS / PCI PIN / P2PE / 3DS / SSF** | Autres standards PCI (terminaux, sécurité du PIN, chiffrement point-à-point, 3-D Secure, logiciels). |
| **Tokenisation** | Remplacement du PAN par un jeton sans valeur. |
| **Troncature / masquage** | Affichage partiel du PAN (ex. 6 premiers + 4 derniers chiffres max). |

## 3. Conformité, audit & GRC

| Terme | Définition |
|---|---|
| **GRC** | Gouvernance, Risques et Conformité. Les plateformes GRC (CISO Assistant, Eramba…) sont la famille de logiciels de ce projet. |
| **Référentiel / framework** | Ensemble structuré d'exigences (PCI DSS, GIE, ISO 27001, NIST CSF…). |
| **Exigence / contrôle** | Unité évaluable d'un référentiel. Dans ce projet, « contrôle » = « exigence ». |
| **Campagne / mission d'audit** | Une évaluation d'un client à une date, sur un référentiel et une version, avec un périmètre. |
| **Preuve (evidence)** | Élément objectif qui démontre la conformité (document, config, log, capture, résultat de script). |
| **Constat (finding)** | Observation de l'auditeur, notamment un écart. |
| **Non-conformité / écart (gap)** | Exigence non (ou partiellement) satisfaite. |
| **Remédiation / plan d'action** | Actions correctives pour traiter les écarts, avec responsable et échéance. |
| **Retest** | Nouvelle vérification après correction. |
| **Mapping (correspondance)** | Lien entre exigences de deux référentiels. |
| **Couverture croisée** | Part d'un référentiel cible couverte par les résultats d'un référentiel source via le mapping. |
| **Gap analysis** | Analyse d'écarts entre l'existant et le référentiel. |
| **Criticité** | Niveau de gravité d'un écart (critique, élevé, moyen, faible). |
| **KPI** | Key Performance Indicator. |
| **OSCAL** | Open Security Controls Assessment Language (NIST) : formats JSON/XML/YAML standard pour catalogues, profils, résultats d'évaluation. |
| **Compliance as code** | Exprimer les règles de conformité sous forme de code/données exécutables (ce que fait le moteur de règles du Lot 2). |
| **ISO/IEC 27001 / 27002** | Norme de système de management de la sécurité de l'information / guide des mesures. |
| **PSSI** | Politique de Sécurité des Systèmes d'Information. |
| **RSSI** | Responsable de la Sécurité des SI. |

## 4. Sécurité de la plateforme

| Terme | Définition |
|---|---|
| **Multi-tenant** | Une seule instance logicielle sert plusieurs clients (tenants) avec isolation des données. Ici, **deux niveaux** : organisation (tenant SaaS) → client / entité auditée. |
| **SaaS** | Software as a Service : logiciel fourni en service (souvent hébergé par l'éditeur), vendu par abonnement / **licence**. Mode B de la plateforme (réunion du 05/11/2026). |
| **Organisation (tenant)** | Dans la plateforme : MNA (mode service) ou une entreprise **licenciée** (mode SaaS), totalement isolée des autres. |
| **Licence / module** | Droit d'usage d'une organisation : durée, nombre d'utilisateurs, **modules** référentiels activés (PCI DSS, GIE…). |
| **Pool / Bridge / Silo** | Modèles d'isolation SaaS : base partagée filtrée (pool), schéma par tenant (bridge), instance dédiée (silo). |
| **RBAC** | Role-Based Access Control : droits attribués selon le rôle. |
| **ABAC** | Attribute-Based Access Control : droits selon des attributs (client, campagne…). Le CDC décrit un RBAC contextuel proche de l'ABAC. |
| **IDOR** | Insecure Direct Object Reference : accès à un objet d'un autre utilisateur en changeant un ID — **la** faille à tester en multi-tenant. |
| **RLS** | Row-Level Security (PostgreSQL) : filtrage des lignes au niveau de la base. |
| **MFA / TOTP** | Authentification multifacteur / mot de passe à usage unique basé sur le temps (Google Authenticator…). |
| **SSO / OIDC / SAML** | Authentification unique / protocoles de fédération d'identité. |
| **Coffre de secrets (vault)** | Stockage sécurisé des mots de passe/clés : HashiCorp Vault, OpenBao (fork open source). |
| **Hash (SHA-256)** | Empreinte cryptographique permettant de vérifier l'intégrité d'un fichier. |
| **Chiffrement au repos / en transit** | Données chiffrées sur disque / pendant le transfert (TLS 1.2+). |
| **Journal d'audit (audit trail)** | Historique infalsifiable (append-only) des actions. |
| **Moindre privilège** | Donner le minimum de droits nécessaires. |
| **STRIDE** | Méthode de threat modeling : Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege. |
| **OWASP Top 10 / ASVS** | Top 10 des risques web / standard de vérification de la sécurité applicative (niveaux 1–3). |
| **SAST / DAST / SCA** | Analyse statique du code / dynamique de l'appli en fonctionnement / des dépendances. |
| **On-premise** | Hébergé dans les locaux / l'infrastructure du client, pas dans un cloud public. |

## 5. Automatisation & IA

| Terme | Définition |
|---|---|
| **Agent** | Logiciel installé sur la cible qui collecte et remonte des informations. |
| **Script de collecte** | Programme ponctuel exécuté sur/contre la cible (PowerShell, Bash, Python). |
| **Connecteur** | Intégration où la plateforme interroge la cible via une API/protocole (LDAP, WinRM, SSH, API REST du firewall/SIEM). |
| **Active Directory (AD) / GPO / FGPP** | Annuaire Microsoft / stratégies de groupe / politiques de mots de passe affinées. |
| **LDAP / LDAPS** | Protocole d'annuaire / version chiffrée TLS. |
| **WinRM / SSH** | Administration distante Windows / Linux. |
| **SIEM** | Security Information and Event Management (Wazuh, Splunk, QRadar…). |
| **Moteur de règles** | Composant qui compare des valeurs observées à des critères (opérateurs) définis en données. |
| **LLM** | Large Language Model (Qwen, Mistral, Llama…). |
| **IA locale / on-prem** | LLM exécuté sur un serveur interne, sans appel à une API cloud. |
| **Ollama / vLLM / llama.cpp** | Outils pour exécuter des LLM localement. |
| **Quantification (Q4, Q8)** | Réduction de la précision des poids pour faire tenir un modèle en mémoire (moins de VRAM, un peu moins de qualité). |
| **VRAM** | Mémoire de la carte graphique (GPU) ; détermine la taille de modèle exécutable. |
| **Token** | Unité de texte traitée par un LLM (~¾ de mot). |
| **Fenêtre de contexte** | Quantité maximale de tokens qu'un modèle lit en une fois. |
| **Embedding** | Vecteur numérique représentant le sens d'un texte ; sert à la recherche sémantique. |
| **RAG** | Retrieval-Augmented Generation : on recherche les passages pertinents puis on les donne au LLM pour qu'il réponde en s'appuyant dessus. |
| **pgvector** | Extension PostgreSQL pour stocker/rechercher des embeddings. |
| **Chunking** | Découpage d'un document en morceaux pour le RAG. |
| **Hallucination** | Réponse inventée par le LLM. Contre-mesure : citations vérifiées. |
| **Sortie structurée (JSON mode)** | Forcer le LLM à répondre selon un schéma JSON. |
| **Injection de prompt** | Texte malveillant dans un document qui tente de détourner les instructions du LLM. |
| **Score de confiance** | Estimation de la fiabilité de la proposition IA (≠ statut de conformité). |
| **Human-in-the-loop** | Un humain valide les décisions de l'automate/IA — principe central du CDC. |
| **OCR** | Reconnaissance optique de caractères (Tesseract) pour les captures/scans. |
