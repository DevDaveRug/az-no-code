# Interface NocoDB native -- Demandes clients urgentes

Spec du Dashboard NocoDB à créer. NocoDB propose une fonctionnalité `Dashboards` (à partir de la v0.203+) avec widgets Number, Kanban, Calendar, Table, Button, Text. Équivalent fonctionnel de l'Interface Designer Airtable.

## Dashboard `Vue d'ensemble propriétaire`

### Widget Text (titre)

- Contenu : `Tes demandes clients`. Sous-titre : `Priorisées par urgence, statut Kanban`.

### 4 Widgets Number en ligne

1. `Number` : source vue `Nouvelles à traiter`. Aggregation `Count`. Label `Nouvelles`.

2. `Number` : source vue `Toutes`, filtre UrgenceReelle = `Critique`. Label `Critiques`. Couleur rouge.

3. `Number` : filtre UrgenceReelle = `Haute`. Label `Hautes`. Couleur orange.

4. `Number` : filtre Statut = `Fait` + DateTraitement dans le mois courant. Label `Fait ce mois`.

### Widget Table `Urgentes en premier`

Source : vue `Urgentes en premier`. Fields visibles : Ref, Client, Description, UrgenceReelle, DateDemande, Statut. Row height `Medium`. Inline edit : `On`.

### Widget Kanban `Par statut`

Source : vue `Kanban Par statut`. Group by Statut. Drag & drop entre colonnes : `On`.

### Widget Button `Nouveau formulaire`

Bouton lien vers l'URL publique du form partageable. Label `Envoyer une demande`. Ouvre le form dans un nouvel onglet.

## Partage public du Dashboard

NocoDB permet le partage public d'un Dashboard via `Share > Public > Read-only`. L'URL générée : `https://sb-nocodb.coolify.salescloser.fr/#/nc/dashboard/<uuid>` -- à donner au propriétaire pour son bookmark.

Note : NocoDB Dashboards n'est pas encore aussi mature qu'Airtable Interfaces (au 20/09/2026, en beta). Fallback si un widget manque : partager directement la vue Kanban en public, qui affiche déjà les priorités par colonne.

## Termes NocoDB officiels

Termes utilisés ci-dessus, tous présents dans la doc NocoDB 2026 : `Widget`, `Dashboard`, `Number`, `Kanban`, `Table`, `Button`, `Text`, `Grid`, `Form`. Ces termes sont ceux de la doc NocoDB officielle, pas des inventions.
