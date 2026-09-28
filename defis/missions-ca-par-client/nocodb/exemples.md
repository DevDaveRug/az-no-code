# Exemples anonymisés -- NocoDB (miroir Airtable)

Mêmes données que `../airtable/exemples.md` : 7 missions reliées aux 5 clients déjà présents dans `AZ_Clients` de la base unifiée.

## Correspondance clients (enregistrements existants)

| Id AZ_Clients | Nom | Entreprise (ajoutée) | Email |
|---|---|---|---|
| 1 | Alice Martin | Cabinet Legrand | alice.martin@legrand.example |
| 2 | Bob Durand | TechFlow SAS | bob.durand@techflow.example |
| 3 | Chloe Dubois | Studio Zenith | chloe.dubois@studio-zenith.example |
| 4 | Emma Petit | MarketPro | emma.petit@marketpro.example |
| 5 | Fabien Roux | Atelier Nord | fabien.roux@atelier-nord.example |

## AZ_Missions (7 lignes)

| Mission | Client (Id) | Statut | Montant | DateFin | Facturé | MontantFacturé | MontantResteAFacturer | MontantEnCours |
|---|---|---|---|---|---|---|---|---|
| Refonte site | 1 | Terminée | 2500 | 2026-03-12 | oui | 2500 | 0 | 0 |
| Création d'une app mobile | 2 | Terminée | 80000 | 2026-05-31 | oui | 80000 | 0 | 0 |
| Maintenance page | 1 | Arrêtée | 500 | 2026-06-30 | oui | 500 | 0 | 0 |
| Audit | 3 | En cours | 1200 | 2026-06-24 | non | 0 | 0 | 1200 |
| Maintenance | 1 | En cours | 450 | 2026-07-31 | non | 0 | 0 | 450 |
| Tunnel de vente | 4 | Terminée | 3200 | 2026-05-15 | non | 0 | 3200 | 0 |
| Atelier CRM | 5 | À faire | 900 | 2026-10-30 | non | 0 | 0 | 0 |

## Résultat attendu dans `AZ_Clients` (vue `CA par client`)

| Nom | NbMissions | CAFacturé | ResteAFacturer | MontantEnCours |
|---|---|---|---|---|
| Bob Durand | 1 | 80 000 € | 0 € | 0 € |
| Alice Martin | 3 | 3 000 € | 0 € | 450 € |
| Emma Petit | 1 | 0 € | 3 200 € | 0 € |
| Chloe Dubois | 1 | 0 € | 0 € | 1 200 € |
| Fabien Roux | 1 | 0 € | 0 € | 0 € |

Notes de migration : identiques à `../import-csv/missions.csv` (colonne `NotesMigration`).
