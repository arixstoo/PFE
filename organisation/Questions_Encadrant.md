# Questions aux encadrants MNA (Noor, Rabah)

> Suivi des questions posées et de leurs réponses. Chaque réponse est tracée dans un **compte rendu** ([Comptes_Rendus/](Comptes_Rendus/)).
> Contact : **e-mail direct ou téléphone**. Présence chez MNA : **chaque lundi**. Réunion d'avancement : **toutes les 2 semaines**.
> Dernière mise à jour : 09/11/2026 (après la réunion n°1 du 05/11/2026).

---

## ❓ À poser à la réunion n°2 (~19/11/2026)

### Restées ouvertes après la réunion n°1
- [ ] **CDC § 5.1** — « Documents généraux pouvant être réutilisés selon validation » : quels **exemples** de documents ?
- [ ] **CDC § 9** — Où sont les **étapes et le processus de remédiation** dans le diagramme de démarche d'une campagne ? (Proposition : boucle Remédiation → Retest avant clôture.)
- [ ] **CDC § 11.4** — Preuves « anciennes ou potentiellement obsolètes » : **obsolètes en quel sens** ? (fréquence dépassée, nouvelle version du document, changement de périmètre, ancienne campagne…)
- [ ] **CDC § 12.2** — Qu'est-ce que le **« taux de satisfaction interne »** ?
- [ ] **CDC § 14** — L'**antivirus** sera-t-il **fourni par MNA** ou utilise-t-on un **open source** (ClamAV) ?
- [ ] **CDC § 16.2** — **Maquettes UX/UI** : un fichier **Figma** ou un autre format ?

### Nouvelles (suite aux réponses du 05/11)
- [ ] Livrable 10 — Rapport de sécurité : vue **développeur** (sécurité by design, SAST/DAST, ASVS) ou **test d'intrusion** validé par MNA ? (à négocier, D12)
- [ ] **Mode SaaS** : hébergement mutualisé par MNA ou **instance dédiée** par client licencié ? Modèle de licence (par utilisateur, par module PCI/GIE, par durée) ? La gestion de la facturation est-elle dans le périmètre ? (proposition : non)
- [ ] **Mode SaaS** : dans un audit interne, qui joue les rôles « responsable de mission » / « auditeur » ? (l'équipe conformité de l'entreprise ?)
- [ ] **Assister à un audit** : quand, quel type d'audit, sous quelles conditions de confidentialité ? (D1)
- [ ] **Référentiel GIE** : quand le recevra-t-on, sous quel format, et conditions de confidentialité (dépôt Git, mémoire, assistant IA) ?
- [ ] Date de **fin de stage** exacte (début juin 2027 ?) et date de **soutenance** ?
- [ ] Réception de l'**estimation du VPS** (envoyée avant le 16/11) : délai d'acquisition ? GPU possible ?

## 🟠 Toujours à poser (liste initiale, non encore traitée)
- [ ] Existe-t-il chez MNA un **mapping PCI ↔ GIE** (même partiel) ou PCI ↔ ISO 27001 ?
- [ ] **Modèles réels anonymisés** : fichier Excel de suivi d'audit, rapport type, plan d'action type ?
- [ ] **Modèle de rapport Word / charte graphique MNA** ?
- [ ] Qui sera notre **référent métier** pour valider le mapping, les règles et la recette ? (Noor ou Rabah ?)
- [ ] **Criticité** des écarts : échelle (3 ou 4 niveaux) ? Calcul ?
- [ ] Pondération du statut **« Partiellement conforme »** dans le taux de conformité ?
- [ ] **Transitions** entre statuts de campagne (proposition dans [01_Sujet_Detaille.md](../01_Sujet_Detaille.md) § 5.2) ?
- [ ] Qui peut **rouvrir** une campagne clôturée ?
- [ ] **Contrôle compensatoire** et **approche personnalisée** PCI à gérer ?
- [ ] Le contributeur client voit-il les **résultats** et **rapports** ?
- [ ] **Systèmes cibles** prioritaires pour les commandes fixes (AD ? Linux ? autres ?)
- [ ] Langues : interface en **français** uniquement ? Rapports FR / EN ?
- [ ] Usage du **texte intégral PCI DSS** dans la plateforme couvert par la licence PCI SSC (surtout en mode SaaS commercial) ?
- [ ] Objectif de **disponibilité** (CDC § 15 « à définir ») ?
- [ ] Licence des dépendances : éviter l'**AGPL** dans un produit vendu ?

---

## ✅ Réponses obtenues

| Date | Question | Réponse | CR |
|---|---|---|---|
| 05/11/2026 | Assister à des audits ? | **Oui**, côté MNA (pas chez les clients) | [CR n°1](Comptes_Rendus/CR_2026-11-05_Reunion_lancement.md) D1 |
| 05/11/2026 | Acquisition du VPS ? | Plus tard ; **estimation des ressources** à fournir **avant le 16/11/2026** (Rahim) | D2 |
| 05/11/2026 | Jours de présence ? | **Lundi**, chaque semaine | D3 |
| 05/11/2026 | Moyen de communication ? | **E-mail direct ou téléphone** | D4 |
| 05/11/2026 | Stack imposée ? | **Libre**, laissée au binôme (Rostom), **avant le 16/11/2026** | D5 |
| 05/11/2026 | Pourquoi définir périmètre et contrôles (rôle du QSA) ? | Dépend de l'environnement et du référentiel ; fonction du **socle générique** avant le module PCI | D6 |
| 05/11/2026 | Propriété du code, partage avec l'USTHB ? | Seulement des **informations non sensibles** ; à rediscuter | D7 |
| 05/11/2026 | Anciennes versions PCI (3.2.1) ? | **Non** ; seulement le mapping GIE (basé sur **PCI DSS 3.2**) | D8 |
| 05/11/2026 | Importance de la méthode Agent/Script ? | **Commandes bien définies**, incluse mais pas prioritaire | D9 |
| 05/11/2026 | Sortie attendue de l'IA ? | **Statut** (non / partiel / conforme) + **explications et justifications** ; **pas de chatbot** | D10 |
| 05/11/2026 | Déploiement chez MNA ? | **Deux modes** : A. service (MNA audite ses clients) ; B. **SaaS sous licence** (audits internes d'entreprises) | D11 |
| 05/11/2026 | *(Rapport de sécurité — non posée)* | MNA insiste sur **bonnes pratiques** et **sécurité by design** ; niveau négociable | D12 |
| 05/11/2026 | Durée du stage ? | Début **09/11/2026**, fin vers **début juin 2027** (à confirmer) | — |
| 05/11/2026 | Fréquence des points ? | Réunion d'avancement **tous les 15 jours** + **CR** à chaque réunion | — |
| 05/11/2026 | *(Consignes)* | CDC = **brouillon flexible** ; estimation VPS + techno **URGENT** ; **références vers des articles** (pas Wikipédia) ; **étude de l'existant** = grande partie, y compris technologies, avant janvier | — |
