# Interface Airtable native -- Demandes clients urgentes

Spec de la page Interface Designer à ajouter à l'interface `Sales Closer Souverain` (ou à une interface dédiée si tu isoles le projet). Objectif : donner au propriétaire une vue unique de son back-office avec KPIs + tableau priorisé + Kanban.

Rappel vocabulaire (source `dr-context/docs/DR/DR_Medias/Me_Logiciels/MeLc_Plateformes/MeLcPf_Airtable/AIRTABLE_INTERFACE_TERMS.md` v1.0.0+) : les éléments s'appellent `Number`, `Grid`, `Kanban`, `Chart`, `Text`, `Filter`, jamais `Tuile` / `Widget` / `Bandeau`.

## Page 1 -- `Demandes -- Vue d'ensemble` (Layout `Dashboard`)

Source table : `AZ_Demandes`.

Éléments à poser (drag & drop dans Interface Designer) :

### Ligne 1 -- Titre

Élément `Text` en tête :

- Contenu : `Tes demandes clients`

- Sous-titre : `Priorisées par urgence, statut Kanban, KPIs live`

### Ligne 2 -- 4 x `Number` côte à côte

Chaque `Number` prend 1/4 de la largeur. Choisir dans Interface Designer :

1. `Number` #1 : source vue `Nouvelles à traiter`, agrégation `Count` (nombre de records). Label : `Nouvelles à traiter`. Couleur du chiffre : bleu par défaut.

2. `Number` #2 : source vue `Toutes` filtrée à la volée sur UrgenceReelle = `Critique` (via le config panel du Number). Agrégation `Count`. Label : `Critiques`. Couleur : rouge.

3. `Number` #3 : source vue `Toutes` filtrée sur UrgenceReelle = `Haute`. Agrégation `Count`. Label : `Hautes`. Couleur : orange.

4. `Number` #4 : source vue `Toutes` filtrée sur Statut = `Fait`. Agrégation `Count`. Label : `Fait ce mois`. Ajouter un filtre date sur DateTraitement dans le mois courant si l'option existe dans la version Airtable actuelle.

### Ligne 3 -- `Grid` `Urgentes en premier`

Source : vue `Urgentes en premier` de la table `AZ_Demandes`.

Fields visibles : Ref, Client (Link, affiche Nom), Description (tronquée), UrgenceReelle, DateDemande, Statut.

Row height : `Medium` (pour lire la Description en 2 lignes).

Enable inline edit : `On` (pour changer UrgenceReelle et Statut sans quitter la page).

### Ligne 4 -- `Kanban` `Par statut`

Source : vue `Kanban Par statut` de la table `AZ_Demandes`.

Group by : Statut (déjà configuré dans la vue).

Cartes : Ref + Client + UrgenceReelle badge + Description tronquée.

Enable drag & drop entre colonnes : `On` (change Statut).

### Ligne 5 -- `Filter` global optionnel

Un `Filter` sur UrgenceReelle et Statut, positionné en haut à droite de la page, s'applique aux 2 éléments Grid et Kanban.

## Page 2 -- `Clients` (Layout `Dashboard`)

Source table : `AZ_Clients`.

- `Text` titre : `Tes clients demandeurs`.

- `Number` : Count sur vue `Tous les clients`. Label : `Clients total`.

- `Number` : Count sur vue `Top demandeurs`. Label : `Top demandeurs (3+ demandes)`.

- `Grid` : vue `Tous les clients`, fields Nom, Email, Telephone, NbDemandes, DateCreation.

- `Grid` : vue `Top demandeurs`, mêmes fields, tri NbDemandes desc.

## Page 3 -- `Détail projet` (Layout `Record review`)

Une page par record `AZ_Demandes`. Le propriétaire clique sur un record dans la Grid ou Kanban de la Page 1 et arrive sur une vue détail avec :

- Header : Ref, Client (Nom + Email + Telephone), DateDemande

- Description en full text

- UrgenceClient (badge, non éditable)

- UrgenceReelle (SingleSelect éditable en un clic)

- Statut (SingleSelect éditable + boutons rapides `-> En cours`, `-> Bloque`, `-> Fait`)

- Commentaires (LongText éditable)

- DateTraitement (Date, rempli automatiquement quand Statut passe à Fait via automation ou à la main)

## Partage public de la Page 1

Bouton `Share` en haut à droite de la page Interface. Générer un lien `pag...` en lecture seule pour la démo.

Note : le partage public d'une Interface Dashboard nécessite le forfait Team (voir `AIRTABLE_INTERFACE_TERMS.md` §Pièges, note S136z sur limitation Free/Plus). Si tu n'as pas le Team, garder l'Interface interne et partager plutôt un lien de vue Grid (`shr...`) pour la démo publique.

## Termes officiels utilisés

Tous les termes ci-dessus sont ceux du dropdown Element picker Airtable Interface Designer 2026. Aucun `Tuile`, `Bandeau`, `Widget`, `Formule dans Tuile` -- termes qui n'existent pas dans l'UI (piège documenté S135z, doc de référence `AIRTABLE_INTERFACE_TERMS.md`).
