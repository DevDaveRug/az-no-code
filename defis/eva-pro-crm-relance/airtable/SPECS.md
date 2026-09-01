# Airtable - CRM prospects Eva PRO

Structure complète prête à recréer manuellement dans Airtable en 15 minutes, ou à importer via API.

## Base

Nom de la base : `Eva PRO - CRM Prospects`

## Table SC_Prospects

Champs à créer dans l'ordre :

| Champ | Type | Notes |
|---|---|---|
| `Nom complet` | Formula | `{Prenom} & " " & {Nom}` (champ principal) |
| `Prenom` | Single line text | Requis |
| `Nom` | Single line text | Requis |
| `Entreprise` | Single line text | |
| `Email` | Email | Requis, format email |
| `Telephone` | Phone number | Format FR |
| `Statut` | Single select | Options : `Nouveau` (bleu), `En cours` (orange), `Gagné` (vert), `Perdu` (gris) |
| `Source` | Single select | Options : `LinkedIn`, `Recommandation`, `Site`, `Autre` |
| `Date entrée` | Date | Default = `TODAY()` |
| `Dernier contact` | Date | Rempli à chaque relance |
| `Prochaine relance` | Formula | `IF(OR({Statut}='Nouveau',{Statut}='En cours'), DATEADD({Dernier contact}, 7, 'days'), BLANK())` |
| `Jours restants` | Formula | `DATETIME_DIFF({Prochaine relance}, TODAY(), 'days')` (affichage rouge si négatif via conditional coloring) |
| `Notes` | Long text | |
| `Montant potentiel` | Currency (EUR) | Optionnel, utile pour Gagné |

Champ principal (celui qui s'affiche dans les liens et vues) : `Nom complet`.

## Vues

### Vue 1 - Grille "Tous les prospects" (par défaut)

Type : Grid

Tri : `Date entrée` décroissant

Champs affichés : tous

### Vue 2 - "À relancer" (la vue star du CRM)

Type : Grid

Filtre :

- `Statut` est `Nouveau` OU `En cours`

- ET `Prochaine relance` est `On or before today`

Tri : `Prochaine relance` croissant (les plus urgents en haut)

Champs affichés : `Nom complet`, `Entreprise`, `Statut`, `Dernier contact`, `Prochaine relance`, `Jours restants`, `Telephone`, `Email`

Coloration conditionnelle : ligne en rouge si `Jours restants < 0`, orange si `= 0`, jaune si `1-2`.

### Vue 3 - Calendrier "Relances"

Type : Calendar

Champ date : `Prochaine relance`

Filtre : `Statut` est `Nouveau` OU `En cours`

Coloration : par `Statut` (bleu Nouveau, orange En cours)

### Vue 4 - Kanban "Pipeline"

Type : Kanban

Groupement : `Statut`

Ordre des colonnes : Nouveau, En cours, Gagné, Perdu

Champs sur la carte : `Nom complet`, `Entreprise`, `Montant potentiel`, `Prochaine relance`

## Formulaire d'ajout

Type : Form view sur la table `SC_Prospects`

Nom : "Ajouter un prospect"

Champs visibles (dans cet ordre) :

- Prenom (requis)

- Nom (requis)

- Entreprise

- Email (requis)

- Telephone

- Source (dropdown, default vide)

- Notes

Champs cachés (auto-remplis) :

- Statut : Nouveau (default à la création)

- Date entrée : TODAY() (default)

- Dernier contact : TODAY() (default)

Message post-soumission : "Prospect ajouté. Tu peux le suivre depuis la vue Tous les prospects."

URL du formulaire : à partager avec Eva PRO ou intégrer sur un site.

## Automatisation

Nom : "Email hebdo - Prospects à relancer"

Déclencheur : At scheduled time

- Fréquence : Weekly

- Jour : Lundi

- Heure : 9:00 (Europe/Paris)

Étape 1 - Find records :

- Table : `SC_Prospects`

- Vue : `À relancer`

Étape 2 - Send email :

- To : email du owner (à saisir : `owner@exemple.com` ou l'email d'Eva PRO pour test)

- Subject : `[CRM] {{count}} prospects à relancer cette semaine`

- Body :

```
Bonjour,

Voici les prospects à relancer cette semaine :

{{#each records}}
- {{Nom complet}} ({{Entreprise}}) - dernier contact le {{Dernier contact}} - statut {{Statut}}
{{/each}}

Bonne semaine.
```

Condition (optionnelle) : ne pas envoyer si `count = 0`.

## Import via API (optionnel)

Le fichier `schema.json` de ce dossier contient la structure exportable via l'API Airtable Metadata (POST `/v0/meta/bases/{baseId}/tables`). Utile pour recréer la base ailleurs en une commande.

## Exemples de données

Voir `exemples.md` dans ce dossier - 4 lignes couvrant les 4 statuts + les 2 premières dans la vue "à relancer".

## Captures d'écran attendues (dans `captures/`)

- `01_base_grille.png` - Vue "Tous les prospects" avec les 4 exemples

- `02_vue_a_relancer.png` - Vue "À relancer" avec Alice + Bob en rouge/orange

- `03_calendrier.png` - Vue calendrier avec les relances positionnées

- `04_kanban.png` - Vue Kanban avec les 4 colonnes remplies

- `05_formulaire.png` - Vue du formulaire d'ajout

- `06_automatisation.png` - Config de l'automatisation email hebdo
