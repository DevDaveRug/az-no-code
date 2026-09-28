# NocoDB -- Missions et CA par client (self-host)

Équivalent souverain de `../airtable/SPECS.md`, dans la base unifiée NocoDB.

## Cible

-> Instance : `https://sb-nocodb.coolify.salescloser.fr/` (NocoDB `2026.09.0`, plan Free)

-> Base : `Sales Closer Souverain` (`phwalskbrftv4o4`), lien public `https://sb-nocodb.coolify.salescloser.fr/#/base/22ca37d4-9d7a-4cf2-86ed-6e569babae1d`

-> Tables existantes réutilisées : `AZ_Clients` (`my2gc6wwrlaqlh2`, 5 clients fictifs du projet 4)

-> Table créée : `AZ_Missions`

Commandes API prêtes (identifiants réels) : `API.md`.

## Limites du plan Free constatées (lecture de la méta de la base, 28/09/2026)

-> `limit_dashboard: 0` : **pas de Dashboard NocoDB** sur cette instance. La spec d'interface passe par une vue partagée + une page Interface (voir `INTERFACE.md`)

-> `limit_interface: 2`, `limit_interface_page: 2` : 2 Interfaces de 2 pages maximum pour tout l'espace de travail

-> `feature_rollup_limit_records_by_filter: false` : **pas de cumul conditionnel** (l'équivalent du « Only include linked records that meet certain conditions » d'Airtable). Contournement : 3 formules d'aide dans `AZ_Missions`, puis un Rollup `sum` simple sur chacune

-> `feature_group_by_aggregations: true` : les totaux par groupe dans une vue Grid groupée sont disponibles (plan B si un Rollup sur formule est refusé)

-> `feature_form_field_validation: false`, `feature_form_custom_submit_label: false` : formulaire sans validation avancée ni libellé de bouton personnalisé

## Table `AZ_Missions`

| Champ | Type NocoDB (`uidt`) | Notes |
|---|---|---|
| Id | ID | auto |
| Mission | SingleLineText | champ d'affichage |
| Client | Links (belongs to `AZ_Clients`) | créé depuis `AZ_Clients` en `hm` (voir `API.md` étape 2), puis renommé `Client` |
| Statut | SingleSelect | `À faire`, `En cours`, `Terminée`, `Arrêtée` |
| Montant | Currency | locale `fr-FR`, devise `EUR` |
| DateFin | Date | format `DD/MM/YYYY` |
| Facturé | Checkbox | |
| NotesMigration | LongText | alertes de nettoyage |
| MontantFacturé | Formula | `IF({Facturé}, {Montant}, 0)` |
| MontantResteAFacturer | Formula | `IF({Facturé}, 0, IF({Statut} = "Terminée", {Montant}, 0))` |
| MontantEnCours | Formula | `IF({Statut} = "En cours", {Montant}, 0)` |

Les 3 formules d'aide sont masquées dans les vues opérationnelles et dans le formulaire.

## `AZ_Clients` : champs ajoutés

| Champ | Type | Configuration |
|---|---|---|
| Entreprise | SingleLineText | Cabinet Legrand, TechFlow SAS, Studio Zenith, MarketPro, Atelier Nord |
| Missions | Links (has many `AZ_Missions`) | |
| NbMissions | Rollup | `Missions` -> `Id`, fonction `count` |
| CAFacturé | Rollup | `Missions` -> `MontantFacturé`, fonction `sum` |
| ResteAFacturer | Rollup | `Missions` -> `MontantResteAFacturer`, fonction `sum` |
| MontantEnCours | Rollup | `Missions` -> `MontantEnCours`, fonction `sum` |

Si l'UI ou l'API refuse un Rollup sur une colonne Formula (dépend de la version) : plan B = vue Grid `CA par client` sur `AZ_Missions` groupée par `Client`, avec l'agrégation `Sum` en pied de groupe sur `MontantFacturé`, `MontantResteAFacturer`, `MontantEnCours`.

### Écart de types constaté sur `AZ_Clients` (projet 4)

`Email` et `Telephone` sont en `SingleLineText` dans la base NocoDB (Airtable : `Email` et `Phone number`). C'est exactement le défaut n°1 du diagnostic. Correction proposée dans `API.md` (étape 6, optionnelle, 2 conversions).

## Vues `AZ_Missions`

-> Grid `Toutes les missions` : tri `DateFin` croissant

-> Grid `À facturer` : filtre `Statut` = `Terminée` ET `Facturé` décoché. Partagée en lecture seule (lien public de démonstration)

-> Grid `À vérifier (migration)` : filtre `NotesMigration` non vide

-> Kanban `Par statut` : groupé par `Statut`

-> Calendar `Échéances` : sur `DateFin`

-> Form `Nouvelle mission` : champs Mission, Client, Statut, Montant, DateFin, Facturé. Partagée en public (lien de démonstration `Lien_NocoDB_Demo`)

## Vues `AZ_Clients`

-> Grid `CA par client` : tri `CAFacturé` décroissant, champs Nom, Entreprise, NbMissions, CAFacturé, ResteAFacturer, MontantEnCours. Partagée en lecture seule

## Automatisations

Aucune (comme côté Airtable). Option ultérieure : workflow n8n hebdomadaire qui lit la vue `À facturer` via l'API NocoDB et envoie le récapitulatif par e-mail.
