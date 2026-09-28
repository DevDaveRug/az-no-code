# Interface NocoDB -- Missions et CA par client

Équivalent souverain de `../airtable/INTERFACE.md`, adapté au plan Free de l'instance (`limit_dashboard: 0`, voir `SPECS.md` §Limites).

## Ce qui remplace le Dashboard (indisponible en plan Free)

### 1- Vue partagée `CA par client` (lien public principal pour le CA)

Table `AZ_Clients`, vue Grid `CA par client` :

- Champs visibles : Nom, Entreprise, NbMissions, CAFacturé, ResteAFacturer, MontantEnCours

- Tri : `CAFacturé` décroissant

- Pied de colonne (aggregation) : `Sum` sur CAFacturé, ResteAFacturer, MontantEnCours. On obtient les 3 totaux du Dashboard Airtable (83 000 € / 3 200 € / 1 650 €) en bas de la grille

- `Share` -> `Enable public viewing` (lecture seule)

### 2- Formulaire partagé `Nouvelle mission` (lien de démonstration `Lien_NocoDB_Demo`)

Table `AZ_Missions`, vue Form :

- Champs : Mission (requis), Client (requis, liste des clients existants), Statut (requis), Montant (requis), DateFin, Facturé

- Masqués : NotesMigration, MontantFacturé, MontantResteAFacturer, MontantEnCours

- `Share` -> `Enable public viewing` : le visiteur peut soumettre une mission et la voir remonter dans `CA par client`

### 3- Kanban `Par statut` et Calendar `Échéances`

Vues standard de `AZ_Missions`, partageables en lecture seule si besoin d'une démonstration visuelle.

## Option : page Interface (si un emplacement est libre)

Le plan Free autorise 2 Interfaces de 2 pages. Si un emplacement est libre, une page `CA par client` peut reprendre la vue `CA par client` + le Kanban `Par statut`. Termes NocoDB : `Interface`, `Page`, `Table`, `Kanban`, `Text`. Vérifier d'abord les Interfaces existantes (projet 4) pour ne pas dépasser la limite.

## Termes NocoDB utilisés

`Grid`, `Form`, `Kanban`, `Calendar`, `Share`, `Enable public viewing`, `Rollup`, `Formula`, `Links`, `Interface`. Pas de `Dashboard` ni de `Widget` sur cette instance (plan Free).
