# Interface Airtable "Portfolio" -- table `AZ_Portfolio`

Version : 2.0.0
Date : 2026-09-16
Session : S135z-ccweb

## But

Portefeuille des projets souverains livrés (ou en cours) pour donner envie à un prospect qui découvre `salescloser.fr` ou reçoit un lien direct. Chaque projet montre : ce qu'il résout, sur quelle stack, où on peut le voir tourner.

Cette interface se pilote depuis Airtable Interface Designer (onglet `Interfaces` en haut à droite de la base). Aucune formule à écrire à la main : tous les calculs se font avec des dropdowns natifs.

## Base

`Sales Closer Souverain` (base perso David, ID `appTqLo3JDg7d1fak`).

## Table `AZ_Portfolio` -- structure

Nom principal : `AZ_Portfolio`. Une ligne par projet livré (ou en cours).

| Champ | Type Airtable | Notes |
|---|---|---|
| `Nom du projet` | Formule (Formula) | `"Projet " & {Numéro} & " -- " & {Slug}` -- champ principal |
| `Numéro` | Nombre (Number, integer) | 1, 2, 3, ... ordinal |
| `Slug` | Ligne de texte (Single line text) | `crm-souverain`, `masterclass-inscriptions`, ... |
| `Semaine` | Nombre (Number, integer) | ISO week (36, 37, 38...) |
| `AnnéeSaison` | Sélection unique (Single select) | `2026-Automne`, `2026-Hiver`... |
| `Client` | Ligne de texte (Single line text) | générique ou anonymisé : "Formateur en ligne", "Consultant BTP"... |
| `DateLivraison` | Date | vendredi de la semaine |
| `Statut` | Sélection unique (Single select) | `Livré` (vert) / `En cours` (bleu) / `À faire` (gris) / `Bonus` (violet) |
| `Pitch` | Texte long (Long text) | 2-3 lignes prospect-friendly : le problème + la solution |
| `TablesConcernées` | Texte long (Long text) | "AZ_Inscrits, SC_Prospects" ou description libre |
| `AutomationsConcernées` | Texte long (Long text) | ex : "Envoi email de confirmation à l'inscription" |
| `Repo_NoCode` | URL | `github.com/DevDaveRug/az-no-code/tree/main/defis/{slug}` |
| `Repo_Code` | URL | `github.com/DevDaveRug/az-code/tree/main/defis/{slug}` |
| `Preview_Vercel` | URL | URL live du MVP Next.js après déploiement |
| `Lien_Airtable_Demo` | URL | lien partagé public de la vue ou du formulaire Airtable |
| `Lien_NocoDB_Demo` | URL | URL publique NocoDB self-host |
| `Captures` | Pièce jointe (Attachment) | screenshots démo |
| `AngleCommercial` | Texte long | argument à raconter à un prospect |
| `Notes` | Texte long | apprentissages, choix techniques |
| `DateAjout` | Heure de création (Created time) | auto |

## Vues à créer sur `AZ_Portfolio`

Créer via `Créer une vue` en bas à gauche de la table. 5 vues :

-> **Grille "Tous les projets"** (défaut) -- tri `Numéro` desc, champs affichés : tous

-> **Grille "En cours + À faire"** -- filtre `Statut` est `En cours` OU `À faire`, tri `DateLivraison` asc

-> **Grille "Portfolio public (livrés uniquement)"** -- filtre `Statut` est `Livré`, champs affichés : `Nom du projet`, `Semaine`, `Client`, `Pitch`, `Repo_NoCode`, `Repo_Code`, `Preview_Vercel`, `Lien_Airtable_Demo`, `Lien_NocoDB_Demo`, `Captures`, `AngleCommercial`

-> **Kanban "Statut"** -- regroupement par `Statut`

-> **Chronologie "Livraisons"** -- champ date `DateLivraison`, coloration par `Statut`

## 3 lignes initiales à ajouter

Copie-colle ce bloc via `Coller les données du tableau` -> choisir `Ajouter dans une table existante` -> `AZ_Portfolio`. Emails et clients anonymisés / génériques pour rester "portfolio-friendly".

```
Numéro	Slug	Semaine	AnnéeSaison	Client	DateLivraison	Statut	Pitch	Repo_NoCode	Repo_Code
1	crm-souverain	36	2026-Automne	Consultant B2B	2026-09-05	Livré	Un CRM prospects avec vue "à relancer" (dernière relance > 7 jours) et email récap hebdo. Idéal quand tu perds tes prospects dans un fichier Excel.	https://github.com/DevDaveRug/az-no-code/tree/main/defis/crm-souverain	https://github.com/DevDaveRug/az-code/tree/main/defis/crm-souverain
2	cr-rdv-formate	37	2026-Automne	Coach / formateur	2026-09-12	Livré	Un compte-rendu de RDV brut devient une synthèse structurée en 2 colonnes (verbatim + reformulation). Économise 45 min de reprise après chaque appel.	https://github.com/DevDaveRug/az-no-code/tree/main/defis/cr-rdv-formate	https://github.com/DevDaveRug/az-code/tree/main/defis/cr-rdv-souverain
3	masterclass-inscriptions	38	2026-Automne	Organisateur d'événements	2026-09-19	Livré	Un formulaire d'inscription à une masterclass qui envoie automatiquement l'email de confirmation personnalisé (prénom + date). Fini l'heure par jour à envoyer les mails à la main.	https://github.com/DevDaveRug/az-no-code/tree/main/defis/masterclass-inscriptions	https://github.com/DevDaveRug/az-code/tree/main/defis/masterclass-inscriptions
```

Champs à remplir manuellement après import :

-> `Nom du projet` (Formule) : formule = `"Projet " & {Numéro} & " -- " & {Slug}`, puis clique droit sur le champ -> `Utiliser comme champ principal`

-> `DateAjout` (Heure de création) : auto

-> `Captures` (Pièces jointes) : upload manuel des screenshots

-> `Preview_Vercel`, `Lien_Airtable_Demo`, `Lien_NocoDB_Demo` : à remplir quand les démos live sont prêtes

## Interface Airtable -- pas à pas avec VRAIS termes

Onglet `Interfaces` (en haut à droite de la base, entre `Automatisations` et `Formulaires`). Deux cas :

-> **Cas A -- interface existante** : ouvre-la, clique sur `+ Ajouter une page` (bouton en haut à gauche du panneau des pages).

-> **Cas B -- première interface** : clique `Créer une interface` -> nomme-la `Sales Closer Souverain` -> confirme.

Dans les 2 cas, tu arrives sur le choix du layout de page.

### Page 1 -- "Portfolio" (page d'accueil de l'interface)

Layout à choisir : `Tableau de bord` (Dashboard).

Airtable te dépose une page vide. En haut à droite, clic sur `Modifier` (bouton crayon) pour entrer en mode édition.

Sur la barre latérale de gauche, tu vois la palette des **éléments** disponibles. On va en poser 6 :

**1- Élément `Texte` (Text)**

Drag & drop en haut de la page. Configure :

-> Contenu : `Portfolio Sales Closer Souverain`

-> Style : `Titre` (Heading 1) via le sélecteur

Résultat : gros titre en haut de la page. Sert d'ancrage.

**2- Élément `Nombre` (Number) x 3 (bandeau statistiques)**

Drag & drop 3 éléments Nombre côte à côte sous le titre. Pour chacun, panneau de configuration à droite :

-> **Nombre 1** : `Projets livrés`

   -> Source : sélectionne la table `AZ_Portfolio`, puis la vue `Portfolio public (livrés uniquement)`

   -> Type de calcul (dropdown) : `Nombre d'enregistrements` (Count)

   -> Label : "Projets livrés"

-> **Nombre 2** : `Prochaine livraison`

   -> Source : table `AZ_Portfolio`, vue `En cours + À faire`

   -> Type de calcul (dropdown) : `Min` (Minimum)

   -> Champ : `DateLivraison`

   -> Label : "Prochaine livraison"

-> **Nombre 3** : `Semaines livrées`

   -> Source : table `AZ_Portfolio`, vue `Portfolio public (livrés uniquement)`

   -> Type de calcul : `Nombre unique` (Count unique) sur champ `Semaine`

   -> Label : "Semaines livrées consécutives"

Aucune formule à taper : le calcul se choisit dans un dropdown. Airtable fait tout.

**3- Élément `Chronologie` (Timeline)**

Drag & drop sous les 3 tuiles.

-> Source : table `AZ_Portfolio`, vue `Tous les projets`

-> Champ date : `DateLivraison`

-> Coloration : par `Statut`

Résultat : ruban chronologique horizontal. Un prospect qui scroll voit la cadence des livraisons.

**4- Élément `Grille` (Grid)**

Drag & drop sous la chronologie.

-> Source : table `AZ_Portfolio`, vue `Portfolio public (livrés uniquement)`

-> Champs affichés : cocher `Nom du projet`, `Semaine`, `Pitch`, `Repo_NoCode`, `Repo_Code`, `Preview_Vercel`, `Captures`

-> Option `Permettre le clic sur une ligne pour ouvrir le détail` : activer

Résultat : le prospect voit la liste des projets, clique sur une ligne, atterrit sur la page détail (voir Page 2).

**5- Élément `Filtre` (Filter, optionnel)**

Drag & drop en haut, sous le titre. Permet au prospect de filtrer par `AnnéeSaison` ou `Client` s'il veut zoomer sur un type.

**6- Sauvegarde**

Bouton `Publier` en haut à droite. La page est live pour tous les collaborateurs de la base. Pour la partager avec un prospect externe (lecture seule), clic sur `Partager` -> `Créer un lien public`.

### Page 2 -- "Détail projet" (record summary)

Layout à choisir : `Résumé de l'enregistrement` (Record summary) OU `Vue de l'enregistrement` (Record review).

En haut de la page, Airtable te demande la source :

-> Source : table `AZ_Portfolio`

-> Vue : `Tous les projets`

Le layout t'affiche automatiquement un enregistrement à la fois avec un sélecteur en haut. Configure les éléments :

-> **Texte** (Titre 1) : bind sur le champ `Nom du projet`

-> **Texte** (Sous-titre) : bind sur le champ `Pitch`

-> **Boutons** (Button) : 3 boutons horizontaux

   -> Bouton 1 : label "Voir le repo no-code" -> action `Ouvrir URL` -> champ source `Repo_NoCode`

   -> Bouton 2 : label "Voir le repo code" -> action `Ouvrir URL` -> champ source `Repo_Code`

   -> Bouton 3 : label "Ouvrir la démo Vercel" -> action `Ouvrir URL` -> champ source `Preview_Vercel`

-> **Grille des captures** (Attachment gallery) : bind sur `Captures`

-> **Texte long** : bind sur `AngleCommercial` -- prospect voit l'argument de vente

-> **Métadonnées** : Semaine, Client, DateLivraison, Statut affichés en side panel

Publier. Depuis Page 1, quand un prospect clique une ligne, il atterrit ici.

### Comment ajouter chaque nouveau projet

À la fin d'un nouveau projet livré (ex : projet 4 la semaine prochaine) :

1- Va dans la base -> table `AZ_Portfolio` -> ajoute une ligne (bouton `+` en bas)

2- Remplis : Numéro (4), Slug, Semaine, AnnéeSaison, Client, DateLivraison, Statut (`Livré` une fois OK), Pitch, Repos, Preview_Vercel, Lien_Airtable_Demo, Lien_NocoDB_Demo, Captures, AngleCommercial

3- Le projet apparaît automatiquement dans la page Portfolio (via la vue filtrée `Livré`) et dans la Chronologie

4- Aucune action manuelle sur l'interface -- elle s'auto-alimente

## Angle "donner envie au prospect" -- checklist

À vérifier sur l'interface actuelle (2 premiers projets déjà là) et sur chaque nouveau projet :

-> **Pitch** court (2-3 lignes max) qui parle du problème réel du client, pas de la stack

-> **Preview Vercel** live cliquable -- un prospect qui n'a pas de compte doit pouvoir voir le résultat en 5 secondes

-> **Captures** avec une belle mise en scène (pas juste des screens bruts -- ajouter un titre, une flèche, une annotation)

-> **AngleCommercial** en une phrase qui répond à "pourquoi j'aurais besoin de ça"

-> **Repos GitHub** publics et lisibles (README à jour, screenshots dans le README)

-> **Pas d'accroche narcissique** ("j'ai construit" -> "voici la solution qu'un formateur peut réutiliser en 30 min")

-> **Pas de jargon technique dans le Pitch** ("Next.js + Neon + Prisma" -> "un système souverain que tu peux héberger toi-même")

Si tu m'envoies des captures de tes 2 premières pages projet, je te ferai un audit ligne à ligne "donne envie / à retravailler".

## Automatisations optionnelles

**Rappel J-2 avant livraison** -- automation Airtable : Cron quotidien 8h -> Find records où `DateLivraison` = TODAY() + 2 AND `Statut` != `Livré` -> Send email/Telegram/notif à David.

**Auto-update Statut sur PR merge** -- webhook GitHub Actions -> Airtable API : quand une PR `defi-<slug>` est mergée, update le record dans `AZ_Portfolio` (Statut = `Livré`, ajoute automatiquement la date de merge en DateLivraison si elle est vide).

## Partage Interface

L'interface Airtable Portfolio se partage via `Partager` en haut à droite -> `Créer un lien public` (lecture seule). URL stable tant que le partage n'est pas révoqué.

À utiliser :

-> dans le pied de mail signature David (`Portfolio : lien`)

-> sur `davidruggieri.com` ou `salescloser.fr` bloc "Ce que je peux te livrer"

-> dans les CTA prospects B2B ("Regarde 3 exemples concrets de systèmes que j'ai livrés")

-> comme argument LIA'M/SC quand un prospect demande "montre-moi ce que ça donne"

## Changelog

-> 2.0.0 -- 2026-09-16 -- refonte (S135z-ccweb, Cor David) : renommage `AZ_Defis` -> `AZ_Portfolio`, "Défi" -> "Projet", disparition mentions Alegria/Eva du contenu public, ajout champs `Pitch` + `Lien_Airtable_Demo` + `Lien_NocoDB_Demo` (portfolio-friendly). Récriture complète de la section Layout avec les VRAIS termes Airtable Interface Designer (Texte, Nombre, Chronologie, Grille, Filtre, Bouton, Résumé de l'enregistrement) -- plus aucune "tuile" ni "bandeau" (termes inventés), plus aucune formule à taper (dropdowns natifs). Ajout checklist "donner envie au prospect".

-> 1.0.0 -- 2026-09-15 -- création initiale sous le nom `DEFIS_ALEGRIA_INTERFACE.md` (S135z-ccweb).

