# Interface Airtable native -- masterclass-inscriptions

Version : 1.0.0
Date : 2026-09-16
Session : S135z-ccweb

## But

Interface Airtable Interface Designer pour ce projet, à montrer à un prospect qui organise des masterclass. Le prospect doit voir en 30 secondes que :

-> ses inscrits sont proprement organisés

-> les emails de confirmation partent tout seuls

-> il pilote tout depuis une seule vue "propriétaire"

Cette interface est une PAGE dans l'interface globale `Sales Closer Souverain` (voir `interfaces/AZ_PORTFOLIO_INTERFACE.md`). Elle NE remplace PAS la page Portfolio -- elle vit à côté et sert la démo dédiée.

## Prérequis

-> Table `AZ_Inscrits` créée dans la base `Sales Closer Souverain` (spec dans `airtable/SPECS.md`)

-> 5 lignes d'exemples collées (spec dans `airtable/exemples.md`)

-> Automation `Envoi email de confirmation` active

-> Interface globale ouverte, tu es en mode édition

## Ajouter la page "Masterclass inscriptions"

Onglet `Interfaces` -> ouvrir l'interface globale -> `+ Ajouter une page` -> layout `Tableau de bord` -> nommer `Masterclass inscriptions`.

Airtable dépose une page vide. Passe en mode édition (bouton crayon).

## Éléments à poser (dans l'ordre)

### 1- `Texte` (titre)

Contenu : `Masterclass inscriptions`. Style : `Titre` (Heading 1).

Juste en dessous, un second `Texte` en Sous-titre : `Chaque nouvel inscrit reçoit automatiquement son email de confirmation.` Sert de pitch d'ouverture.

### 2- Bandeau `Nombre` x 4

Côte à côte sous le titre. Chaque `Nombre` a une source (vue) + un calcul (dropdown, pas de formule).

-> **Nombre 1 -- Total inscrits**

   -> Source : table `AZ_Inscrits`, vue `Tous les inscrits`

   -> Calcul : `Nombre d'enregistrements` (Count)

   -> Label : "Total inscrits"

-> **Nombre 2 -- Emails envoyés**

   -> Source : `AZ_Inscrits`, créer une nouvelle vue filtrée `StatutEmail` = `Envoyé` (nomme-la "Emails envoyés")

   -> Calcul : `Nombre d'enregistrements`

   -> Label : "Emails envoyés"

-> **Nombre 3 -- Erreurs**

   -> Source : `AZ_Inscrits`, vue `Erreurs d'envoi`

   -> Calcul : `Nombre d'enregistrements`

   -> Label : "Erreurs"

   -> Couleur d'accent : rouge (si le composant le permet)

-> **Nombre 4 -- Taux de succès**

   -> Source : `AZ_Inscrits`, vue `Tous les inscrits`

   -> Calcul : `Pourcentage` (Percentage) sur les records qui matchent une condition -> condition : `StatutEmail` = `Envoyé`

   -> Label : "% d'emails délivrés"

Résultat visuel : 4 tuiles chiffrées qui rassurent le prospect ("tu vois combien de gens se sont inscrits et si le système leur envoie bien le mail").

### 3- `Kanban` "Statut d'envoi"

Drag & drop sous le bandeau chiffres.

-> Source : `AZ_Inscrits`, vue `Tous les inscrits`

-> Regroupement (Stack by) : `StatutEmail`

-> Colonnes : `En attente` (gris), `Envoyé` (vert), `Erreur` (rouge)

-> Champs sur la carte : `Prenom`, `Email`, `DateMasterclass`

Résultat : le prospect voit visuellement où en est chaque inscrit dans le cycle d'envoi.

### 4- `Chronologie` (Timeline) "Masterclass à venir"

Sous le Kanban.

-> Source : `AZ_Inscrits`, vue `Par masterclass`

-> Champ date : `DateMasterclass`

-> Coloration : par `StatutEmail`

Résultat : le prospect voit sa charge par date de session. Utile s'il a plusieurs masterclass dans le mois.

### 5- `Grille` (Grid) "Détail des inscrits"

Sous la chronologie.

-> Source : `AZ_Inscrits`, vue `Par masterclass`

-> Champs affichés : `Prenom`, `Email`, `DateMasterclass`, `StatutEmail`, `DateInscription`, `Notes`

-> Groupement : hérité de la vue (par `DateMasterclass`)

-> Option `Permettre l'édition en ligne` : activer (le prospect peut ajouter des notes)

### 6- `Bouton` "Ouvrir le formulaire d'inscription"

En haut à droite (position fixe).

-> Action : `Ouvrir URL`

-> URL : lien partagé public du formulaire d'inscription (généré par Airtable dans l'onglet `Formulaires`)

-> Label : "Ouvrir le formulaire"

Résultat : le prospect peut simuler une inscription en un clic et voir l'automation partir en live.

### 7- `Texte` (footer explicatif)

En bas de page.

Contenu :

```
Chaque nouvel inscrit ajouté ici (via le formulaire ou à la main) déclenche automatiquement :
1- Un email de confirmation personnalisé (prénom + date de la session)
2- Une mise à jour du statut d'envoi visible dans le Kanban ci-dessus

Aucune manipulation manuelle. Aucun tableau Excel à mettre à jour. Aucun mail à envoyer un par un.
```

Style : `Paragraphe`.

## Publication et partage

Clique `Publier` en haut à droite -> `Partager` -> `Créer un lien public` (lecture seule pour un prospect externe).

Le prospect qui clique sur ce lien voit ta page en lecture seule, sans compte Airtable, sans exposer les autres tables de ta base.

Le lien à mettre dans `Lien_Airtable_Demo` de la table `AZ_Portfolio` (champ URL) pour que la page Portfolio pointe vers cette démo.

## Angle "donner envie"

-> **Le bandeau chiffres au-dessus de tout** = argument émotionnel immédiat ("regarde mes 5 inscrits, 4 emails partis, 0 erreur")

-> **Le Kanban** = argument visuel ("je vois où j'en suis")

-> **La Timeline** = argument planification ("je gère plusieurs sessions sans confusion")

-> **Le Bouton formulaire** = argument démo live ("tu peux tester avec ton propre email, ça arrive en 20 secondes")

Ne surcharge pas la page. Reste sobre. Un prospect qui voit trop de choses décroche.

## Comparaison avec le code souverain (Next.js)

L'interface code souverain est déjà déployable sur Vercel (`az-code/defis/masterclass-inscriptions/`). Elle propose :

-> `/` = vue liste groupée par date, badges statut

-> `/nouveau` = formulaire d'inscription client-side

Différence avec l'Airtable Interface : la version code est BRANDÉE (le prospect voit son propre nom / logo / couleurs), alors qu'Airtable Interface reste "Airtable look". Argument à raconter : "Si tu veux ça brandé à ta marque, on passe sur la version code, même fonctionnalité, ton domaine, ton design."

## Changelog

-> 1.0.0 -- 2026-09-16 -- création (S135z-ccweb, Cor David) : spec de l'Interface Airtable native pour ce projet, à ajouter comme page dans l'interface globale `Sales Closer Souverain`. Éléments avec les VRAIS termes Airtable (Nombre, Kanban, Chronologie, Grille, Bouton, Texte). Aucune formule à taper.
