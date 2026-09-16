# Interface NocoDB native -- masterclass-inscriptions

Version : 1.0.0
Date : 2026-09-16
Session : S135z-ccweb

## But

Équivalent souverain de l'interface Airtable, chez toi (Coolify + NocoDB). Le prospect doit voir la même chose : ses inscrits organisés, les emails de confirmation partis automatiquement, un tableau de bord unique. Différence commerciale à raconter : "c'est le même produit visuel, mais hébergé sur ton serveur, sans quota d'automation, sans facturation par utilisateur."

Cette interface s'ajoute comme **Dashboard** dans la base `Masterclass Inscriptions` (spec dans `nocodb/SPECS.md`), aux côtés de la table `AZ_Inscrits` et des vues Grid/Form.

## Prérequis

-> Instance NocoDB opérationnelle sur `sb-nocodb.coolify.salescloser.fr` (ou équivalent client)

-> Base `Masterclass Inscriptions` créée avec la table `AZ_Inscrits`

-> 5 lignes d'exemples collées (spec dans `nocodb/exemples.md`)

-> Workflow n8n `masterclass-inscrit-cree` actif sur `sb-n8n.coolify.salescloser.fr`

-> Webhook NocoDB `After Insert` configuré et testé (au moins 1 ligne créée en test)

## Vocabulaire NocoDB (ce qui remplace Airtable)

| Concept Airtable | Équivalent NocoDB |
|---|---|
| Interface Designer | Dashboard (onglet dédié dans la base) |
| Page dans une interface | Dashboard (chaque Dashboard = 1 page) |
| Élément `Nombre` | Widget `Number` |
| Élément `Grille` | Widget `Table` (pointe vers une View existante) |
| Élément `Chronologie` | Widget `Timeline` (dispo via plugin ou vue Calendar) |
| Élément `Kanban` | Widget `Kanban` (pointe vers une View Kanban) |
| Élément `Bouton` | Widget `Button` (URL externe) |
| Élément `Texte` | Widget `Text` (markdown supporté) |
| Filtre global | Filtre appliqué au Dashboard entier |

Note : selon la version de NocoDB, certains widgets peuvent porter des noms très légèrement différents (`Number` vs `Metric`, `Timeline` vs `Calendar`). Les concepts restent les mêmes.

## Ajouter le Dashboard "Masterclass inscriptions"

Dans la base `Masterclass Inscriptions` :

-> Panneau de gauche -> `+ Add new` -> `Dashboard`

-> Nom : `Masterclass inscriptions`

-> Le Dashboard s'ouvre vide. Bouton `+ Add widget` en haut à droite pour poser chaque brique.

## Widgets à poser (dans l'ordre)

### 1- `Text` (titre)

Contenu (markdown) :

```
# Masterclass inscriptions

Chaque nouvel inscrit reçoit automatiquement son email de confirmation.
```

Poser en haut, pleine largeur.

### 2- Bandeau `Number` x 4

Côte à côte sous le titre. Chaque widget `Number` a une source (table + filtre optionnel) et une agrégation (dropdown).

-> **Number 1 -- Total inscrits**

   -> Data source : table `AZ_Inscrits`

   -> Filter : (aucun)

   -> Aggregation : `Count`

   -> Label : "Total inscrits"

-> **Number 2 -- Emails envoyés**

   -> Data source : table `AZ_Inscrits`

   -> Filter : `StatutEmail = Envoyé`

   -> Aggregation : `Count`

   -> Label : "Emails envoyés"

-> **Number 3 -- Erreurs**

   -> Data source : table `AZ_Inscrits`

   -> Filter : `StatutEmail = Erreur`

   -> Aggregation : `Count`

   -> Label : "Erreurs"

   -> Couleur : rouge (dans les options du widget)

-> **Number 4 -- Taux de succès**

   -> Data source : table `AZ_Inscrits`

   -> Aggregation : `Percentage` avec condition `StatutEmail = Envoyé`

   -> Label : "% d'emails délivrés"

   -> Si le calcul percentage n'est pas natif dans ta version NocoDB : créer un champ Formula `TauxSucces = IF({StatutEmail}='Envoyé',1,0)` puis `Aggregation: Average` sur ce champ, affichage en pourcentage.

Résultat visuel : 4 chiffres au-dessus qui rassurent le prospect ("tu vois combien de gens se sont inscrits et si le système leur envoie bien le mail").

### 3- `Kanban` "Statut d'envoi"

Sous le bandeau chiffres.

-> Prérequis : créer d'abord une **vue Kanban** dans la table `AZ_Inscrits` :

   -> onglet table `AZ_Inscrits` -> `+ Create view` -> `Kanban`

   -> Group by : `StatutEmail`

   -> Colonnes : `En attente`, `Envoyé`, `Erreur`

   -> Card fields : `Prenom`, `Email`, `DateMasterclass`

   -> Nom de la vue : `Kanban statut envoi`

-> Puis dans le Dashboard : `+ Add widget` -> `Kanban` -> pointer vers la vue `Kanban statut envoi`.

Résultat : le prospect voit visuellement où en est chaque inscrit dans le cycle d'envoi.

### 4- `Calendar` ou `Timeline` "Masterclass à venir"

Sous le Kanban.

-> Prérequis : créer une **vue Calendar** dans la table `AZ_Inscrits` :

   -> `+ Create view` -> `Calendar`

   -> Date field : `DateMasterclass`

   -> Nom : `Calendrier masterclass`

-> Dans le Dashboard : `+ Add widget` -> `Calendar` -> pointer vers `Calendrier masterclass`.

-> Coloration des events : par `StatutEmail` (option dans le widget si dispo, sinon accepter la couleur par défaut).

Résultat : le prospect voit sa charge par date de session.

### 5- `Table` "Détail des inscrits"

Sous le calendrier.

-> Data source : vue `Par masterclass` (déjà créée dans `SPECS.md`)

-> Champs affichés : `Prenom`, `Email`, `DateMasterclass`, `StatutEmail`, `DateInscription`, `Notes`

-> Groupement : hérité de la vue (par `DateMasterclass`)

-> Édition en ligne : activée (le prospect peut ajouter des notes)

### 6- `Button` "Ouvrir le formulaire d'inscription"

En haut à droite du Dashboard.

-> Type : `URL button`

-> URL : lien public de la vue Form `Inscription masterclass` (généré par NocoDB via `Share view`)

-> Label : "Ouvrir le formulaire"

Résultat : démo live -- le prospect peut simuler une inscription en un clic et voir l'automation partir via n8n.

### 7- `Text` (footer explicatif)

En bas du Dashboard. Contenu (markdown) :

```
Chaque nouvel inscrit ajouté ici (via le formulaire ou à la main) déclenche automatiquement :

1- Un webhook NocoDB vers ton workflow n8n

2- Le workflow envoie un email de confirmation personnalisé (prénom + date de session)

3- Le statut d'envoi est mis à jour dans le Kanban ci-dessus

Aucune manipulation manuelle. Aucun tableau Excel. Aucun mail envoyé un par un. Aucun quota d'automation à surveiller (contrairement à Airtable free = 100 runs/mois).
```

## Partage public du Dashboard

`Share` en haut à droite -> `Enable public link` -> mode `Read-only`.

Le lien public affiche le Dashboard sans compte, sans exposer les autres bases NocoDB de ton instance.

Le lien à mettre dans `Lien_NocoDB_Demo` de la table `AZ_Portfolio` (base Airtable `Sales Closer Souverain`) -- oui, la table portfolio reste sur Airtable même si un projet expose un lien NocoDB. Cohérence pour le prospect : il clique et voit exactement la même chose que le lien Airtable, hébergé chez toi.

## Angle "souverain" (à raconter au prospect)

-> **Même interface visuelle** que la version Airtable -> aucun sacrifice fonctionnel

-> **Chez toi** (VPS Hostinger + Coolify) -> pas de quota, pas de dépendance à un compte US

-> **Workflow n8n modifiable** à l'infini -> tu peux ajouter demain un SMS Twilio, une notif Telegram, un log Google Sheets, sans changer d'outil

-> **Coût fixe** (VPS ~10 €/mois) au lieu de coût variable Airtable (5-20 €/utilisateur/mois)

-> **Pas de rupture** si Airtable change ses tarifs, coupe une fonctionnalité ou disparaît

## Comparaison avec le Dashboard Airtable

L'interface Airtable native (`airtable/INTERFACE.md`) et ce Dashboard NocoDB visent le même effet visuel. Différences réelles :

-> **UI plus polie** côté Airtable (drag & drop plus fluide, transitions animées) -> avantage démo prospect qui ne connaît pas les outils

-> **Contrôle total** côté NocoDB (SQL brut accessible, backup Postgres, restauration en 1 commande) -> avantage démo prospect technique ou qui a été échaudé par un SaaS

-> **Automation gratuite illimitée** côté NocoDB (via n8n self-host) -> argument prix si le prospect anticipe un volume élevé

Le pitch commercial reste : "Airtable pour aller vite, NocoDB si tu veux ta souveraineté, code Next.js si tu veux ta marque -- tu choisis, on livre les 3."

## Changelog

-> 1.0.0 -- 2026-09-16 -- création (S135z-ccweb, Cor David) : spec du Dashboard NocoDB natif pour ce projet, équivalent fonctionnel de l'interface Airtable. Vocabulaire NocoDB explicite (Dashboard, Widget, Number, Kanban, Calendar, Button, Text). Prérequis webhook NocoDB + workflow n8n. Angle souverain à raconter au prospect. Aucune formule à taper hors le fallback `TauxSucces` si le percentage natif absent.
