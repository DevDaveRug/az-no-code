# Interface Airtable "Portfolio" -- table `AZ_Portfolio`

Version : 4.0.0
Date : 2026-09-19
Session : S135z-ccweb (création) puis S136z-ccdd (correction contre l'UI réelle de David)

> **MAJ S136z-ccdd** : la section "pas à pas" v3.0.0 ci-dessous décrivait un Interface Designer générique sourcé sur la doc Airtable Support, jamais vérifié contre le compte réel de David. Testé en conditions réelles S136z (captures d'écran à l'appui) : plusieurs termes/mécanismes ne correspondent pas. Section réécrite en v4.0.0 avec les VRAIS écrans observés. L'Interface Portfolio est déjà construite et fonctionnelle à cette date (3 Number, 1 Calendar, 1 Grid) -- ce document sert désormais de référence pour la MAINTENIR et l'ÉTENDRE, pas de guide de construction initiale.

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

## Interface Airtable -- ce qui est VRAIMENT à l'écran (vérifié S136z, captures David)

Onglet `Interfaces` (en haut, entre `Automatisations` et `Formulaires`). Sidebar gauche : liste des interfaces existantes + pages (`Portfolio Sales Closer`, `Accueil`, `Prendre un CR de RDV`, `Ajouter un nouveau Prospect`...).

**Il n'y a PAS de panneau latéral "Element picker" à glisser-déposer.** Le mécanisme réel : clique `+ Ajouter un groupe` au milieu de la page (ou en bas d'un groupe existant) -> un petit menu liste d'abord les **tables sources** (`SC_Prospects`, `SC_CRs_de_RDV`, `AZ_Inscrits`, `AZ_Portfolio`) -> puis une **rangée de boutons bleus** apparaît avec les éléments disponibles :

`+ Ajouter un numéro` (= Number) -- `+ Ajouter un graphique` (= Chart) -- `+ Ajouter une liste` (= List) -- `+ Ajouter un tableur` (= Grid) -- `+ Ajouter une galerie` (= Gallery) -- `+ Ajouter un kanban` (= Kanban) -- `+ Ajouter un calendrier` (= Calendar) -- `+ Ajouter un tableau croisé dynamique` (= Pivot table, pas dans la doc Airtable officielle utilisée en v3.0.0) -- `+ Ajouter une chronologie` (= Timeline) -- `+ Ajouter une extension personnalisée`.

Pas de `Text`, `Divider`, `Button`, `Filter` ou `Record picker` comme éléments séparés dans cette rangée -- ces "vrais termes officiels" v3.0.0 existent dans la doc Airtable générique mais pas comme boutons distincts dans la version de David. Les titres de section (`Portfolio Sales Closer Souverain`, `Dates Portfolio`, `Portfolio Public`) s'éditent en tapant directement sur le texte généré par défaut au-dessus de chaque groupe, pas via un élément `Text` à poser.

**Le `Properties panel` à droite existe bien**, avec les mêmes sections que prévu (`Données`, `Apparence`, `Actions d'utilisateur`) -- juste pas de section `Filters` séparée : le filtrage vit DANS `Données` (`Filtrer par`, un filtre fixé par David) et DANS `Actions d'utilisateur` (`Filtre`, un interrupteur qui donne un contrôle de filtrage au visiteur de la page).

### État réel au 2026-09-19 (déjà construit, fonctionnel, publié)

Page `Portfolio Sales Closer` :

-> 2 `Number` : `Projets livrés` (2, source vue `Portfolio public`, calcul `Record count`) et `Prochaine livraison` (1, source vue `En cours + À faire`, agrégation `Min` sur `DateLivraison`). Un 3e Number `Semaines livrées` a été retiré (redondant avec `Projets livrés` dans le modèle actuel 1 projet = 1 semaine).

-> 1 `Calendar` ("Dates Portfolio") : source `AZ_Portfolio`, affiche les 3 projets positionnés sur `DateLivraison`.

-> 1 `Grid` ("Portfolio Public") : source vue `Portfolio public (livrés uniquement)`, colonnes `Nom du projet`, `DateLivraison`, `Statut`, `EnonceCourt`. Filtrage `Filtrer par` laissé sur `Aucun` (déjà filtré en amont par la vue Airtable, redondant de filtrer deux fois). Toggle `Cliquer pour accéder aux détails de l'entrée` (section `Actions d'utilisateur`) à activer pour obtenir une fiche détail automatique par clic sur une ligne -- pas besoin de construire une page dédiée séparée à ce stade (3 projets).

-> Publication : `Publier` en haut à droite. Partage externe : `Partager l'interface`.

### Étendre l'interface pour un futur projet (4, 5...)

1- Ajoute une ligne dans `AZ_Portfolio` (table, pas Interface) avec tous les champs remplis, `Statut = Livré` une fois prêt.

2- Le `Number` `Projets livrés`, le `Calendar` et le `Grid` s'auto-mettent à jour (ils lisent la vue filtrée, aucune action sur l'Interface).

3- Aucune reconstruction d'Interface nécessaire -- c'est le principe même du Dashboard connecté aux vues.

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

-> 4.0.0 -- 2026-09-19 (S136z-ccdd) : section "pas à pas" entièrement réécrite après vérification contre l'UI réelle de David (3 captures d'écran fournies). Corrections : pas de panneau latéral "Element picker" (c'est une rangée de boutons "+ Ajouter un X" après avoir choisi la table source), pas d'éléments séparés `Text`/`Divider`/`Button`/`Filter`/`Record picker` (titres tapés directement, filtrage intégré aux sections `Données`/`Actions d'utilisateur` d'un élément existant), élément `Pivot table` disponible mais absent de la doc v3.0.0. État réel documenté : Interface déjà construite et publiée (2 Number + 1 Calendar + 1 Grid), tuile "Semaines livrées" retirée (redondante), détail projet via toggle natif plutôt que page dédiée séparée.

-> 3.0.0 -- 2026-09-16 -- correction termes officiels (S135z-ccweb, Cor David) : bascule des termes français inventés (Nombre / Grille / Chronologie / Bouton / Texte / Filtre / "Tableau de bord recommandé") vers les VRAIS termes anglais officiels sourcés sur la doc Airtable Support 2026 (`Number` / `Grid` / `Timeline` / `Button` / `Text` / `Filter` / `Dashboard`). Ajout section "Termes Airtable utilisés dans cette spec" pointant vers `dr-context/docs/DR/DR_Medias/Me_Logiciels/MeLc_Plateformes/MeLcPf_Airtable/AIRTABLE_INTERFACE_TERMS.md` (doc de référence unique). Réécriture précise Page 1 + Page 2 avec Element picker (panneau gauche), Properties panel (panneau droit, sections Data / Appearance / User actions), types de calcul du `Number` (`Record count`, `Field summary` avec agrégation `Min`), action du `Button` (`Go to URL in record`). Précision sur SPA JS Airtable (WebFetch KO -> captures d'écran nécessaires pour audit).

-> 2.0.0 -- 2026-09-16 -- refonte (S135z-ccweb, Cor David) : renommage `AZ_Defis` -> `AZ_Portfolio`, "Défi" -> "Projet", disparition mentions Alegria/Eva du contenu public, ajout champs `Pitch` + `Lien_Airtable_Demo` + `Lien_NocoDB_Demo` (portfolio-friendly). Récriture complète de la section Layout avec les termes français traduits (v2 corrigée en v3 car termes non officiels).

-> 1.0.0 -- 2026-09-15 -- création initiale sous le nom `DEFIS_ALEGRIA_INTERFACE.md` (S135z-ccweb).

