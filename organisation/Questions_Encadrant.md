# Questions à poser à l'encadrant MNA (réunion de lancement — semaine 1)

> Imprimez cette liste, cochez au fur et à mesure, et envoyez un **compte rendu écrit** après la réunion (ça vaut validation).

## 🔴 Bloquant (à obtenir en S1–S2)
- [ ] **Durée exacte du stage**, dates de début / fin, date prévisionnelle de soutenance, jours de présence sur site / télétravail ?
- [ ] **Référentiel GIE Monétique officiel** : quand, sous quel format (Excel idéal), quelle version ? Conditions de confidentialité (dépôt Git ? annexes du mémoire ? usage d'un assistant IA ?)
- [ ] Existe-t-il chez MNA un **mapping PCI ↔ GIE** (même partiel) ou PCI ↔ ISO 27001 ?
- [ ] **Matériel** : poste de travail, serveur de démo, **GPU** pour l'IA locale ? Sinon, quelle machine ?
- [ ] **Stack technique imposée** ou libre ? Contraintes de l'environnement MNA (Windows/Linux, Docker autorisé ?)
- [ ] **Hébergement cible** : on-premise chez MNA, chez les clients, les deux ?
- [ ] Validation du **périmètre MVP** proposé (voir [02_Plan_Roadmap.md](../02_Plan_Roadmap.md) § 1)

## 🟠 Important (S2–S4)
- [ ] Peut-on obtenir des **modèles réels anonymisés** : fichier Excel de suivi d'audit, rapport d'évaluation type, plan d'action type ? → base du module reporting
- [ ] Le **modèle de rapport Word / charte graphique MNA** à utiliser ?
- [ ] Qui sera notre **référent métier** (auditeur PCI/GIE) pour valider le mapping, les règles et la recette ? Créneau hebdomadaire possible ?
- [ ] Comment est défini le **« taux de satisfaction interne »** (KPI du CDC § 12.2) ?
- [ ] **Criticité** des écarts : quelle échelle (3 ou 4 niveaux) ? Calcul ?
- [ ] Pondération du statut **« Partiellement conforme »** dans le taux de conformité ?
- [ ] **Transitions** autorisées entre statuts de campagne (proposition dans le sujet détaillé § 5.2) ?
- [ ] Qui peut **rouvrir** une campagne clôturée (jamais ? super admin avec justification ?)
- [ ] Faut-il prendre en compte le **contrôle compensatoire** et l'**approche personnalisée** PCI ?
- [ ] Le contributeur client voit-il les **résultats** et **rapports**, ou seulement les demandes de preuves et ses actions ?
- [ ] Quels **systèmes cibles** sont prioritaires pour les collecteurs chez les clients (AD ? Linux ? FortiGate/Palo Alto ? QRadar/Splunk ? Oracle ?)
- [ ] Mode de collecte préféré : **script exécuté par le client** ou **connecteur distant** ?
- [ ] Langues : interface en **français** uniquement ? Rapports en français / anglais ?
- [ ] Usage du **texte intégral PCI DSS** dans la plateforme couvert par la licence PCI SSC ?

## 🟡 Utile
- [ ] Nombre de clients / campagnes simultanés visés (pour la scalabilité) ?
- [ ] Objectif de **disponibilité** (CDC § 15 « à définir ») ?
- [ ] SSO avec l'annuaire MNA souhaité (Keycloak/AD) ?
- [ ] Outil de gestion de projet / dépôt Git imposé (GitLab interne ?) ?
- [ ] Fréquence des points d'avancement souhaitée ? Format (démo, rapport écrit) ?
- [ ] Le code reste-t-il propriété de MNA ? Peut-on montrer le code / la démo au jury ? Licence des dépendances (ex. éviter l'AGPL) ?
- [ ] Possibilité d'**assister à une vraie mission d'audit** (même en observateur) pour comprendre le métier ?

## Compte rendu type (à envoyer après chaque réunion)
```
Objet : [PFE PCI/GIE] CR réunion du JJ/MM/AAAA

Participants : …
Décisions :
  1. …
Actions :
  | Action | Responsable | Échéance |
  | …      | …           | …        |
Points ouverts : …
Prochaine réunion : …
```
