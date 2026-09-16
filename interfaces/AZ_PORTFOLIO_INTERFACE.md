# Interface Airtable "Portfolio" -- table `AZ_Portfolio`

Version : 3.0.0
Date : 2026-09-16
Session : S135z-ccweb

## But

Portefeuille des projets souverains livrés (ou en cours) pour donner envie à un prospect qui découvre `salescloser.fr` ou reçoit un lien direct. Chaque projet montre : ce qu'il résout, sur quelle stack, où on peut le voir tourner.

Cette interface se pilote depuis Airtable Interface Designer (onglet `Interfaces` en haut à droite de la base). Aucune formule à écrire à la main : tous les calculs se font avec des dropdowns natifs.

## Termes Airtable utilisés dans cette spec

Tous les termes techniques (Element picker, Properties panel, Number, Grid, Timeline, Button, Text, Dashboard...) sont les VRAIS termes anglais officiels de l'Airtable Interface Designer, sourcés sur la doc officielle Airtable Support (septembre 2026). Doc de référence unique : `dr-context/docs/DR/DR_Medias/Me_Logiciels/MeLc_Plateformes/MeLcPf_Airtable/AIRTABLE_INTERFACE_TERMS.md`.

Si l'UI Airtable de David est en français, les traductions sont fournies entre parenthèses, mais la logique et la position dans l'UI sont identiques. Le plus sûr en cas de doute : basculer l'UI en anglais dans les préférences Airtable pour retrouver exactement les termes ci-dessous.

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

## Interface Airtable -- pas à pas avec les VRAIS termes officiels

Onglet `Interfaces` (en haut à droite de la base, entre `Automations` et `Forms`). Deux cas :

-> **Cas A -- Interface existante** : ouvre-la, clique sur `+ Add page` (bouton en haut à gauche du panneau des pages).

-> **Cas B -- première Interface** : clique `Create interface` -> nomme-la `Sales Closer Souverain` -> confirme.

Dans les 2 cas, tu arrives sur le choix du `Layout` de page.

### Page 1 -- "Portfolio" (page d'accueil de l'Interface)

Layout à choisir : `Dashboard`.

Airtable te dépose une page vide. En haut à droite, clic sur `Edit` (icône crayon) pour entrer en mode édition.

Deux panneaux apparaissent :

-> **Element picker à gauche** : palette des Elements posables via drag & drop (`Grid`, `Number`, `Chart`, `Timeline`, `Gallery`, `Kanban`, `Text`, `Divider`, `Button`, `Filter`, `Record picker`, `Comment`).

-> **Properties panel à droite** : apparaît quand tu sélectionnes un Element posé, avec sections `Data`, `Appearance`, `User actions`, `Advanced`, `Filters` selon le type.

On va poser 6 Elements dans l'ordre :

**1- Element `Text`**

Drag & drop depuis l'Element picker (icône `T`) en haut de la page. Dans le Properties panel :

-> Style : `Heading 1`

-> Contenu : `Portfolio Sales Closer Souverain`

Résultat : titre en haut de la page.

**2- 3 x Element `Number` côte à côte (ligne de stats)**

Drag & drop 3 fois l'Element `Number` (icône `123`) et aligne-les horizontalement. Chacun se configure indépendamment :

-> **Number 1 -- `Projets livrés`**

   -> Section `Data` : Source = table `AZ_Portfolio`, vue `Portfolio public (livrés uniquement)`

   -> Type de calcul (dropdown en haut du Properties panel) : `Record count`

   -> Section `Appearance` : Label = "Projets livrés"

-> **Number 2 -- `Prochaine livraison`**

   -> Section `Data` : Source = `AZ_Portfolio`, vue `En cours + À faire`

   -> Type de calcul : `Field summary` -> champ `DateLivraison` -> agrégation `Min` (dans le dropdown)

   -> Label : "Prochaine livraison"

-> **Number 3 -- `Semaines livrées`**

   -> Section `Data` : Source = `AZ_Portfolio`, vue `Portfolio public (livrés uniquement)`

   -> Type de calcul : `Record count` (chaque projet = une semaine unique dans notre modèle -- si un jour deux projets partagent la même semaine, il faudra ajouter un champ `Formula` de type comptage unique dans la table)

   -> Label : "Semaines livrées"

Aucune formule à taper : tout se choisit dans les dropdowns du Properties panel. Airtable fait le calcul.

**3- Element `Timeline`**

Drag & drop sous la ligne de 3 `Number`.

Note : nécessite un plan Airtable payant. Si absent, remplacer par un layout `Calendar` sur une autre page.

-> Section `Data` : Source = `AZ_Portfolio`, vue `Tous les projets`

-> Champ date : `DateLivraison`

-> Section `Appearance` : Coloration = par champ `Statut`

Résultat : ruban chronologique horizontal. Un prospect qui scroll voit la cadence des livraisons.

**4- Element `Grid`**

Drag & drop sous la Timeline.

-> Section `Data` : Source = `AZ_Portfolio`, vue `Portfolio public (livrés uniquement)`

-> Section `Appearance` : cocher les champs à afficher = `Nom du projet`, `Semaine`, `Pitch`, `Repo_NoCode`, `Repo_Code`, `Preview_Vercel`, `Captures`

-> Section `User actions` : activer `Allow record detail view` (ouvre la page détail au clic sur une ligne, cf Page 2)

Résultat : le prospect voit la liste des projets, clique sur une ligne, atterrit sur la page détail.

**5- Element `Filter` (optionnel)**

Drag & drop en haut, sous le titre. Dans le Properties panel :

-> Style : `Tabs` ou `Dropdown`

-> Champ filtré : `AnnéeSaison` ou `Client`

Permet au prospect de filtrer par saison ou secteur.

**6- Sauvegarde**

Bouton `Publish` en haut à droite. La page est live pour tous les collaborateurs de la base. Pour la partager avec un prospect externe (lecture seule), clic `Share` -> `Create a shareable link`.

### Page 2 -- "Détail projet"

Layout à choisir : `Record review` (parcourir un record à la fois via un sélecteur en haut).

Alternative moderne (si disponible dans ta version Airtable) : layout `Record summary` ou `Grid` avec `Allow record detail view` activé sur Page 1 -- Airtable génère alors une page détail automatique.

En haut de la page (en mode édition), Airtable te demande la source :

-> Source : table `AZ_Portfolio`

-> Vue : `Tous les projets`

Configure les Elements du layout `Record review` :

-> **Text** (Heading 1) : bind sur le champ `Nom du projet`

-> **Text** (Subtitle) : bind sur le champ `Pitch`

-> **3 x Button** horizontaux :

   -> Button 1 -- Section `Data` : Action = `Go to URL in record` -> champ source = `Repo_NoCode`. Section `Appearance` : Label = "Voir le repo no-code"

   -> Button 2 -- Action = `Go to URL in record` -> champ = `Repo_Code`. Label = "Voir le repo code"

   -> Button 3 -- Action = `Go to URL in record` -> champ = `Preview_Vercel`. Label = "Ouvrir la démo Vercel"

-> **Field** `Captures` (type Attachment) : affiché automatiquement en galerie de miniatures par Airtable

-> **Field** `AngleCommercial` (Long text) : affiché en pleine largeur -- le prospect voit l'argument de vente

-> **Métadonnées** dans le side panel : `Semaine`, `Client`, `DateLivraison`, `Statut`

`Publish`. Depuis Page 1, quand un prospect clique une ligne, il atterrit ici.

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

L'Interface Airtable Portfolio se partage via `Share` en haut à droite -> `Create a shareable link` (mode lecture seule). URL au format `https://airtable.com/app.../shr...`, stable tant que le partage n'est pas révoqué.

Attention : le lien public est une SPA JavaScript. Un WebFetch serveur (agent IA, curl) ne le rend pas -- il retourne une page "browser not supported". Pour un audit visuel du Portfolio -> me fournir des captures d'écran, pas juste l'URL.

À utiliser :

-> dans le pied de mail signature David (`Portfolio : lien`)

-> sur `davidruggieri.com` ou `salescloser.fr` bloc "Ce que je peux te livrer"

-> dans les CTA prospects B2B ("Regarde 3 exemples concrets de systèmes que j'ai livrés")

-> comme argument LIA'M/SC quand un prospect demande "montre-moi ce que ça donne"

## Changelog

-> 3.0.0 -- 2026-09-16 -- correction termes officiels (S135z-ccweb, Cor David) : bascule des termes français inventés (Nombre / Grille / Chronologie / Bouton / Texte / Filtre / "Tableau de bord recommandé") vers les VRAIS termes anglais officiels sourcés sur la doc Airtable Support 2026 (`Number` / `Grid` / `Timeline` / `Button` / `Text` / `Filter` / `Dashboard`). Ajout section "Termes Airtable utilisés dans cette spec" pointant vers `dr-context/docs/DR/DR_Medias/Me_Logiciels/MeLc_Plateformes/MeLcPf_Airtable/AIRTABLE_INTERFACE_TERMS.md` (doc de référence unique). Réécriture précise Page 1 + Page 2 avec Element picker (panneau gauche), Properties panel (panneau droit, sections Data / Appearance / User actions), types de calcul du `Number` (`Record count`, `Field summary` avec agrégation `Min`), action du `Button` (`Go to URL in record`). Précision sur SPA JS Airtable (WebFetch KO -> captures d'écran nécessaires pour audit).

-> 2.0.0 -- 2026-09-16 -- refonte (S135z-ccweb, Cor David) : renommage `AZ_Defis` -> `AZ_Portfolio`, "Défi" -> "Projet", disparition mentions Alegria/Eva du contenu public, ajout champs `Pitch` + `Lien_Airtable_Demo` + `Lien_NocoDB_Demo` (portfolio-friendly). Récriture complète de la section Layout avec les termes français traduits (v2 corrigée en v3 car termes non officiels).

-> 1.0.0 -- 2026-09-15 -- création initiale sous le nom `DEFIS_ALEGRIA_INTERFACE.md` (S135z-ccweb).

