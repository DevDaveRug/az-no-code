# Airtable - Masterclass Inscriptions

Structure complète prête à recréer manuellement dans Airtable en 15 minutes, ou à importer via API. L'objectif du défi : chaque nouvel inscrit ajouté déclenche l'envoi automatique d'un email de confirmation personnalisé (prénom + date).

## Base

Nom de la base : `Masterclass Inscriptions`

## Table AZ_Inscrits

Champs à créer dans l'ordre :

| Champ | Type | Notes |
|---|---|---|
| `Nom complet` | Formula | `{Prenom}` (champ principal, l'énoncé ne demande que le prénom) |
| `Prenom` | Single line text | Requis |
| `Email` | Email | Requis, format email |
| `DateMasterclass` | Date | ISO, sans heure. Format d'affichage FR (`14/10/2026`) via view settings. |
| `StatutEmail` | Single select | Options : `En attente` (gris, default), `Envoyé` (vert), `Erreur` (rouge). Rempli par l'automatisation. |
| `DateInscription` | Created time | Auto |
| `Notes` | Long text | Optionnel |

Champ principal (colonne affichée dans les liens/vues) : `Nom complet`.

## Vues

### Vue 1 - Grid "Tous les inscrits" (par défaut)

Type : Grid

Tri : `DateInscription` décroissant (les plus récents en haut)

Champs affichés : tous

### Vue 2 - Grid "Par masterclass"

Type : Grid

Groupement : `DateMasterclass` croissant

Tri interne : `Prenom` ascendant

Utile pour voir "qui vient à la session du 14 octobre".

### Vue 3 - Grid "Erreurs d'envoi"

Type : Grid

Filtre : `StatutEmail` = `Erreur`

Sert de vue "à traiter" -- l'automatisation a échoué, David regarde pourquoi.

## Formulaire d'ajout

Type : Form view sur `AZ_Inscrits`

Nom : "Inscription masterclass"

Champs visibles :

- `Prenom` (requis)

- `Email` (requis)

- `DateMasterclass` (requis, préremplir avec la prochaine session si possible)

Champs cachés (défaut) :

- `StatutEmail` : `En attente`

Message post-soumission : "Merci pour ton inscription ! Un email de confirmation arrive dans quelques secondes."

URL du formulaire : à partager (LinkedIn, site, page Alegria).

## Automatisation (LE cœur du défi)

Nom : "Envoi email de confirmation"

Déclencheur : `When record is created` sur la table `AZ_Inscrits`

Étape 1 - Send email :

- To : `{{Email}}` (dynamique, tiré du record déclencheur)

- From : email vérifié Airtable (par défaut `noreply@airtable.com`, ou domaine custom si vérifié via SendGrid/Postmark)

- Subject : `Ta place pour la masterclass du {{DateMasterclass}} est confirmée`

- Body (texte, PAS de bloc de code -- accents obligatoires) :

```
Salut {{Prenom}},

Ta place pour la masterclass du {{DateMasterclass}} est confirmée.

Je t'envoie le lien de connexion + le programme détaillé quelques jours avant.

Si tu as la moindre question d'ici là, réponds simplement à cet email.

À très vite,
David
```

Étape 2 - Update record :

- Table : `AZ_Inscrits`

- Record ID : `{{recordId}}` (celui déclencheur)

- `StatutEmail` : `Envoyé`

Gestion d'erreur (optionnelle mais recommandée) :

- Sur "Send email" -> configurer "Continue on failure"

- Ajouter une 3ème étape "Update record" qui met `StatutEmail` = `Erreur` si le step 1 échoue (utiliser le résultat conditionnel du step précédent)

## Import via API (référence, PAS via UI)

Le fichier `schema.json` de ce dossier documente la structure au format API Airtable Metadata (POST `/v0/meta/bases/{baseId}/tables`). **Il n'est PAS importable depuis l'UI Airtable** (le dialog "Ajouter des données" ne le reconnaît pas). Il sert :

-> de référence formelle pour la structure (utile si tu écris un script d'automatisation)

-> de base pour un futur script `az-code/scripts/airtable-import.mjs` qui appellerait l'API avec ton token

Pour recréer la table à la main dans l'UI, suivre la section "Table AZ_Inscrits" ci-dessus (~8 min).

## Enregistrer ce défi dans AZ_Defis + Interface Alegria

Après création + captures, ajouter une ligne dans la table `AZ_Defis` de la base `Sales Closer Souverain`, et vérifier que le défi apparaît dans l'Interface "Défis Alegria". Spec complète : `az-no-code/interfaces/DEFIS_ALEGRIA_INTERFACE.md`.

## Exemples de données

Voir `exemples.md` -- 5 inscrits, dont David lui-même (le test explicitement demandé par l'énoncé). Emails toujours en `.example` dans le repo public.

**Variante base perso David** : dans la base `Sales Closer Souverain`, David peut ajouter un champ `LinkedProspect` de type Enregistrement lié vers `SC_Prospects` pour rattacher chaque inscrit à un prospect réel sans exposer son email (l'email reste en `.example` dans la colonne Email, le lien fournit l'accès au contact réel via le record link). Les captures Alegria doivent alors flouter la colonne Email si un vrai email s'y trouve.

## Captures d'écran attendues (dans `captures/`)

- `01_base_grille.png` -- Table `AZ_Inscrits` peuplée avec les 5 inscrits

- `02_vue_par_masterclass.png` -- Regroupement par date de masterclass

- `03_formulaire.png` -- Vue du form "Inscription masterclass"

- `04_automatisation_config.png` -- Config de l'automation (déclencheur + step Send email + step Update record)

- `05_automatisation_run.png` -- Historique d'exécutions (au moins 1 run réussi)

- `06_email_recu.png` -- Capture de l'email de confirmation reçu dans la boite (David a reçu son propre email de test)
