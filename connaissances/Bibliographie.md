# Bibliographie du projet

> **Consigne MNA (05/11/2026) — IMPORTANT :** toutes les affirmations importantes des documents et du mémoire doivent renvoyer à des **références vérifiables** : **articles scientifiques**, **normes et publications officielles**, **documentation technique officielle**. **Pas de Wikipédia.**
>
> **Comment citer :** dans les fichiers `.md`, on écrit la clé entre crochets, ex. « RAG [A1] ». Dans le mémoire, ces clés deviennent des entrées Zotero (style IEEE recommandé pour un mémoire d'ingénieur).
>
> **Niveaux de fiabilité :**
> 🟢 article évalué par les pairs (conférence / revue) ou norme officielle · 🟡 prépublication arXiv, mémoire, rapport technique · 🟠 littérature grise (blog technique d'éditeur, presse spécialisée) — à n'utiliser que pour des faits techniques récents, jamais comme seul appui d'un argument scientifique.
>
> ⚠️ Toutes les références ci-dessous ont été **retrouvées et vérifiées en ligne le 09/11/2026** (titre, auteurs, identifiant). Avant de citer un chiffre dans le mémoire, **lire la source elle-même**.

---

## 1. Normes, standards et publications officielles

| Clé | Référence | Niv. | Utilisé pour |
|---|---|---|---|
| **[S1]** | PCI Security Standards Council, *Payment Card Industry Data Security Standard — Requirements and Testing Procedures, v4.0.1*, juin 2024. Document Library : https://www.pcisecuritystandards.org/document_library/ | 🟢 | Référentiel principal, modélisation des contrôles |
| **[S2]** | PCI SSC, *PCI DSS — Summary of Changes from PCI DSS Version 3.2.1 to 4.0*, mars 2022 (même Document Library). | 🟢 | **Clé pour le mapping GIE** : le GIE est basé sur PCI DSS 3.2 (réunion du 05/11) → ce document donne la correspondance de numérotation 3.2.1 → 4.0 |
| **[S3]** | PCI SSC, *PCI DSS Prioritized Approach for v4.0.1* (tableur), Document Library. | 🟢 | Import du référentiel, criticité par défaut |
| **[S4]** | PCI SSC, *ROC Template for PCI DSS v4.0.1* et *ROC Reporting Instructions*, Document Library. | 🟢 | Statuts d'évaluation (In Place / Not in Place…), structure de rapport |
| **[S5]** | NIST, *IR 8278 Rev. 1 — National Online Informative References (OLIR) Program: Program Overview and Integration Guidelines*, février 2024. https://csrc.nist.gov/pubs/ir/8278/r1/final | 🟢 | Justification des types de relation de mapping |
| **[S6]** | NIST, *IR 8278A Rev. 1 — OLIR Program: Submission Guidance for OLIR Developers*, 2024. https://csrc.nist.gov/pubs/ir/8278/a/r1/final | 🟢 | Méthode de construction d'un mapping |
| **[S7]** | NIST, *Open Security Controls Assessment Language (OSCAL)*, documentation officielle. https://pages.nist.gov/OSCAL/ | 🟢 | Format d'import des référentiels, export des résultats |
| **[S8]** | ISO/IEC 27001:2022 — *Information security management systems — Requirements* ; ISO/IEC 27002:2022 — *Information security controls*. | 🟢 | GIE inspiré d'ISO 27000 ; exigence 12 PCI |
| **[S9]** | ISO 19011:2018 — *Guidelines for auditing management systems*. | 🟢 | **Démarche d'audit** (programme, preuves, constats) → « penser comme un auditeur » |
| **[S10]** | OWASP, *Application Security Verification Standard (ASVS) 5.0.0*, mai 2025. https://owasp.org/www-project-application-security-verification-standard/ | 🟢 | Exigences et tests de sécurité de la plateforme, rapport de sécurité |
| **[S11]** | OWASP GenAI Security Project, *OWASP Top 10 for LLM Applications* (édition 2025 ; vérifier s'il existe une édition plus récente). https://genai.owasp.org/llm-top-10/ | 🟢 | Risques de l'IA locale (injection de prompt, fuite de données…) |
| **[S12]** | Loi n° 18-07 du 10 juin 2018 relative à la protection des personnes physiques dans le traitement des données à caractère personnel (JORADP), modifiée par la loi n° 25-11 du 24 juillet 2025. https://www.joradp.dz | 🟢 | Justification IA locale / on-premise |

## 2. Articles scientifiques

### 2.1 IA, LLM et RAG appliqués à la conformité
| Clé | Référence | Niv. | Ce qu'on en retient pour le projet |
|---|---|---|---|
| **[A1]** | P. Lewis, E. Perez, A. Piktus *et al.*, « Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks », *NeurIPS 2020*. arXiv:2005.11401 | 🟢 | Article fondateur du RAG : base théorique du pipeline « preuve → passages → LLM » |
| **[A2]** | M. Roman, K. Rafferty, B. Devereux, « CTRAG: An In-Context Retrieval-based Framework for Automated Compliance Checking using LLMs », arXiv:2608.02472, août 2026. https://arxiv.org/abs/2608.02472 | 🟡 | **Très proche de notre Méthode 2** : questions de contrôle extraites du référentiel, confrontées aux documents de l'entreprise ; déployé dans un cabinet « Big Four » ; F1 ≈ 78 %, rappel ≈ 85 % → ordre de grandeur réaliste, et preuve que la validation humaine reste nécessaire |
| **[A3]** | « RAGulating Compliance: A Multi-Agent Knowledge Graph for Regulatory QA », arXiv:2508.09893, 2025. | 🟡 | Graphe de connaissances + RAG pour la réglementation (piste d'amélioration, hors MVP) |
| **[A4]** | « PrivComp-KG: Leveraging Knowledge Graph and Large Language Models for Privacy Policy Compliance Verification », arXiv:2404.19744, 2024. | 🟡 | Vérification d'une politique (document) contre une réglementation : analogue à l'analyse de politiques PCI 12.x |
| **[A5]** | *Evaluating Retrieval-Augmented Generation for Automated Regulatory Gap Analysis in the Information Security domain*, mémoire de master, Chalmers University of Technology. https://odr.chalmers.se/items/66fa07cb-6a17-4d1e-9b22-1b88af22cd6d | 🟡 | Analyse d'écarts ISO 27001 / NIS2 par RAG : **protocole d'évaluation** réutilisable pour notre jeu de test IA |

### 2.2 Fiabilité et sécurité des LLM
| Clé | Référence | Niv. | Ce qu'on en retient |
|---|---|---|---|
| **[A6]** | K. Greshake, S. Abdelnabi, S. Mishra, C. Endres, T. Holz, M. Fritz, « Not what you've signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection », *ACM AISec 2023*. arXiv:2302.12173 | 🟢 | **Injection de prompt indirecte** : une preuve déposée peut contenir des instructions malveillantes → garde-fous et tests adverses obligatoires |
| **[A7]** | L. Huang, W. Yu *et al.*, « A Survey on Hallucination in Large Language Models: Principles, Taxonomy, Challenges, and Open Questions », arXiv:2311.05232. | 🟡 | Taxonomie des hallucinations (factualité / fidélité) → justifie les **citations vérifiées** et le score de confiance |

### 2.3 Modèles et outils d'IA locale
| Clé | Référence | Niv. | Ce qu'on en retient |
|---|---|---|---|
| **[A8]** | W. Kwon, Z. Li, S. Zhuang *et al.*, « Efficient Memory Management for Large Language Model Serving with PagedAttention », *ACM SOSP 2023*. arXiv:2309.06180 | 🟢 | Fondement de **vLLM** : débit ×2–4 vs l'état de l'art ; pertinent pour le mode SaaS (requêtes concurrentes) |
| **[A9]** | Qwen Team, « Qwen3 Technical Report », arXiv:2505.09388, mai 2025. | 🟡 | Famille de modèles candidats (0,6 B → 235 B, dense + MoE, 119 langues, licence Apache 2.0) |
| **[A10]** | J. Chen, S. Xiao, P. Zhang, K. Luo, D. Lian, Z. Liu, « BGE M3-Embedding: Multi-Lingual, Multi-Functionality, Multi-Granularity Text Embeddings Through Self-Knowledge Distillation », arXiv:2402.03216, 2024. | 🟡 | Modèle d'embeddings multilingue (FR/EN/AR) pour la recherche de passages |
| **[A11]** | C. Auer *et al.*, « Docling Technical Report », arXiv:2408.09869, 2024. | 🟡 | Extraction de documents PDF/DOCX en local (licence MIT) avec structure (pages, tableaux) |

### 2.4 Conformité, PCI DSS, GRC et architecture SaaS
| Clé | Référence | Niv. | Ce qu'on en retient |
|---|---|---|---|
| **[A12]** | S. Park, J. D. Hastings, « Weak Enforcement and Low Compliance in PCI DSS: A Comparative Security Study », *IEEE ISDFS 2026*. arXiv:2512.13430 | 🟢 | Seules **32,4 %** des organisations étaient pleinement conformes PCI DSS en 2022 (selon les auteurs) → justifie le besoin d'outils de pilotage et de suivi |
| **[A13]** | T. Angermeir, J. Fischbach, F. Moyón, D. Mendez, « Towards Automated Continuous Security Compliance », *ACM/IEEE ESEM 2024*. DOI 10.1145/3674805.3690748 | 🟢 | Définition de la conformité continue, défis, feuille de route → cadre théorique du Lot 2 (automatisation) |
| **[A14]** | C.-P. Bezemer, A. Zaidman, « Multi-tenant SaaS applications: maintenance dream or nightmare? », *IWPSE-EVOL 2010*, ACM, pp. 88–92. DOI 10.1145/1862372.1862393 | 🟢 | Avantages / risques du multi-tenant : la multi-location doit être traitée **dans toutes les couches** → mode SaaS (réunion 05/11) |

## 3. Documentation technique officielle (choix technologiques)

| Clé | Référence | Utilisé pour |
|---|---|---|
| **[D1]** | PostgreSQL Global Development Group, *Row Security Policies*. https://www.postgresql.org/docs/current/ddl-rowsecurity.html | Isolation multi-tenant en base |
| **[D2]** | AWS, *SaaS Tenant Isolation Strategies* (livre blanc). https://docs.aws.amazon.com/whitepapers/latest/saas-tenant-isolation-strategies/saas-tenant-isolation-strategies.html | Modèles silo / pool / bridge pour le mode SaaS |
| **[D3]** | Microsoft, *Architect multitenant solutions on Azure* (guide d'architecture). https://learn.microsoft.com/en-us/azure/architecture/guide/multitenant/overview | Modèles de multi-location, licences, isolation |
| **[D4]** | Django Software Foundation, *Security in Django*. https://docs.djangoproject.com/en/stable/topics/security/ | Protections natives (CSRF, XSS, SQLi, clickjacking) |
| **[D5]** | Cisco Talos, *ClamAV Documentation*. https://docs.clamav.net/ | Antivirus des preuves (en attendant la réponse MNA) |
| **[D6]** | OpenBao (Linux Foundation), documentation. https://openbao.org/docs/ | Coffre de secrets open source (fork de Vault, licence MPL 2.0) |
| **[D7]** | ggml-org, *llama.cpp*. https://github.com/ggml-org/llama.cpp ; Ollama, https://github.com/ollama/ollama ; vLLM, https://docs.vllm.ai/ | Runtimes LLM locaux |
| **[D8]** | intuitem, *CISO Assistant Community*. https://github.com/intuitem/ciso-assistant-community | Étude de l'existant (outil GRC open source le plus proche) |
| **[D9]** | Artifex, *PyMuPDF — License and Copyright* (AGPL-3.0 / commerciale). https://pymupdf.readthedocs.io/ | ⚠️ Contrainte de licence pour un produit vendu en SaaS |

## 4. Littérature grise (à utiliser avec prudence)

| Clé | Référence | Niv. | Pourquoi on la garde |
|---|---|---|---|
| **[G1]** | H. Umesh, « Ollama vs. vLLM: A deep dive into performance benchmarking », Red Hat Developer, 08/08/2025. https://developers.redhat.com/articles/2025/08/08/ollama-vs-vllm-deep-dive-performance-benchmarking | 🟠 | Benchmark reproductible (A100 40 Go, Llama 3.1 8B) : pic vLLM 793 tokens/s vs Ollama 41 tokens/s en charge concurrente |
| **[G2]** | InfoQ, « MinIO in maintenance mode — S3 API alternatives », décembre 2025. https://infoq.com/news/2025/12/minio-s3-api-alternatives/ | 🟠 | MinIO Community Edition en mode maintenance → impact sur le choix du stockage objet |
| **[G3]** | Indjazat (presse économique algérienne), interviews de responsables du GIE Monétique, 2022. | 🟠 | Seules infos publiques sur le référentiel GIE (> 350 exigences, inspiré PCI DSS / ISO 27000) |

---

## 5. À chercher (étude de l'existant, S1–S5)

- [ ] Articles sur l'**évaluation de la conformité assistée par outil** dans le secteur bancaire (IEEE Xplore, ACM DL, ScienceDirect : « compliance management system », « GRC tool evaluation »).
- [ ] Études sur l'**audit assisté par l'IA** (revues d'audit/comptabilité : *International Journal of Accounting Information Systems*, *Journal of Information Systems*).
- [ ] Travaux sur la **génération automatique de mappings** entre référentiels (embeddings, LLM) → pour le bonus Lot 4.
- [ ] Publications algériennes (ASJP — https://www.asjp.cerist.dz) sur la monétique, le CIB, la cybersécurité bancaire.
- [ ] Benchmarks **académiques** d'inférence LLM sur CPU / petits GPU (pour remplacer [G1] dans le mémoire).

**Outils :** Google Scholar, IEEE Xplore, ACM Digital Library, arXiv, Semantic Scholar, ASJP. Stocker tout dans un **groupe Zotero partagé** (Rostom + Rahim).
