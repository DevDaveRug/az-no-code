# Exemples anonymisés -- AZ_Missions (+ AZ_Clients existante)

Données fictives. Clients = les 5 enregistrements déjà présents dans `AZ_Clients` (projet 4), emails en `.example` (RFC 2606), téléphones `+33 6 00 00 00 XX`.

## Avant : la base « en vrille » (1 table, 7 colonnes en texte)

Fichier : `../import-csv/missions-avant.csv`. Reproduit tous les défauts du brief : client retapé avec des casses différentes, e-mail recopié, montants en 6 formats, statuts libres, dates incomplètes, « Facturé » en texte libre.

| Mission | Client | Email client | Statut | Montant | Date de fin | Facturé |
|---|---|---|---|---|---|---|
| Refonte site | Alice martin | alice.martin@legrand.example | terminé | 2500 € | 12/03/2026 | oui |
| Création d'une app mobile | TechFlow SAS | bob.durand@techflow.example | fini | 80k€ | mai 2026 | OUI |
| Maintenance page | Alice Martin | alice.martin@legrand.example | arrêté | 500 EUR | juin | x |
| Audit | Chloé de Studio Zenith | chloe.dubois@studio-zenith.example | en cours | 1200 € | 24/06/2026 | |
| Maintenance | Alice Martin | alice.martin@legrand.example | EN COURS | 450 | Juillet | non |
| Tunnel de vente | Emma Petit | emma.petit@marketpro.example | Terminé | 3 200,00 € | 15/05/2026 | non |
| Atelier CRM | fabien roux | fabien.roux@atelier-nord.example | à faire | 900€ | 30/10/2026 | |

## Après : `AZ_Missions` (7 lignes)

Fichier : `../import-csv/missions.csv`.

| Mission | Client (lien) | Statut | Montant | DateFin | Facturé | NotesMigration |
|---|---|---|---|---|---|---|
| Refonte site | Alice Martin | Terminée | 2 500,00 € | 12/03/2026 | oui | |
| Création d'une app mobile | Bob Durand | Terminée | 80 000,00 € | 31/05/2026 | oui | date déduite (mai 2026), à confirmer |
| Maintenance page | Alice Martin | Arrêtée | 500,00 € | 30/06/2026 | oui | date déduite, « x » lu comme oui, doublon possible |
| Audit | Chloe Dubois | En cours | 1 200,00 € | 24/06/2026 | non | vide lu comme non |
| Maintenance | Alice Martin | En cours | 450,00 € | 31/07/2026 | non | date déduite, doublon possible |
| Tunnel de vente | Emma Petit | Terminée | 3 200,00 € | 15/05/2026 | non | |
| Atelier CRM | Fabien Roux | À faire | 900,00 € | 30/10/2026 | non | vide lu comme non |

## Champs ajoutés à `AZ_Clients`

| Nom | Entreprise |
|---|---|
| Alice Martin | Cabinet Legrand |
| Bob Durand | TechFlow SAS |
| Chloe Dubois | Studio Zenith |
| Emma Petit | MarketPro |
| Fabien Roux | Atelier Nord |

## Ce que les exemples démontrent

-> « TechFlow SAS » (entreprise) devient le client Bob Durand, entreprise TechFlow SAS : le rapprochement se fait par l'e-mail, pas par le nom tapé

-> Alice Martin a 3 missions : une terminée facturée, une arrêtée facturée, une en cours. Son CA facturé (3 000 €) n'était calculable nulle part avant

-> Emma Petit a une mission terminée non facturée : elle remonte seule dans la vue `À facturer` (3 200 €)

-> 5 lignes sur 7 ont une note de migration : la vue `À vérifier (migration)` rend visibles les décisions prises pendant le nettoyage, au lieu de les cacher

-> Vue `Par statut` : À faire (1) / En cours (2) / Terminée (3) / Arrêtée (1)
