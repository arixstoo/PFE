# Estimation des ressources — VPS / serveur du projet

> **Responsable : Rahim** · **À envoyer à MNA avant le 16/11/2026** (réunion du 05/11, décision D2).
> Statut : 🟡 brouillon chiffré — les valeurs sont des **estimations raisonnées** (méthode au § 3), à **confirmer par un benchmark** sur le matériel réel (tâche 3.5 de la roadmap).
> Dépend de : [Choix_Technologiques.md](Choix_Technologiques.md) (stack) · [Architecture_Technique.md](Architecture_Technique.md) (composants).

---

## 1. Résumé à envoyer à MNA (prêt à copier)

> Pour le développement et la démonstration de la plateforme (données **fictives** uniquement), nous avons besoin d'**un serveur Linux** (VPS ou serveur dédié) :
>
> | | **Minimum (sans GPU)** | **Recommandé** |
> |---|---|---|
> | CPU | 8 vCPU (x86-64, AVX2) | 16 vCPU |
> | RAM | 32 Go | 64 Go |
> | GPU | aucun | **1 GPU NVIDIA 24 Go de VRAM** (ex. L4, A10, RTX 4090/A5000) |
> | Stockage | 200 Go SSD NVMe | 500 Go SSD NVMe |
> | Système | Ubuntu Server 24.04 LTS, accès root, Docker | idem |
> | Réseau | 1 IPv4 publique, nom de domaine (ou sous-domaine MNA), ports 443 (+ 22 filtré par IP) | idem + snapshots/sauvegardes du fournisseur |
>
> **Pourquoi un GPU ?** L'analyse des preuves par **IA locale** (exigence du cahier des charges : aucune donnée envoyée à une IA publique) repose sur un modèle de langage hébergé sur le serveur. Sans GPU, seule une petite IA (≈ 8 milliards de paramètres) est utilisable et une analyse prend **quelques minutes** ; avec un GPU 24 Go, on peut utiliser des modèles plus fiables (14–32 milliards) et une analyse prend **de l'ordre de la minute**.
> **Alternative sans GPU sur le VPS :** un VPS « Minimum » + un **poste de travail équipé d'un GPU** chez MNA servant de serveur d'IA sur le réseau interne.
> Le laboratoire de tests (Active Directory, Windows, Linux) tourne sur **nos PC** (VMware) et n'a pas besoin du VPS.

---

## 2. Ce qui tourne sur le serveur

| Composant | Rôle | CPU | RAM | Disque |
|---|---|---|---|---|
| Ubuntu + Docker | Système | 0,5 | 2 Go | 30 Go |
| Nginx | Reverse proxy TLS | 0,2 | 0,1 Go | — |
| API Django (Gunicorn, 4 workers) | Application | 1–2 | 2 Go | — |
| Workers Celery (collectes, rapports, IA) | Tâches asynchrones | 1–2 | 3 Go | — |
| PostgreSQL + pgvector | Base + vecteurs RAG | 1–2 | 4 Go | 10 Go |
| Redis / Valkey | File de tâches, cache | 0,2 | 0,5 Go | — |
| Stockage objet (SeaweedFS) ou FS chiffré | Preuves, rapports | 0,5 | 1 Go | 20–50 Go (§ 3.3) |
| ClamAV (clamd) | Antivirus des uploads | 0,5 | 2–3 Go (signatures en mémoire) | 1 Go |
| OpenBao | Coffre de secrets | 0,1 | 0,25 Go | — |
| Docling (extraction) | Lecture PDF/DOCX | 1 (pics) | 2 Go | 2 Go |
| Embeddings BGE-M3 [A10] | Recherche de passages | 1 | 2,5 Go (CPU) | 2,5 Go |
| **LLM local** (Ollama / llama.cpp / vLLM) | Analyse des preuves | voir § 3.2 | voir § 3.2 | 15–50 Go (modèles) |
| Marge (build Docker, CI, pics) | — | 1 | 4 Go | 30 Go |
| **Total hors LLM** | | **≈ 8 vCPU** | **≈ 24 Go** | **≈ 130 Go** |

## 3. Méthode de calcul

### 3.1 Hypothèses d'usage (phase PFE)
- 2 développeurs + 2–3 utilisateurs de démo (encadrants MNA) ; < 10 utilisateurs simultanés.
- Données **fictives** : ~5 clients, ~20 campagnes de démonstration.
- IA : ~30–60 preuves de test pour le jeu d'évaluation + analyses de démo ; traitements **asynchrones** (pas de temps réel exigé, CDC § 15).

### 3.2 Mémoire de l'IA locale
Mémoire ≈ **poids du modèle** + **cache KV** (contexte) + ~15 % de marge.
- **Poids** (quantification 4 bits type Q4_K_M ≈ 0,57 octet / paramètre) : 8 B → ~4,6 Go · 14 B → ~8 Go · 24 B → ~14 Go · 32 B → ~18 Go.
- **Cache KV** = 2 × couches × têtes KV × dimension de tête × longueur de contexte × 2 octets (FP16). Exemple pour un modèle 8 B type Qwen3-8B [A9] (36 couches, 8 têtes KV, dimension 128) : ≈ 0,14 Mo par token → **~2,3 Go pour 16 000 tokens** de contexte. *(Valeurs d'architecture à vérifier dans la fiche du modèle choisi.)*

| Modèle (Q4) | Poids | + KV 16k + marge | Où ça tient |
|---|---|---|---|
| 8 B | ~4,6 Go | **~8 Go** | RAM CPU (lent) ou GPU ≥ 12 Go |
| 14 B | ~8 Go | **~12 Go** | GPU 16 Go |
| 24 B (Mistral Small) | ~14 Go | **~18 Go** | GPU 24 Go |
| 32 B / 30B-A3B (MoE) | ~18 Go | **~22–23 Go** | GPU 24 Go (juste) |

→ **GPU 24 Go** = permet de comparer toutes les tailles dans le benchmark du Lot 3 (exigence du mémoire : comparer 2–3 modèles).

### 3.3 Temps d'analyse (ordre de grandeur, à mesurer)
Une preuve ≈ 3–5 appels au LLM (~3 000 tokens en entrée + ~400 en sortie chacun).
- **CPU seul, modèle 8 B** : quelques tokens/s → **2 à 5 min par preuve** → acceptable pour une démo en asynchrone, insuffisant pour un usage réel.
- **GPU 24 Go, modèle 14–24 B** : plusieurs dizaines de tokens/s → **~30 s à 1 min 30 par preuve**.
- Une campagne réelle (~150 preuves analysées) : quelques heures GPU (traitement de nuit possible) contre plus d'une journée en CPU.
- Le débit en **requêtes concurrentes** dépend fortement du runtime : vLLM ≫ Ollama sous charge [A8], [G1] — important pour le mode SaaS.

### 3.4 Stockage
| Poste | Estimation |
|---|---|
| Système + images Docker | ~40 Go |
| Modèles IA (3–4 modèles testés + embeddings) | ~50 Go |
| Preuves : ~300–500 preuves × ~2 Mo ≈ 1 Go / campagne × 20 campagnes | ~20 Go |
| Base de données (dont vecteurs) | < 10 Go |
| Sauvegardes locales (2 générations) | ~60 Go |
| **Total** | **~180 Go → 200 Go minimum, 500 Go confortable** |

## 4. Sécurité du serveur (sécurité by design dès l'infrastructure)
- Accès SSH **par clé uniquement**, port filtré par IP (MNA + binôme), pas de connexion root directe.
- Pare-feu hôte (ufw/nftables) : seuls 443 (et 80 → redirection) publics.
- TLS (Let's Encrypt ou certificat MNA), en-têtes de sécurité.
- **Disque chiffré** si le fournisseur le permet ; sauvegardes chiffrées hors du serveur.
- Mises à jour de sécurité automatiques (unattended-upgrades).
- Le conteneur IA n'a **aucun accès Internet** (réseau Docker interne) — démontrable au jury.
- Données **fictives uniquement** pendant le PFE (aucune donnée de client MNA).

## 5. Au-delà du PFE : dimensionnement par mode de déploiement (réunion 05/11, D11)

| Mode | Architecture cible | Ressources indicatives |
|---|---|---|
| **A — Service** (MNA audite ses clients) | 1 serveur applicatif + IA, hébergé chez MNA ou en environnement privé | 16 vCPU, 64 Go RAM, 1 GPU 24 Go, 1 To NVMe, sauvegarde externe |
| **B — SaaS mutualisé** (licences, plusieurs organisations) | Couche applicative (≥ 2 nœuds derrière un répartiteur) + base PostgreSQL avec réplica + **pool d'inférence GPU** (vLLM) + stockage objet répliqué | Dimensionné selon le nombre d'organisations et d'analyses IA simultanées ; prévoir 1 GPU 24–48 Go par ~N analyses concurrentes (à mesurer) |
| **B' — SaaS dédié** (un client licencié exige sa propre instance) | Même paquet Docker que le mode A, déployé chez le client (« silo ») | Comme le mode A, adapté à la taille du client |

> Localisation : pour un usage réel (données bancaires), privilégier un **hébergement en Algérie** ou on-premise (loi 18-07 [S12], souveraineté). Pour le PFE, la localisation est secondaire (données fictives).

## 6. Questions à joindre à l'envoi
1. MNA dispose-t-elle déjà d'un serveur / poste avec GPU utilisable pour l'IA ?
2. Hébergeur imposé ou préféré (en Algérie ?) ; budget indicatif ?
3. Accès d'administration pour le binôme (root / sudo) et VPN éventuel ?
4. Durée de mise à disposition (jusqu'à juin 2027) et devenir du serveur après le PFE ?
