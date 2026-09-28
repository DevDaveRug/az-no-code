# Airtable -- Missions et CA par client

Base cible : `Sales Closer Souverain` (`appTqLo3JDg7d1fak`). On **réutilise** la table `AZ_Clients` du projet 4 (demandes clients urgentes) et on ajoute une seule table, `AZ_Missions`. Pas de 2e table clients : ce serait reproduire le défaut diagnostiqué (le même client à 2 endroits).

Structure « générique » recommandée à la cliente (Clients + Missions, noms de champs en clair) : voir `../DIAGNOSTIC.md` §II. Ici, la déclinaison dans la base unifiée.

## Table `AZ_Missions` (nouvelle)

| Champ | Type Airtable (UI FR / EN) | Notes |
|---|---|---|
| Mission | Texte sur une ligne / Single line text | champ principal |
| Client | Lien vers un autre enregistrement / Link to another record -> `AZ_Clients` | 1 seul client (`Allow linking to multiple records` = Off) |
| Statut | Sélection unique / Single select | `À faire` (gris), `En cours` (jaune), `Terminée` (vert), `Arrêtée` (rouge) |
| Montant | Devise / Currency | symbole `€`, précision 2 décimales |
| DateFin | Date | format `Local` (jj/mm/aaaa), sans heure |
| Facturé | Case à cocher / Checkbox | coché = facturé |
| NotesMigration | Texte long / Long text | alertes de nettoyage (date déduite, doublon possible). Vide = ligne propre |
| EmailClient | Recherche / Lookup -> `Client` -> `Email` | optionnel, pour voir l'e-mail sans le retaper |

## Table `AZ_Clients` (existante, projet 4) : champs ajoutés

Champs existants conservés : `Nom`, `Email`, `Telephone`, `DateCreation`, `Demandes`, `NbDemandes`, `Prospect`.

| Champ ajouté | Type | Configuration |
|---|---|---|
| Entreprise | Texte sur une ligne | Cabinet Legrand, TechFlow SAS, Studio Zenith, MarketPro, Atelier Nord. Sépare la personne (`Nom`) de l'entreprise, le cas « Orange / J. Lefebvre » du brief |
| Missions | Lien vers `AZ_Missions` | symétrique du champ `Client` (créé automatiquement par l'UI) |
| NbMissions | Count -> `Missions` | |
| CAFacturé | Cumul / Rollup -> `Missions` -> `Montant`, `SUM(values)` | `Only include linked records that meet certain conditions` : `Facturé` est coché |
| ResteAFacturer | Cumul / Rollup -> `Missions` -> `Montant`, `SUM(values)` | conditions : `Statut` = `Terminée` ET `Facturé` n'est pas coché |
| MontantEnCours | Cumul / Rollup -> `Missions` -> `Montant`, `SUM(values)` | condition : `Statut` = `En cours` |

Formatage des 3 cumuls : `Currency`, `€`, 2 décimales.

## Vues `AZ_Missions`

### Grid `Toutes les missions`

Tri : `DateFin` croissante. Champs : Mission, Client, Statut, Montant, DateFin, Facturé.

### Grid `À facturer`

Filtre : `Statut` = `Terminée` ET `Facturé` n'est pas coché. Tri : `DateFin` croissante. Avec les données d'exemple : 1 ligne (Tunnel de vente, Emma Petit, 3 200 €).

### Grid `À vérifier (migration)`

Filtre : `NotesMigration` n'est pas vide. Avec les données d'exemple : 5 lignes. Vue de contrôle à vider au fil des confirmations.

### Kanban `Par statut`

Regroupement : `Statut`. Carte : Mission, Client, Montant, DateFin.

### Calendar `Échéances`

Champ date : `DateFin`. Montre ce que le type Date rend possible (impossible avec « juin » en texte).

## Vues `AZ_Clients`

### Grid `CA par client`

Tri : `CAFacturé` décroissant. Champs : Nom, Entreprise, NbMissions, CAFacturé, ResteAFacturer, MontantEnCours. Masquer les champs du projet 4 (Demandes, NbDemandes) dans cette vue seulement.

Résultat attendu avec les données d'exemple :

| Nom | Entreprise | NbMissions | CAFacturé | ResteAFacturer | MontantEnCours |
|---|---|---|---|---|---|
| Bob Durand | TechFlow SAS | 1 | 80 000,00 € | 0,00 € | 0,00 € |
| Alice Martin | Cabinet Legrand | 3 | 3 000,00 € | 0,00 € | 450,00 € |
| Emma Petit | MarketPro | 1 | 0,00 € | 3 200,00 € | 0,00 € |
| Chloe Dubois | Studio Zenith | 1 | 0,00 € | 0,00 € | 1 200,00 € |
| Fabien Roux | Atelier Nord | 1 | 0,00 € | 0,00 € | 0,00 € |

## Formulaire partageable `Nouvelle mission`

Formulaire sur `AZ_Missions` (vue Form ou page Interface `Form`). Champs dans cet ordre :

- Mission (label : `Intitulé de la mission`), requis

- Client (label : `Client`), requis. Le formulaire affiche la liste des clients existants : on choisit, on ne tape pas. C'est la démonstration du point 2 du diagnostic

- Statut, requis, défaut `À faire`

- Montant (label : `Montant HT en €`), requis

- DateFin (label : `Date de fin prévue`), optionnel

- Facturé, optionnel

Champs masqués : NotesMigration, EmailClient.

Message après envoi : `Mission enregistrée. Elle apparaît déjà dans le CA de ton client.`

Partage : `Share form` -> lien public. Ce lien va dans `Lien_Airtable_Demo` de `AZ_Portfolio`.

Note confidentialité : un formulaire public avec un champ lien affiche la liste des clients. Ici les clients sont fictifs. Pour une vraie base, garder ce formulaire interne, ou passer par un champ texte de rapprochement comme dans le projet 4 (`EmailClientTemp` + automatisation).

## Automatisations

Aucune. La valeur de ce projet est dans la structure. Option si la cliente le demande plus tard : rappel hebdomadaire par e-mail de la vue `À facturer` (déclencheur `At scheduled time`, action `Send email` avec la liste des lignes de la vue).

## Recréation via API Metadata

Voir `schema.json`. Rappels (`AIRTABLE_INTERFACE_TERMS.md` §Pièges API Metadata) : créer `AZ_Missions` sans le lien, puis créer le lien `Client` ; vérifier que le symétrique `Missions` existe côté `AZ_Clients` ; Count, Rollup et Lookup se créent dans l'UI ; vues et formulaire restent UI-only.

## Import des données d'exemple

Voir `../import-csv/README.md` (import de `missions.csv`, puis conversion de la colonne `Client` en lien : Airtable rapproche automatiquement chaque nom d'un enregistrement `AZ_Clients` existant, c'est l'étape 3 du plan de migration du diagnostic, jouée sur la vraie base).
