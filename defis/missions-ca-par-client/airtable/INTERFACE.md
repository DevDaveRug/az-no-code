# Interface Airtable native -- Missions et CA par client

Page à ajouter à l'interface `Sales Closer Souverain`. Objectif : que le propriétaire voie en un écran son CA facturé, ce qui reste à facturer et ce qui est en cours, client par client.

Vocabulaire : source `dr-context/docs/DR/DR_Medias/Me_Logiciels/MeLc_Plateformes/MeLcPf_Airtable/AIRTABLE_INTERFACE_TERMS.md` v1.3.0. Éléments utilisés : `Text`, `Number`, `Chart`, `Grid`, `Kanban`, `Filter`, `Button`. Layout : `Dashboard`.

## Page `CA par client` (Layout `Dashboard`)

### Ligne 1 -- `Text`

- Style `Heading 1` : `Ton chiffre d'affaires, client par client`

- Style `Subtitle` : `Facturé, à facturer, en cours : calculé tout seul à chaque mission ajoutée`

### Ligne 2 -- 3 x `Number`

1. `Number` : source table `AZ_Missions`, vue `Toutes les missions`, `Field summary` sur `Montant`, agrégation `Sum`, filtre de l'élément `Facturé` coché. Label : `CA facturé`

2. `Number` : source vue `À facturer`, `Field summary` sur `Montant`, agrégation `Sum`. Label : `Reste à facturer`

3. `Number` : source vue `Toutes les missions`, `Field summary` sur `Montant`, agrégation `Sum`, filtre `Statut` = `En cours`. Label : `En cours`

Valeurs attendues avec les exemples : 83 000 € / 3 200 € / 1 650 €.

### Ligne 3 -- `Chart`

- Type : `bar`

- Source : table `AZ_Clients`, vue `CA par client`

- Axe X : `Nom`

- Axe Y : `CAFacturé` (agrégation `Sum`)

- Tri : valeur décroissante

Titre : `CA facturé par client`. C'est la réponse visuelle à « on n'arrive pas à sortir le CA par client ».

### Ligne 4 -- `Grid` `À facturer`

Source : vue `À facturer` de `AZ_Missions`. Champs : Mission, Client, Montant, DateFin, Facturé. Édition en ligne activée (cocher `Facturé` fait sortir la ligne de la liste et bascule le montant dans `CA facturé`).

### Ligne 5 -- `Kanban` `Par statut`

Source : vue `Par statut` de `AZ_Missions`. Glisser une carte d'une colonne à l'autre change le `Statut`.

### Ligne 6 -- `Grid` `CA par client`

Source : vue `CA par client` de `AZ_Clients`. Champs : Nom, Entreprise, NbMissions, CAFacturé, ResteAFacturer, MontantEnCours. Lecture seule.

### En haut à droite -- `Filter`

Style `Dropdown`, sur `Client` puis `Statut`. S'applique aux éléments `Grid` et `Kanban` de la page.

### `Button`

Action `Open record creation form` sur `AZ_Missions`. Label : `Ajouter une mission`.

## Partage

Démonstration publique : on partage le **formulaire** `Nouvelle mission` (voir `SPECS.md`), pas la page Interface (le partage public d'une Interface exige une authentification ou un forfait supérieur, Cor David S138z). La page Interface sert aux captures d'écran et à la démonstration en visio.
