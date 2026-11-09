# Backlog produit — user stories priorisées

> Format : *En tant que [rôle], je veux [action] afin de [bénéfice].*
> Priorité **MoSCoW** : **M** = Must (MVP), **S** = Should, **C** = Could, **W** = Won't (cette fois).
> Estimation en points (1, 2, 3, 5, 8, 13). Sprint cible = voir [02_Plan_Roadmap.md](../02_Plan_Roadmap.md).

---

## EPIC 1 — Comptes, sécurité, administration (Lot 1, Sprint 1)
| ID | User story | Prio | Pts | Critères d'acceptation |
|---|---|---|---|---|
| US-101 | En tant qu'utilisateur, je veux me connecter avec un compte nominatif et un MFA TOTP afin de sécuriser l'accès | M | 5 | MFA obligatoire pour admin/resp./auditeur ; verrouillage après N échecs ; journalisé |
| US-102 | En tant que super admin, je veux créer des utilisateurs et leur attribuer un rôle afin de contrôler les accès | M | 3 | 5 rôles du CDC ; désactivation possible ; journalisé |
| US-103 | En tant que responsable de mission, je veux affecter des auditeurs à une campagne afin qu'ils n'accèdent qu'à leurs missions | M | 3 | Un auditeur non affecté reçoit 404 |
| US-104 | En tant que super admin, je veux consulter le journal d'audit filtrable afin d'assurer la traçabilité | M | 5 | Tous les événements CDC § 14.1 ; filtres date/utilisateur/client/action ; export CSV |
| US-105 | En tant que RSSI de MNA, je veux que le journal soit infalsifiable afin de garantir la non-répudiation | C | 5 | Chaînage de hash + commande de vérification |

## EPIC 1bis — Organisations & licences — mode SaaS (Lot 1, Sprint 1) — *ajouté le 09/11/2026 (réunion 05/11, D11)*
| ID | User story | Prio | Pts | Critères d'acceptation |
|---|---|---|---|---|
| US-151 | En tant qu'opérateur de la plateforme, je veux créer une organisation (prestataire d'audit ou entreprise licenciée) afin de servir plusieurs organisations sur la même plateforme | M | 5 | Organisation isolée ; premier admin d'organisation créé |
| US-152 | En tant qu'opérateur, je veux attribuer une licence (dates, nombre d'utilisateurs, modules PCI/GIE) à une organisation | M | 5 | Module non licencié = référentiel invisible ; licence expirée = lecture seule |
| US-153 | En tant qu'admin d'organisation, je veux gérer les utilisateurs et rôles de mon organisation | M | 3 | Aucun accès aux autres organisations |
| US-154 | En tant qu'utilisateur, je ne dois jamais voir ni deviner l'existence d'une autre organisation | M | 5 | Tests IDOR inter-organisations verts ; RLS active |
| US-155 | En tant qu'opérateur, je veux gérer les modules sans accéder au contenu des audits des organisations | S | 3 | Séparation des tâches vérifiée par test |
| US-156 | En tant qu'admin d'organisation, je veux brancher l'annuaire de mon entreprise (SSO OIDC) | C | 8 | Keycloak |
| US-157 | En tant qu'opérateur, je veux des quotas par organisation (stockage, analyses IA) | C | 3 | Limitation sans impact sur les autres |

## EPIC 2 — Clients / entités auditées (Lot 1, Sprint 1)
| ID | User story | Prio | Pts | Critères |
|---|---|---|---|---|
| US-201 | En tant que resp. de mission, je veux créer une fiche client (infos, contacts, périmètres, actifs) | M | 5 | Champs CDC § 5.1 |
| US-202 | En tant qu'utilisateur, je veux voir la liste des campagnes passées et en cours d'un client | M | 2 | Tri par date, statut |
| US-203 | En tant que contributeur client, je ne dois voir que les données de mon organisation | M | 5 | Tests IDOR automatisés verts |
| US-204 | En tant que resp. de mission, je veux stocker des documents généraux réutilisables du client | S | 3 | Réutilisation soumise à validation |

## EPIC 3 — Référentiels & versions (Lot 1, Sprint 1)
| ID | User story | Prio | Pts | Critères |
|---|---|---|---|---|
| US-301 | En tant que super admin, je veux importer un référentiel (YAML/JSON/Excel) afin d'ajouter PCI DSS, GIE ou un autre cadre sans code | M | 8 | Validation par JSON Schema ; rapport d'erreurs ; import idempotent |
| US-302 | En tant que super admin, je veux publier / déprécier une version afin de la rendre utilisable ou non | M | 3 | Version publiée immuable |
| US-303 | En tant qu'auditeur, je veux consulter le référentiel (domaines → contrôles, critères, preuves attendues) | M | 3 | Recherche par ID / mot-clé |
| US-304 | En tant que super admin, je veux créer une nouvelle version à partir d'une existante afin de gérer les évolutions | S | 5 | Copie en brouillon ; diff entre versions |
| US-305 | En tant que super admin, je veux exporter un référentiel | C | 2 | JSON/YAML |

## EPIC 4 — Campagnes (Lot 1, Sprint 2)
| ID | User story | Prio | Pts | Critères |
|---|---|---|---|---|
| US-401 | En tant que resp. de mission, je veux créer une campagne (client, nom, référentiel, version, dates, équipe, périmètre, actifs) | M | 5 | Version figée après lancement |
| US-402 | En tant que resp. de mission, je veux faire évoluer le statut de la campagne selon le cycle de vie | M | 5 | 8 statuts ; transitions autorisées seulement ; journalisé |
| US-403 | En tant que resp. de mission, je veux définir le périmètre et les contrôles applicables (règles + ajustement manuel), pour n'importe quel référentiel | M | 5 | Fonction du socle générique (réunion 05/11, D6) |
| US-404 | En tant que resp. de mission, je veux clôturer une campagne afin de figer les résultats | M | 3 | Lecture seule ; rapport final haché |
| US-405 | En tant que resp. de mission, je veux créer la campagne N+1 à partir de la N | S | 5 | Proposition de réutilisation des preuves et N/A (à revalider) |

## EPIC 5 — Évaluation (Lots 1–3)
| ID | User story | Prio | Pts | Critères |
|---|---|---|---|---|
| US-501 | En tant qu'auditeur, je veux naviguer Client → Campagne → Domaine → Contrôle → Preuve → Résultat | M | 5 | Fil d'Ariane ; avancement par domaine |
| US-502 | En tant qu'auditeur, je veux évaluer manuellement un contrôle via un formulaire structuré | M | 5 | Commentaire obligatoire si Partiel/NC ; constat + recommandation |
| US-503 | En tant qu'auditeur, je veux déclarer un N/A avec motif obligatoire | M | 3 | Évaluateur + date auto |
| US-504 | En tant que resp. de mission, je veux valider ou refuser les N/A | S | 3 | — |
| US-505 | En tant qu'auditeur, je veux voir l'historique des modifications d'un contrôle | M | 3 | Qui / quand / avant / après |
| US-506 | En tant qu'auditeur, je veux évaluer un contrôle à partir du résultat d'une **commande bien définie** et voir observé vs attendu | M | 5 | Lot 2 allégé (D9) ; statut proposé « À vérifier » |
| US-507 | En tant que contributeur client, je veux télécharger et exécuter le script de commandes fixes (lecture seule) puis déposer son résultat | M | 5 | Jeton à usage unique ; hash vérifié ; liste blanche de commandes |
| US-508 | En tant qu'auditeur, je veux valider en lot les résultats automatiques conformes | S | 3 | — |
| US-509 | En tant qu'auditeur, je veux obtenir une proposition IA sur une preuve : **statut (non conforme / partiel / conforme) + explication + justification**, avec citations et confiance | M | 13 | Lot 3 ; **pas de chatbot** (D10) ; passages surlignés ; confiance distincte du statut |
| US-510 | En tant qu'auditeur, je veux accepter / modifier / rejeter / demander une preuve complémentaire sur une proposition IA | M | 5 | Décision journalisée |
| US-511 | En tant qu'auditeur, je veux documenter un contrôle compensatoire | C | 3 | — |
| US-512 | En tant qu'auditeur, je veux évaluer un contrôle sur plusieurs actifs (échantillon) | C | 8 | Agrégation des résultats par actif |

## EPIC 6 — Preuves (Lot 1, Sprint 2)
| ID | User story | Prio | Pts | Critères |
|---|---|---|---|---|
| US-601 | En tant qu'utilisateur autorisé, je veux déposer une preuve et la lier à un ou plusieurs contrôles | M | 8 | Antivirus, hash SHA-256, métadonnées CDC § 11.2 |
| US-602 | En tant qu'auditeur, je veux envoyer une demande de preuve au client | M | 3 | Notification ; statut « En attente de preuve » |
| US-603 | En tant qu'auditeur, je veux changer le statut d'une preuve (validée, rejetée, obsolète) | M | 2 | — |
| US-604 | En tant qu'auditeur, je veux être alerté des preuves anciennes / obsolètes | S | 3 | Seuil paramétrable par type ; définition d'« obsolète » à confirmer avec MNA |
| US-605 | En tant qu'auditeur, je veux réutiliser une preuve d'une campagne précédente après revalidation | S | 3 | — |

## EPIC 7 — Écarts & remédiation (Lot 1, Sprint 3)
| ID | User story | Prio | Pts | Critères |
|---|---|---|---|---|
| US-701 | En tant qu'auditeur, je veux qu'un contrôle NC/Partiel génère une non-conformité avec criticité | M | 3 | — |
| US-702 | En tant que resp. de mission, je veux créer des actions correctives (responsable, échéance) | M | 3 | — |
| US-703 | En tant que contributeur client, je veux mettre à jour mes actions et joindre une preuve de correction | M | 3 | — |
| US-704 | En tant qu'auditeur, je veux réaliser un retest et clôturer la non-conformité | M | 3 | — |
| US-705 | En tant que lecteur, je veux voir les actions en retard | M | 2 | — |

## EPIC 8 — KPI & historique (Lot 1, Sprint 3)
| ID | User story | Prio | Pts | Critères |
|---|---|---|---|---|
| US-801 | En tant que lecteur/direction, je veux un tableau de bord avec les KPI du CDC § 12.2 | M | 8 | Tous les KPI listés |
| US-802 | En tant que direction, je veux comparer la campagne actuelle avec la précédente | M | 5 | Évolution par domaine et global |
| US-803 | En tant que direction, je veux une vue multi-clients consolidée | C | 5 | — |

## EPIC 9 — Reporting (Lot 1, Sprint 3)
| ID | User story | Prio | Pts | Critères |
|---|---|---|---|---|
| US-901 | En tant qu'auditeur, je veux générer le rapport détaillé en PDF (12 sections du CDC) | M | 8 | — |
| US-902 | En tant qu'auditeur, je veux le rapport en Word pour le personnaliser | M | 5 | Modèle MNA |
| US-903 | En tant qu'auditeur, je veux exporter résultats + plan d'action en Excel | M | 3 | — |
| US-904 | En tant qu'intégrateur, je veux exporter en CSV/JSON | S | 2 | — |
| US-905 | En tant qu'auditeur, je veux une synthèse exécutive pré-rédigée par l'IA locale | C | 5 | Éditable avant export |

## EPIC 10 — Mapping (Lot 4)
| ID | User story | Prio | Pts | Critères |
|---|---|---|---|---|
| US-1001 | En tant que super admin, je veux importer / éditer un ensemble de mappings versionné | M | 8 | 4 types ; justification obligatoire |
| US-1002 | En tant qu'auditeur, je veux calculer la couverture estimée de l'autre référentiel | M | 8 | Règles CDC § 8.1 ; tests T1–T11 |
| US-1003 | En tant que direction, je veux voir la couverture croisée (graphique + détail) avec la mention « estimée » | M | 5 | — |
| US-1004 | En tant qu'auditeur, je veux que le rapport contienne la section mapping | M | 3 | — |
| US-1005 | En tant qu'auditeur, je veux voir les preuves de la campagne PCI proposées pour les contrôles GIE mappés | S | 5 | — |
| US-1006 | En tant que super admin, je veux des suggestions de correspondances par similarité sémantique | C | 8 | Jamais publiées sans validation |

## Won't (perspectives, hors PFE)
- Agent résident en supervision continue · connecteur distant (LDAPS/WinRM/SSH) · connecteurs cloud (AWS/Azure) · application mobile · fine-tuning du LLM · export OSCAL complet · **chatbot / assistant conversationnel** (exclu par MNA, D10) · **facturation** des licences · import d'anciennes versions PCI DSS (D8).
