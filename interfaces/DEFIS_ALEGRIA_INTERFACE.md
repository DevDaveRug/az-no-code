# Interface Airtable "Défis Alegria" -- spec + procédure

Version : 1.0.0
Date : 2026-09-15
Session : S135z-ccweb

## But

Centraliser tous les défis hebdomadaires Alegria de David dans une interface Airtable unique -- vue chronologique + statistiques + accès rapide aux repos et captures. Sert :

-> de dashboard perso pour ne rien oublier d'une semaine à l'autre

-> de portfolio-démo à raconter à un vrai client B2B ("voici ce que j'ai construit sur des cas réels, en 30 min chacun")

-> de socle pour l'apprentissage transversal quand les défis se cumulent (patterns récurrents, briques réutilisables)

## Base concernée

`Sales Closer Souverain` (base perso David, ID `appTqLo3JDg7d1fak`).

## Table `AZ_Defis` -- structure

Créer une seule fois, puis y ajouter une ligne par défi terminé.

| Champ | Type | Notes |
|---|---|---|
| `Nom du défi` | Formule | `"Défi " & {Numéro} & " -- " & {Slug}` -- champ principal, sert d'ID lisible |
| `Numéro` | Nombre | 1, 2, 3, ... (Brique N°X côté ROADMAP az-code) |
| `Slug` | Ligne de texte | ex : `crm-souverain`, `cr-rdv-formate`, `masterclass-inscriptions` |
| `Semaine` | Nombre | ISO week (36, 37, 38...) |
| `AnnéeSaison` | Sélection unique | `2026-Automne`, `2026-Hiver`, `2027-Printemps`... |
| `Client` | Ligne de texte | ex : `Eva PRO / Alegria` |
| `DateLivraison` | Date | vendredi de la semaine ISO |
| `Statut` | Sélection unique | `À faire` (gris) / `En cours` (bleu) / `Livré` (vert) / `Bonus` (violet, pour défis étendus) |
| `EnonceCourt` | Texte long | 2-3 lignes : ce que le client a demandé |
| `TablesConcernées` | Enregistrement lié (multi) | vers `AZ_Defis_Tables` (table listant les tables de la base concernées par ce défi) -- OU texte multi-ligne si tu veux garder simple |
| `AutomationsConcernées` | Texte long | ex : "Send email on record created" |
| `Repo_NoCode` | URL | `github.com/DevDaveRug/az-no-code/tree/main/defis/{slug}` |
| `Repo_Code` | URL | `github.com/DevDaveRug/az-code/tree/main/defis/{slug}` |
| `Preview_Vercel` | URL | à remplir après provisionnement Vercel |
| `LienPartageAirtable` | URL | share form ou share view spécifique à ce défi |
| `Captures` | Pièce jointe | screenshots (miroir de `captures/` dans le repo) |
| `AngleCommercial` | Texte long | argument à raconter à un vrai client (2-3 lignes) |
| `Notes` | Texte long | apprentissages, choix techniques, pattern réutilisable |
| `DateAjout` | Heure de création | auto |

## Vues à créer sur `AZ_Defis`

### Vue 1 -- Grille "Tous les défis"

Type : Grid. Tri : `Numéro` décroissant (le plus récent en haut). Champs affichés : tous.

### Vue 2 -- Timeline "Chronologie livraisons"

Type : Timeline. Champ date : `DateLivraison`. Coloration : par `Statut`. Utile pour voir la cadence hebdo et anticiper les prochaines livraisons.

### Vue 3 -- Kanban "Statut"

Type : Kanban. Groupement : `Statut`. Ordre colonnes : À faire, En cours, Livré, Bonus. Sert de pipeline visuel pendant la semaine active.

### Vue 4 -- Grille "En cours + à faire"

Type : Grid. Filtre : `Statut` ∈ `En cours`, `À faire`. Tri : `DateLivraison` ascendant. Rappel de ce qui reste à faire.

### Vue 5 -- Grille "Portfolio (livrés uniquement)"

Type : Grid. Filtre : `Statut` = `Livré`. Champs affichés : `Nom du défi`, `Client`, `DateLivraison`, `EnonceCourt`, `AngleCommercial`, `Preview_Vercel`, `Captures`. C'est cette vue qu'on partage à un prospect qui demande "montre-moi ce que tu as fait".

## 3 lignes initiales à ajouter (défis déjà livrés)

```
Numéro	Slug	Semaine	AnnéeSaison	Client	DateLivraison	Statut	EnonceCourt	Repo_NoCode	Repo_Code
1	crm-souverain	36	2026-Automne	Eva PRO / Alegria	2026-09-05	Livré	CRM prospects avec relance 7 jours + vue à relancer + email récap hebdo	https://github.com/DevDaveRug/az-no-code/tree/main/defis/crm-souverain	https://github.com/DevDaveRug/az-code/tree/main/defis/crm-souverain
2	cr-rdv-formate	37	2026-Automne	Eva PRO / Alegria	2026-09-12	Livré	Formatage automatique de compte-rendus de RDV en 2 colonnes via prompt LLM	https://github.com/DevDaveRug/az-no-code/tree/main/defis/cr-rdv-formate	https://github.com/DevDaveRug/az-code/tree/main/defis/cr-rdv-souverain
3	masterclass-inscriptions	38	2026-Automne	Eva PRO / Alegria	2026-09-19	Livré	Formulaire d'inscription à une masterclass + email de confirmation automatique (prénom + date)	https://github.com/DevDaveRug/az-no-code/tree/main/defis/masterclass-inscriptions	https://github.com/DevDaveRug/az-code/tree/main/defis/masterclass-inscriptions
```

## Interface Airtable "Défis Alegria" -- procédure

Deux cas de figure :

### Cas A -- Exploiter une interface existante (préféré)

Si tu as déjà une interface Airtable ouverte pour `Sales Closer Souverain` (ex : dashboard SC prospects), tu ajoutes une page dédiée aux défis, pas une nouvelle interface.

1- Ouvrir l'interface existante -> bouton `+ Ajouter une page` (en haut à gauche)

2- Choisir layout `Tableau de bord` (Dashboard) -> nommer `Défis Alegria`

3- Source de données : table `AZ_Defis`

4- Ajouter les composants ci-dessous ("Layout tableau de bord")

### Cas B -- Créer nouvelle interface

Si aucune interface n'existe encore sur cette base :

1- Onglet `Interfaces` en haut à droite de la base -> `+ Créer une nouvelle interface`

2- Choisir `Tableau de bord`

3- Nommer `Sales Closer Souverain` (nom de la base -- cette interface hébergera plusieurs pages : Défis Alegria, plus tard aussi CRM, CRs, etc.)

4- Page 1 : `Défis Alegria`, source `AZ_Defis`

## Layout tableau de bord recommandé

Structure de la page "Défis Alegria" (composants Airtable Interface Designer) :

**Ligne 1 -- Bandeau statistiques (3 tuiles)**

-> Tuile 1 : `Défis livrés` = COUNT(records where Statut = "Livré")

-> Tuile 2 : `Prochaine livraison` = MIN(DateLivraison where Statut ∈ ["En cours", "À faire"])

-> Tuile 3 : `Cadence` = COUNT(records par semaine ISO courante)

**Ligne 2 -- Timeline (composant Timeline)**

-> Source : `AZ_Defis`

-> Champ date : `DateLivraison`

-> Coloration : `Statut`

-> Filtre : `AnnéeSaison` = courante (dynamique)

**Ligne 3 -- Grille "Tous les défis"**

-> Source : `AZ_Defis` vue `Tous les défis`

-> Colonnes visibles : Nom du défi, Numéro, Semaine, Client, DateLivraison, Statut, Repo_NoCode, Preview_Vercel

-> Clic sur ligne -> ouvre le panneau détail (Ligne 4)

**Ligne 4 -- Panneau détail (record detail)**

-> Affiche tous les champs du défi sélectionné

-> Boutons d'action rapide : `Ouvrir Repo No-code`, `Ouvrir Repo Code`, `Voir Preview`, `Voir Captures`

**Ligne 5 -- Formulaire "Nouveau défi"**

-> Composant `Formulaire` -> table `AZ_Defis`

-> Champs demandés : Numéro, Slug, Semaine, Client, DateLivraison, Statut, EnonceCourt

-> Permet à David d'ajouter un défi en 30 secondes sans quitter l'interface

## Automatisations utiles (optionnelles)

**Automation 1 -- Rappel J-2 avant livraison**

Déclencheur : Cron quotidien 8h -> Find records où `DateLivraison` = TODAY() + 2 days AND `Statut` != `Livré` -> Send Telegram/email à David : "Défi X à livrer dans 2 jours, statut actuel : Y"

**Automation 2 -- Auto-update Statut sur push GH**

Nécessite un webhook depuis GitHub Actions vers Airtable. Quand une PR `defi-<slug>` est mergée -> update record dans `AZ_Defis` : `Statut` = `Livré`. Hors scope du no-code pur, mais faisable en n8n.

## Partage lien Interface

L'interface Airtable est partageable via `Partager -> Créer un lien public`. Cette URL est stable tant que le partage n'est pas révoqué.

À utiliser dans :

-> le portfolio Sales Closer / davidruggieri.com (`/portfolio` -> "Voir mes 20 derniers défis")

-> les CTA sortants pour prospects B2B ("Voici 3 exemples de systèmes que j'ai construits en 30 min")

-> la page LIA'M / SC comme preuve de compétence tangible

## Changelog

-> 1.0.0 -- 2026-09-15 -- création initiale (S135z-ccweb) : spec table AZ_Defis, layout dashboard, 3 défis initiaux, procédure Cas A/B.
