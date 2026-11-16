# Estimation des ressources — VPS / serveur du projet

> **Responsable : Rahim** · Demandée par MNA à la réunion du 05/11/2026 (décision D2), échéance 16/11/2026.
> Statut : ✅ **version finale rédigée** (16/11/2026), appuyée par un **benchmark réel** de LLM locaux (§ 4).
> Document de référence envoyé à MNA (anglais, charte MNA) : [memoire/MNA Nostra - Analysis and Estimation of Project’s Required Resources (charte MNA).docx](../memoire/MNA%20Nostra%20-%20Analysis%20and%20Estimation%20of%20Project’s%20Required%20Resources%20(charte%20MNA).docx) (version d'origine non mise en forme dans le même dossier).
> Ce fichier en est la **synthèse en français** ; en cas d'écart, le document Word fait foi.
> Dépend de : [Choix_Technologiques.md](Choix_Technologiques.md) (stack) · [Architecture_Technique.md](Architecture_Technique.md) (composants) · benchmark : [github.com/MedjberAbderrahim/llm-benchmark](https://github.com/MedjberAbderrahim/llm-benchmark).

---

## 1. Résumé envoyé à MNA

Ressources nécessaires pour réaliser le projet et obtenir un produit **prêt pour la production**, en deux niveaux selon le budget et les priorités de MNA :

| Ressource | **Minimum** | **Confortable** |
|---|---|---|
| vCPU | **8** | **12** |
| Stockage SSD | **512 Go** | **512 Go** |
| RAM | **48 Go** | **64 Go** |
| VRAM du GPU | **24 Go** | **32 Go** |

- **24 Go de VRAM** : modèles de la classe **~14 B** (performance moyenne) avec une fenêtre de contexte de **16K**.
- **32 Go de VRAM** : modèles de la classe **~27 B** (plus fiables) avec une fenêtre de **16K**.
- Système : Linux sans interface graphique (Ubuntu Server 24.04 LTS ou RHEL) + Docker/Podman.
- Le laboratoire de tests (Active Directory, Windows, Linux) reste sur **nos PC** (VMware).

> **Changement par rapport au brouillon du 14/11** (8–16 vCPU, 32–64 Go RAM, GPU 24 Go optionnel, 200–500 Go) : le benchmark montre que le **GPU n'est plus optionnel** (CPU seul = 45 h pour 300 contrôles avec un 14 B), que la **RAM minimale passe à 48 Go** (24 Go d'applicatif + jusqu'à 17 Go de poids de modèle) et que **512 Go de SSD** sont nécessaires dès le départ pour héberger plusieurs organisations.

---

## 2. À quoi servent les ressources

Quatre ressources : **vCPU**, **RAM**, **stockage SSD**, **GPU (VRAM)**. Elles servent principalement à :
- faire tourner les composants de la stack (serveur web, reverse proxy, base de données…) ;
- stocker de gros volumes de fichiers (surtout des PDF et des scripts de configuration) de plusieurs clients ;
- exécuter le **workflow multi-agents** (plusieurs modèles d'IA selon l'architecture d'évaluation) ;
- lire les PDF et en extraire le contenu, puis le nettoyer pour le modèle évaluateur ;
- faire évaluer les informations par le modèle évaluateur ;
- tenir les traitements longs sous forte charge (évaluer des centaines de contrôles).

---

## 3. Estimation par ressource

### 3.1 Stockage

Usages : poids des modèles d'IA (dizaines de Go), documents des clients (de **milliers à dizaines de milliers de pages PDF** et scripts), E/S rapides, sauvegardes, texte extrait des documents.

| Poste | Estimation |
|---|---|
| Système d'exploitation (Linux headless) | ~30 Go [R1] |
| Modèle 8 B en BF16 (non quantifié) | ~16 Go [R2] |
| Modèle 14 B en Q4_K_M | ~9 Go [R2][R4] |
| Modèle 27 B en Q4_K_M | ~17 Go [R3] |
| **Modèles (3–4 modèles)** | **> 50 Go** |
| Images Docker, builds CI… | dizaines de Go |
| Données clients | non estimable (aucun échantillon reçu) |

→ On pourrait tenir dans **256 Go**, mais pour héberger **plusieurs organisations** (une instance par organisation, modèle Silo) et leurs données confortablement : **512 Go dès le départ**.

### 3.2 vCPU

Le LLM consomme surtout du GPU ; les vCPU servent aux services de l'architecture [R6][R7] :

| Service | vCPU |
|---|---|
| Système (Ubuntu/RHEL + Docker/Podman) | 0,5 |
| Reverse proxy TLS (Nginx) | 0,2 |
| Application (Django + DRF) | 1–2 |
| Tâches asynchrones (workers Celery) | 1–2 |
| Base + vecteurs RAG (PostgreSQL + pgvector) | 1–2 |
| File de tâches, cache (Redis / Valkey) | 0,2 |
| Preuves, rapports (stockage objet SeaweedFS ou FS chiffré) | 0,5 |
| Antivirus (ClamAV) | 0,5 |
| Coffre de secrets (OpenBao) | 0,1 |
| Lecture PDF/DOCX (Docling) | 1 (pics) |
| Marge (build Docker, CI, pics) | 1 |
| **Total** | **≈ 8 minimum, ~12 confortable** |

### 3.3 RAM

Règle prudente : `RAM > max(taille(modèle_1), taille(modèle_2), …)` [R8][R9]. Le plus gros modèle visé (14–27 B) demande à lui seul **9 à 17 Go**.

| Service | RAM |
|---|---|
| Système (Ubuntu/RHEL + Docker/Podman) | 4 Go |
| Nginx | 0,1 Go |
| Django + DRF | 2 Go |
| Workers Celery | 3 Go |
| PostgreSQL + pgvector | 4 Go [R11] |
| Redis / Valkey | 0,5 Go |
| SeaweedFS ou FS chiffré | 1 Go |
| ClamAV (signatures en mémoire) | 2–3 Go [R10] |
| OpenBao | 0,25 Go |
| Docling | 2 Go [R7] |
| Marge (build Docker, CI, pics) | 4 Go [R12] |
| **Total hors modèles** | **≈ 24 Go** |

→ 24 Go d'applicatif + 17 Go de modèle ≈ **41 Go** → **48 Go** suffisent, pics compris ; **64 Go** en confortable.

---

## 4. GPU — benchmark et calcul de la VRAM

### 4.1 Démarche

Le GPU est le **goulot d'étranglement** dès le départ [R8]. Rahim a simulé une version simplifiée du workflow d'évaluation des contrôles : [github.com/MedjberAbderrahim/llm-benchmark](https://github.com/MedjberAbderrahim/llm-benchmark) (copie locale non versionnée dans `Benchmarking/llm-benchmark/`).

Conditions des runs finaux :
- données **synthétiques** générées par OpenAI Codex : 11 Mo, plus de 11 millions de caractères, en Markdown ;
- **8 contrôles PCI DSS** d'exemple ;
- 2 modes : **Full** (le modèle lit toutes les données, sans recherche) et **Retrieval** (RAG : recherche des passages par embeddings).

### 4.2 Résultats (tier M, 10 cas par run, exécution CPU)

| Run | Terminés | Exactitude (tous / labels fermes) | Citations valides | Latence médiane | Tokens de prompt | Heures CPU / 300 contrôles | Erreurs dangereuses |
|---|---|---|---|---|---|---|---|
| 7B, retrieval | 9 / 10 | 60 % / 62,5 % | 93,8 % | 218 s | 3 459 | 20,6 | 0 |
| 14B, retrieval | 9 / 10 | 70 % / 62,5 % | 100 % | 504 s | 2 687 | 45,1 | 0 |
| 7B, contexte complet | 9 / 10 | 70 % / 87,5 % | 78,9 % | 761 s | 9 947 | 55,5 | 0 |
| 14B, contexte complet | 1 / 10 | non significatif | n/a | 900 s (limite) | n/a | n/a | 0 |

Conclusions :
- **CPU seul = impraticable** : plus de 45 h pour 300 contrôles, alors que les vraies preuves seront bien plus lourdes que 11 Mo de données déjà propres.
- **Le RAG est obligatoire** : calcul moins cher (plus de temps pour d'autres campagnes) et **meilleures citations** (indispensables comme preuves dans le rapport final). Le mode « contexte complet » ne passe pas à l'échelle (le 14B n'a pas fini dans le temps imparti).
- La classe **~14 B** est la **base de travail** pour une évaluation fiable, avec deux axes d'amélioration : mieux préparer les données (workflow multi-agents) et un **meilleur modèle évaluateur** (fenêtre plus large, classe **~27 B**).

### 4.3 Fenêtre de contexte
Les vrais dossiers de preuves seront plus lourds : **8K minimum, 16K confortable**.

### 4.4 VRAM nécessaire [R5][R8]

| Taille du modèle / contexte | 8K | 16K |
|---|---|---|
| ~14 B (ex. Qwen2.5:14B) | 11,97 Go | 13,66 Go |
| ~27 B (ex. Gemma2:27B) | 22,58 Go | 25,83 Go |

→ **24 Go minimum** (14 B, 16K) · **32 Go confortable** (27 B, 16K).

---

## 5. Sécurité du serveur (sécurité by design dès l'infrastructure)
- Accès SSH **par clé uniquement**, port filtré par IP (MNA + binôme), pas de connexion root directe.
- Pare-feu hôte (ufw/nftables) : seuls 443 (et 80 → redirection) publics.
- TLS (Let's Encrypt ou certificat MNA), en-têtes de sécurité.
- **Disque chiffré** si le fournisseur le permet ; sauvegardes chiffrées hors du serveur.
- Mises à jour de sécurité automatiques (unattended-upgrades).
- Le conteneur IA n'a **aucun accès Internet** (réseau Docker interne) — démontrable au jury.
- Données **fictives uniquement** pendant le PFE (aucune donnée de client MNA).

## 6. Au-delà du PFE : dimensionnement par mode de déploiement (réunion 05/11, D11)

| Mode | Architecture cible | Ressources indicatives |
|---|---|---|
| **A — Service** (MNA audite ses clients) | 1 serveur applicatif + IA, hébergé chez MNA ou en environnement privé | Niveau « Confortable » : 12 vCPU, 64 Go RAM, GPU 32 Go, ≥ 512 Go SSD + sauvegarde externe |
| **B — SaaS** (licences, une instance dédiée par organisation — modèle Silo) | Une pile Docker par organisation (base, stockage, conteneurs propres) + **pool d'inférence GPU** partagé ou dédié (vLLM) | Applicatif ≈ 24 Go RAM / 8 vCPU par instance ; GPU dimensionné selon le nombre d'analyses IA simultanées (à mesurer) |

> Localisation : pour un usage réel (données bancaires), privilégier un **hébergement en Algérie** ou on-premise (loi 18-07 [S12], souveraineté). Pour le PFE, la localisation est secondaire (données fictives).

## 7. Questions à joindre à l'envoi
1. MNA dispose-t-elle déjà d'un serveur / poste avec GPU (24–32 Go de VRAM) utilisable pour l'IA ?
2. Hébergeur imposé ou préféré (en Algérie ?) ; budget indicatif ; niveau retenu (Minimum ou Confortable) ?
3. Accès d'administration pour le binôme (root / sudo) et VPN éventuel ?
4. Durée de mise à disposition (jusqu'à juin 2027) et devenir du serveur après le PFE ?
5. Un **échantillon anonymisé de preuves réelles** est-il possible ? (nécessaire pour estimer le stockage client et valider le benchmark sur des données réelles)

## Références (reprises du document Word)
- **[R1]** Ubuntu Server documentation, *System requirements (24.04 LTS)* — 25 Go de stockage et 3 Go de RAM minimum. https://ubuntu.com/server/docs/reference/installation/system-requirements/
- **[R2]** Ollama library, *Qwen3 tags* — Q4_K_M 5,2 / 9,3 / 20 Go et FP16 16 / 30 / 66 Go pour 8B / 14B / 32B. https://ollama.com/library/qwen3/tags
- **[R3]** Ollama library, *Gemma 3 tags* — 27B en Q4_K_M : 17 Go. https://ollama.com/library/gemma3/tags
- **[R4]** llama.cpp, *quantize README* — Q4_K_M ≈ 4,9 bits/poids contre 16 en F16. https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md
- **[R5]** LLM VRAM Calculator. https://llmvramcalculator.com/
- **[R6]** Celery documentation, *Workers guide* — concurrence par défaut = nombre de CPU. https://docs.celeryq.dev/en/stable/userguide/workers.html
- **[R7]** *Docling Technical Report* (arXiv:2501.17887) — ~3,1 s/page sur CPU 8 vCPU contre ~0,5 s sur GPU L4 ; l'OCR est le coût principal. https://arxiv.org/abs/2501.17887
- **[R8]** NVIDIA Technical Blog, *Mastering LLM Techniques: Inference Optimization* — décodage limité par la bande passante mémoire ; formule du cache KV. https://developer.nvidia.com/blog/mastering-llm-techniques-inference-optimization
- **[R9]** J. Tunney, chargement des poids LLM par mmap (avril 2023). https://justine.lol/mmap
- **[R10]** ClamAV documentation, *Docker installation, Memory requirements* — au moins 3 Gio recommandés. https://docs.clamav.net/manual/Installing/Docker.html
- **[R11]** pgvector README — index HNSW plus rapides quand ils tiennent en mémoire. https://github.com/pgvector/pgvector
- **[R12]** Docker Desktop system requirements (2026). https://usedocker.com/system-requirements
