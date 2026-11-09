# Compte rendu — Réunion n°1 (lancement) — 05/11/2026

| | |
|---|---|
| **Objet** | [PFE PCI/GIE] Réunion de lancement avec MNA |
| **Date** | Jeudi 05/11/2026 |
| **Participants MNA** | Noor, Rabah (encadrants) |
| **Binôme** | Rostom, Rahim |
| **Rédigé par** | Binôme — à envoyer par e-mail aux encadrants pour validation |
| **Prochaine réunion d'avancement** | ~19/11/2026 (toutes les 2 semaines — date exacte à confirmer) |

> ℹ️ **Note générale de MNA :** le cahier des charges (`PCI_DSS_PFE_CDC_V12102026.docx`) est un **brouillon**. Il est **flexible** et pourra évoluer pour coller aux besoins métier. Toute évolution sera tracée dans ces comptes rendus.

---

## 1. Décisions et réponses obtenues

| # | Question posée | Réponse MNA | Impact sur le projet |
|---|---|---|---|
| D1 | Pouvons-nous **assister à des audits** PCI DSS (ou similaires) pour comprendre le point de vue d'un auditeur ? | **Oui**, côté MNA uniquement (pas chez leurs clients). | Ajout d'une tâche « observation d'audits » dans l'étude de l'existant. Préparer une grille d'observation. |
| D2 | Statut de l'acquisition du **VPS** pour le projet ? | **À faire plus tard.** Le binôme doit fournir une **estimation des ressources nécessaires** (CPU / GPU / RAM / serveurs…) **avant le 16/11/2026**. Tâche assignée à **Rahim**. | Nouveau document : [Estimation_Ressources_VPS.md](../../conception/Estimation_Ressources_VPS.md). |
| D3 | Organisation des jours de présence ? | **Lundi** = jour de présence chez MNA, chaque semaine. | Rituel hebdomadaire mis à jour. |
| D4 | Moyen de communication avec les encadrants ? | **E-mail direct ou appel téléphonique.** | Pas de Discord/WhatsApp avec MNA. |
| D5 | Stack technologique ? | **Laissée au binôme** (pas forcément à discuter avec MNA). Responsable : **Rostom**, à finaliser **avant le 16/11/2026**. | Nouveau document : [Choix_Technologiques.md](../../conception/Choix_Technologiques.md). |
| D6 | CDC § 5.2 « Périmètre et contrôles en cours de définition » : un QSA ne fait que valider le CDE/les contrôles, pourquoi les définir nous-mêmes ? | Cela **dépend de l'environnement et du référentiel choisi**. C'est surtout une fonction **du socle de la plateforme et des audits génériques**, avant d'être une fonction du module PCI DSS. | La plateforme a un **socle d'audit générique** (périmètre, contrôles, applicabilité) ; PCI DSS et GIE sont des **modules** au-dessus. |
| D7 | Propriété du code source, partage avec l'USTHB ? | On ne met que des **informations non sensibles** (dépôt, mémoire). **À rediscuter plus tard.** | Règle : aucune donnée sensible MNA/client dans Git ni dans le mémoire. |
| D8 | CDC § 6.2 : faut-il aussi gérer d'anciennes versions (ex. PCI DSS 3.2.1) ? | **Non.** Seulement le mapping avec le **GIE Monétique**, qui est **basé sur PCI DSS 3.2**. | Pas d'import de PCI DSS 3.2.1. Le mapping GIE ↔ PCI DSS v4.0.1 devra tenir compte des changements 3.2 → 4.0 (renumérotation). |
| D9 | CDC § 10 Méthode Agent/Script : quelle importance, sachant que MNA ne pourra pas beaucoup l'utiliser ? | Des **commandes bien définies** ; **ne pas trop se focaliser** dessus, mais **l'inclure**. | Lot 2 **allégé** : bibliothèque de commandes fixes en lecture seule + moteur de règles. Temps récupéré pour l'étude de l'existant et le mode SaaS. |
| D10 | CDC § 11.3 : si l'IA n'est qu'une « assistance », quelle sortie attendue ? | Essentiellement un **statut de conformité** (non conforme / partiel / conforme) **avec explications et justifications**. **Pas un chatbot.** | Sortie IA = JSON structuré (statut + justification + citations + confiance). Aucune interface conversationnelle. |
| D11 | CDC § 15 « on-premise ou environnement privé » : la plateforme sera-t-elle déployée chez MNA ? | **Deux modes :** **A.** *en service* — MNA audite ses clients avec la plateforme ; **B.** *SaaS* — la plateforme est **vendue sous licence** à des entreprises pour leurs **audits internes**. | Impact fort : multi-tenant à deux niveaux (organisation licenciée → entités auditées), gestion des licences et modules, isolation renforcée. |
| D12 | CDC § 16.2 « Rapport de sécurité avant mise en production » : point de vue développeur ou pentest ? | (Question non posée.) MNA insiste sur les **bonnes pratiques de sécurité** et la **sécurité by design**. Le niveau du rapport **pourra être négocié plus tard**. | Sécurité by design dès la conception ; périmètre du rapport à confirmer. |

## 2. Consignes de MNA (notes)

| # | Consigne | Priorité |
|---|---|---|
| C1 | Faire l'**estimation des ressources** (VPS) et le **choix technologique** | 🔴 **URGENT** — avant le 16/11/2026 |
| C2 | Mettre des **références** dans les documents, vers des **articles** (et pas Wikipédia) | 🔴 **IMPORTANT** — voir [Bibliographie.md](../../connaissances/Bibliographie.md) |
| C3 | L'**étude de l'existant** doit être une **grande partie** du travail, **y compris sur les technologies**. Indication : 4–5 semaines, **avant janvier** (peut être plus courte si on avance vite) | 🟠 Important |
| C4 | Préparer un **compte rendu à chaque réunion** ; une réunion d'avancement **tous les 15 jours** | 🟠 Rituel |

## 3. Actions

| Action | Responsable | Échéance | Statut |
|---|---|---|---|
| Estimation des ressources du VPS (CPU/GPU/RAM/stockage/serveurs), envoi à MNA | Rahim | 16/11/2026 | 🟡 Brouillon prêt : [Estimation_Ressources_VPS.md](../../conception/Estimation_Ressources_VPS.md) |
| Choix de la stack technique (comparatif + décision) | Rostom | 16/11/2026 | 🟡 Brouillon prêt : [Choix_Technologiques.md](../../conception/Choix_Technologiques.md) |
| Démarrer l'étude de l'existant (outils, technologies, processus d'audit MNA) | Rostom + Rahim | 13/12/2026 au plus tard | ⬜ |
| Demander un créneau pour **assister à un audit** côté MNA | Rostom + Rahim | Lundi 16/11/2026 | ⬜ |
| Compléter la bibliographie (articles, rapports officiels) | Rostom + Rahim | Continu | 🟡 Amorcée |
| Poser les questions restantes (§ 4) | Rostom + Rahim | Réunion n°2 | ⬜ |
| Envoyer ce compte rendu par e-mail à Noor et Rabah | Rostom | 10/11/2026 | ⬜ |

## 4. Questions ouvertes (pour la réunion n°2)

1. **CDC § 5.1** — « Documents généraux pouvant être réutilisés selon validation » : quels exemples de documents ? (PSSI, organigramme, schéma réseau, inventaire des actifs… ?)
2. **CDC § 9** — Où se situent les **étapes et le processus de remédiation** dans le diagramme de démarche d'une campagne ?
3. **CDC § 11.4** — Preuves « anciennes ou potentiellement obsolètes » : obsolètes **en quel sens** ? (date dépassée selon la fréquence exigée, nouvelle version du document, changement de périmètre, preuve d'une ancienne campagne… ?)
4. **CDC § 12.2** — Qu'est-ce que le **« taux de satisfaction interne »** ?
5. **CDC § 14** — L'**antivirus** des fichiers sera-t-il **fourni par MNA** ou utilise-t-on un antivirus **open source** (ex. ClamAV) ?
6. **CDC § 16.2** — **Maquettes UX/UI** : un fichier **Figma** est-il attendu, ou un autre format ?
7. *(Nouvelle)* Rapport de sécurité avant production : rapport « développeur » (sécurité by design + tests SAST/DAST internes) ou **test d'intrusion** validé par une équipe MNA ?
8. *(Nouvelle, suite D11)* Mode SaaS : hébergement par MNA (multi-tenant mutualisé) ou instance dédiée par client licencié ? Modèle de licence (par utilisateur, par module PCI/GIE, par durée) ?

---

*E-mail d'envoi type :*
```
Objet : [PFE PCI/GIE] CR réunion du 05/11/2026

Bonjour Noor, bonjour Rabah,

Veuillez trouver ci-dessous le compte rendu de notre réunion du 05/11/2026
(décisions, actions et questions ouvertes). N'hésitez pas à nous signaler
toute correction.

Nous vous transmettrons l'estimation des ressources du VPS et le choix de la
stack technique avant le 16/11/2026.

Cordialement,
Rostom et Rahim
```
