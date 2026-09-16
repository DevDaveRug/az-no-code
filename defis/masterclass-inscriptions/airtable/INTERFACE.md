# Interface Airtable native -- masterclass-inscriptions

Version : 2.0.0
Date : 2026-09-16
Session : S135z-ccweb

## But

Interface Airtable Interface Designer pour ce projet, à montrer à un prospect qui organise des masterclass. Le prospect doit voir en 30 secondes que :

-> ses inscrits sont proprement organisés

-> les emails de confirmation partent tout seuls

-> il pilote tout depuis une seule page "propriétaire"

Cette page vit à côté de la page Portfolio globale dans la même Interface `Sales Closer Souverain` (spec Portfolio dans `interfaces/AZ_PORTFOLIO_INTERFACE.md`).

## Termes Airtable utilisés dans cette spec

Tous les termes ci-dessous sont les VRAIS termes anglais officiels de l'Element picker (panneau de gauche en mode édition) et du Properties panel (panneau de droite quand un Element est sélectionné). Source unique : `dr-context/docs/DR/DR_Medias/Me_Logiciels/MeLc_Plateformes/MeLcPf_Airtable/AIRTABLE_INTERFACE_TERMS.md`. Si l'UI de David est en français, les libellés peuvent être traduits par Airtable, mais la fonctionnalité et la position sont identiques.

Note : la traduction française d'Airtable (si activée dans les préférences) traduit certains labels mais garde souvent le terme anglais dans le panneau de sélection. En cas de doute -> basculer l'UI en anglais dans les préférences Airtable pour retrouver exactement les termes ci-dessous.

## Prérequis

-> Table `AZ_Inscrits` créée dans la base `Sales Closer Souverain` (spec dans `airtable/SPECS.md`)

-> 5 lignes d'exemples collées (spec dans `airtable/exemples.md`)

-> Automation `Envoi email de confirmation` active

-> Interface `Sales Closer Souverain` ouverte, en mode édition

## Ajouter la page "Masterclass inscriptions"

En haut à droite de la base, clic sur l'onglet `Interfaces` -> ouvrir l'Interface `Sales Closer Souverain` -> clic `+ Add page` (ou `+ Ajouter une page` en FR) -> choisir le layout `Dashboard`.

Nommer la page `Masterclass inscriptions`. Airtable crée une page vide en mode édition, avec le panneau `Element picker` à gauche (icônes des Elements à drag & drop) et le panneau `Properties panel` à droite (pour configurer l'Element sélectionné).

## Elements à poser (dans l'ordre)

### 1- `Text` (titre)

Drag & drop depuis l'Element picker (icône `T`). Dans le Properties panel à droite :

-> Style : `Heading 1`

-> Contenu : `Masterclass inscriptions`

Ajouter un second `Text` juste en dessous :

-> Style : `Subtitle` ou `Paragraph`

-> Contenu : `Chaque nouvel inscrit reçoit automatiquement son email de confirmation.`

### 2- 4 x `Number` côte à côte

Drag & drop 4 fois l'Element `Number` (icône `123`). Positionner en ligne sous le titre. Chacun se configure indépendamment via le Properties panel :

-> **Number 1 -- Total inscrits**

   -> Section `Data` : Source = table `AZ_Inscrits`, vue `Tous les inscrits`

   -> Type de calcul : `Record count` (dropdown en haut du Properties panel)

   -> Section `Appearance` : Label = "Total inscrits"

-> **Number 2 -- Emails envoyés**

   -> Section `Data` : Source = `AZ_Inscrits`, créer une nouvelle vue filtrée `StatutEmail` = `Envoyé` (nomme-la "Emails envoyés") ET utiliser cette vue

   -> Type de calcul : `Record count`

   -> Label : "Emails envoyés"

-> **Number 3 -- Erreurs**

   -> Section `Data` : Source = `AZ_Inscrits`, vue `Erreurs d'envoi`

   -> Type de calcul : `Record count`

   -> Label : "Erreurs"

   -> Section `Appearance` : accent rouge si disponible

-> **Number 4 -- Taux de succès**

   -> Section `Data` : Source = `AZ_Inscrits`, vue `Tous les inscrits`

   -> Type de calcul : `Percentage` (dropdown), avec condition `StatutEmail = Envoyé`

   -> Label : "% d'emails délivrés"

   -> Si le `Percentage` natif n'est pas dispo dans votre plan Airtable : créer un champ `Formula` dans la table (`IF({StatutEmail}='Envoyé',1,0)`) puis utiliser `Field summary` -> `Average` sur ce champ, avec affichage en pourcentage dans le Properties panel

Résultat visuel : 4 valeurs numériques côte à côte qui rassurent le prospect ("tu vois combien de gens se sont inscrits et si le système leur envoie bien le mail"). Aucune formule à taper dans l'Interface Designer -- tout se sélectionne dans les dropdowns du Properties panel.

### 3- `Kanban` (element) "Statut d'envoi"

Drag & drop l'Element `Kanban` sous les 4 Numbers.

-> Section `Data` : Source = `AZ_Inscrits`, vue `Tous les inscrits`

-> Section `Data` : `Stack by` = champ `StatutEmail`

-> Colonnes automatiques héritées des Single Select : `En attente` (gris), `Envoyé` (vert), `Erreur` (rouge)

-> Section `Appearance` : champs affichés sur chaque carte = `Prenom`, `Email`, `DateMasterclass`

Résultat : le prospect voit visuellement où en est chaque inscrit dans le cycle d'envoi.

### 4- `Timeline` (element) "Masterclass à venir"

Drag & drop l'Element `Timeline` sous le Kanban.

Note : cet Element nécessite un plan Airtable payant. Si absent, remplacer par un `Calendar` layout séparé ou une seconde page dédiée.

-> Section `Data` : Source = `AZ_Inscrits`, vue `Par masterclass`

-> Champ date : `DateMasterclass`

-> Coloration : par `StatutEmail`

Résultat : le prospect voit sa charge par date de session. Utile s'il a plusieurs masterclass dans le mois.

### 5- `Grid` (element) "Détail des inscrits"

Drag & drop l'Element `Grid` sous la Timeline.

-> Section `Data` : Source = `AZ_Inscrits`, vue `Par masterclass`

-> Section `Appearance` : champs affichés = `Prenom`, `Email`, `DateMasterclass`, `StatutEmail`, `DateInscription`, `Notes`

-> Groupement : hérité de la vue (par `DateMasterclass`)

-> Section `User actions` : cocher `Allow inline editing` (le prospect peut ajouter des notes)

### 6- `Button` "Ouvrir le formulaire d'inscription"

Drag & drop l'Element `Button` en haut à droite de la page (position fixe).

Dans le Properties panel :

-> Section `Data` -> Action = `Go to external URL`

-> URL = lien public de la vue Form `Inscription masterclass` (généré par Airtable via `Share view` dans l'onglet Base -> vue Form)

-> Section `Appearance` -> Label = "Ouvrir le formulaire"

Résultat : le prospect peut simuler une inscription en un clic et voir l'automation partir en live.

### 7- `Text` (footer explicatif)

Drag & drop un `Text` en bas de page.

-> Style : `Paragraph`

-> Contenu (Markdown supporté) :

```
Chaque nouvel inscrit ajouté ici (via le formulaire ou à la main) déclenche automatiquement :

1- Un email de confirmation personnalisé (prénom + date de la session)

2- Une mise à jour du statut d'envoi visible dans le Kanban ci-dessus

Aucune manipulation manuelle. Aucun tableau Excel à mettre à jour. Aucun mail à envoyer un par un.
```

## Publication et partage

Clic `Publish` en haut à droite -> `Share` -> `Create a shareable link` (lecture seule pour un prospect externe).

Le prospect qui clique sur ce lien voit la page en lecture seule, sans compte Airtable, sans exposer les autres tables de la base.

Le lien à mettre dans le champ `Lien_Airtable_Demo` de la table `AZ_Portfolio` (champ URL) pour que la page Portfolio pointe vers cette démo.

## Angle "donner envie"

-> **Les 4 `Number` en haut** = argument émotionnel immédiat ("regarde mes 5 inscrits, 4 emails partis, 0 erreur")

-> **Le `Kanban`** = argument visuel ("je vois où j'en suis")

-> **La `Timeline`** = argument planification ("je gère plusieurs sessions sans confusion")

-> **Le `Button` formulaire** = argument démo live ("tu peux tester avec ton propre email, ça arrive en 20 secondes")

Ne pas surcharger la page. Rester sobre. Un prospect qui voit trop de choses décroche.

## Comparaison avec le code souverain (Next.js)

L'interface code souverain est déjà déployable sur Vercel (`az-code/defis/masterclass-inscriptions/`). Elle propose :

-> `/` = vue liste groupée par date, badges statut

-> `/nouveau` = formulaire d'inscription client-side

Différence avec l'Interface Airtable : la version code est BRANDÉE (le prospect voit son propre nom / logo / couleurs), alors qu'Airtable Interface reste "Airtable look". Argument à raconter : "Si tu veux ça brandé à ta marque, on passe sur la version code, même fonctionnalité, ton domaine, ton design."

## Changelog

-> 2.0.0 -- 2026-09-16 -- correction termes officiels (S135z-ccweb, Cor David) : bascule de la spec française inventée (Nombre / Grille / Chronologie / Bouton / Texte) vers les VRAIS termes anglais officiels de l'Element picker Airtable (Number / Grid / Timeline / Button / Text / Kanban) sourcés sur la doc Airtable Support 2026. Ajout section "Termes Airtable utilisés dans cette spec" pointant vers `dr-context/docs/DR/DR_Medias/Me_Logiciels/MeLc_Plateformes/MeLcPf_Airtable/AIRTABLE_INTERFACE_TERMS.md`. Précisions sur Element picker (gauche), Properties panel (droite), sections du panel (Data / Appearance / User actions), types de calcul du Number (`Record count`, `Percentage`, `Field summary`) qui se choisissent dans un dropdown -- aucune formule à taper dans l'Interface Designer. Layout `Dashboard` explicitement nommé (pas "Tableau de bord recommandé").

-> 1.0.0 -- 2026-09-16 -- création (S135z-ccweb) : première spec avec termes français inventés (Tuile / Bandeau / Formule dans Tuile), corrigée en v2.0.0 après feedback David.
