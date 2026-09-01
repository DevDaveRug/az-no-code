# Guide pas à pas -- Créer la base Airtable CRM Souverain (défi Alegria Eva PRO)

Public : David, à partir de zéro sur airtable.com, sans expérience préalable de l'API Airtable.

Deux voies possibles :

- **Voie A -- Import via API Airtable Metadata** (rapide, ~10 min, nécessite un Personal Access Token)

- **Voie B -- Création manuelle depuis SPECS.md** (~25 min, aucun token nécessaire)

Les deux voies aboutissent au même résultat.

## Voie A -- Import via API Airtable Metadata

### Étape 1 -- Créer un compte Airtable si tu n'en as pas

-> https://airtable.com/signup

-> Plan gratuit suffit pour ce défi (limite : 1200 lignes par base, aucun problème pour 4 exemples)

### Étape 2 -- Créer une base vide dans un workspace

-> Se rendre sur https://airtable.com/

-> Cliquer sur `+ Create a base` -> `Start from scratch`

-> Nommer la base : `CRM Souverain`

-> Choisir un workspace (par défaut : `Personal workspace`)

-> La base contient une table `Table 1` par défaut, la SUPPRIMER après l'import (voir étape 5)

-> Noter le `baseId` de la base :

  1. Ouvre la base

  2. Regarde l'URL dans le navigateur, elle a le format : `https://airtable.com/appXXXXXXXXXX/tblYYYYYYY/...`

  3. La partie `appXXXXXXXXXX` est ton `baseId`. Le noter dans un fichier temporaire.

### Étape 3 -- Créer un Personal Access Token

-> Se rendre sur https://airtable.com/create/tokens

-> Cliquer sur `+ Create new token`

-> Nom : `CRM Souverain S131c` (ou ce que tu veux)

-> Scopes (permissions) à cocher :

  - `data.records:read`

  - `data.records:write`

  - `schema.bases:read`

  - `schema.bases:write`

-> Access : `Add a base` -> sélectionner ta base `CRM Souverain`

-> Cliquer `Create token`

-> **COPIER LE TOKEN AFFICHÉ** (il ne sera plus visible après). Format : `patXXXXXXXXXXXXXX.YYYYYYYYYYYYYY...`

-> Le mettre dans ton fichier temporaire.

### Étape 4 -- Importer le schéma via API

Ouvre PowerShell dans C:/Users/conta/dev/az-no-code/defis/crm-souverain/airtable/ et exécute la commande suivante (remplacer `TON_BASE_ID` et `TON_TOKEN` par les valeurs réelles) :

Pour créer la table `SC_Prospects` avec ses champs :

```bash
curl -X POST "https://api.airtable.com/v0/meta/bases/TON_BASE_ID/tables" \
  -H "Authorization: Bearer TON_TOKEN" \
  -H "Content-Type: application/json" \
  -d @- <<'EOF'
{
  "name": "SC_Prospects",
  "description": "Prospects avec statut et relance à 7 jours (defi crm-souverain, origine Alegria)",
  "fields": [
    {"name": "Prenom", "type": "singleLineText"},
    {"name": "Nom", "type": "singleLineText"},
    {"name": "Entreprise", "type": "singleLineText"},
    {"name": "Email", "type": "email"},
    {"name": "Telephone", "type": "phoneNumber"},
    {"name": "Statut", "type": "singleSelect", "options": {"choices": [{"name": "Nouveau"}, {"name": "En cours"}, {"name": "Gagné"}, {"name": "Perdu"}]}},
    {"name": "Source", "type": "singleSelect", "options": {"choices": [{"name": "LinkedIn"}, {"name": "Recommandation"}, {"name": "Site"}, {"name": "Autre"}]}},
    {"name": "Date entrée", "type": "date", "options": {"dateFormat": {"name": "iso"}}},
    {"name": "Dernier contact", "type": "date", "options": {"dateFormat": {"name": "iso"}}},
    {"name": "Notes", "type": "multilineText"},
    {"name": "Montant potentiel", "type": "currency", "options": {"precision": 2, "symbol": "€"}}
  ]
}
EOF
```

Réponse attendue : JSON avec `id` du nouveau table (`tblZZZZZZZ`). Noter cet id, on va s'en servir juste après.

Note : les champs formule (`Nom complet`, `Prochaine relance`, `Jours restants`) sont ajoutés manuellement à l'étape 5 car l'API Airtable Metadata ne supporte PAS la création de formulas via `fields.type = "formula"` (Airtable limitation). Ajout manuel = 2 minutes.

### Étape 5 -- Ajouter les champs formule à la main + finaliser

Dans l'interface Airtable, ouvre la table `SC_Prospects` :

-> Ajouter un champ à la fin (`+` en bout de ligne d'en-tête) :

  - Nom : `Nom complet`

  - Type : `Formula`

  - Formule : `{Prenom} & " " & {Nom}`

-> Ajouter un autre champ :

  - Nom : `Prochaine relance`

  - Type : `Formula`

  - Formule : `IF(OR({Statut}='Nouveau',{Statut}='En cours'), DATEADD({Dernier contact}, 7, 'days'), BLANK())`

  - Format d'affichage : Date (iso)

-> Ajouter un autre champ :

  - Nom : `Jours restants`

  - Type : `Formula`

  - Formule : `IF({Prochaine relance}, DATETIME_DIFF({Prochaine relance}, TODAY(), 'days'), BLANK())`

-> Faire du champ `Nom complet` le champ principal (Right-click sur la colonne -> `Make primary`)

-> **Supprimer la table par défaut `Table 1`** (Right-click sur son onglet -> `Delete table`)

### Étape 6 -- Créer les 4 vues (Grille / À relancer / Calendrier / Kanban)

Voir `airtable/SPECS.md` section Vues pour les filtres exacts. En résumé :

-> Vue par défaut `Grid view` : renommer en `Tous les prospects`, tri par `Date entrée` desc

-> Nouvelle vue `Grid` : nommer `À relancer`, filtres :

  - `Statut` is `Nouveau` OR `En cours`

  - AND `Prochaine relance` is On or before today

  - Tri : `Prochaine relance` asc

-> Nouvelle vue `Calendar` : nommer `Relances`, champ date = `Prochaine relance`, filtre : `Statut` in Nouveau, En cours

-> Nouvelle vue `Kanban` : nommer `Pipeline`, group by `Statut`

### Étape 7 -- Injecter les 4 lignes d'exemples anonymisés

Ouvrir `airtable/exemples.md` dans un onglet à côté, puis dans Airtable (vue `Tous les prospects`) :

-> Alice Martin (Nouveau, dernier contact 2026-08-25)

-> Bob Durand (En cours, dernier contact 2026-08-20)

-> Chloé Dubois (Gagné, dernier contact 2026-08-30)

-> Emma Petit (Perdu, dernier contact 2026-08-22)

Vérifier après injection : la vue `À relancer` doit afficher **Bob (rouge, -5)** et **Alice (orange, 0)**.

### Étape 8 -- Créer le formulaire d'ajout

Dans la table `SC_Prospects` :

-> Clic sur `+ Create...` en bas à gauche de la liste des vues -> `Form`

-> Nommer : `Ajouter un prospect`

-> Champs visibles à cocher (dans l'ordre) : Prenom, Nom, Entreprise, Email, Telephone, Source, Notes

-> Champs cachés (auto-remplis à la création) : Statut (default Nouveau), Date entrée (default aujourd'hui), Dernier contact (default aujourd'hui)

-> Message post-soumission : `Prospect ajouté. Tu peux le suivre depuis la vue Tous les prospects.`

-> Cliquer `Share form` en haut à droite pour obtenir l'URL publique (à partager avec Eva PRO ou intégrer sur un site).

### Étape 9 -- Créer l'automatisation email hebdo

Dans la base, panneau gauche -> `Automations` -> `+ Create automation` :

-> Nom : `Email hebdo - Prospects à relancer`

-> Trigger : `At scheduled time` -> Weekly / Monday / 09:00 (Europe/Paris)

-> Étape 1 -- Add action : `Find records`

  - Table : `SC_Prospects`

  - View : `À relancer`

-> Étape 2 -- Add action : `Send email`

  - To : ton email (test) ou `owner@exemple.com`

  - Subject : `[CRM] Prospects à relancer cette semaine`

  - Body : voir SPECS.md pour le template Handlebars complet

-> `Test action` puis `Turn on automation`

### Étape 10 -- Prendre les captures d'écran

Sauvegarder dans `az-no-code/defis/crm-souverain/captures/` :

- `01_base_grille.png` -- Vue "Tous les prospects" avec les 4 exemples visibles

- `02_vue_a_relancer.png` -- Vue "À relancer" avec Alice + Bob colorés

- `03_calendrier.png` -- Vue calendrier avec relances positionnées

- `04_kanban.png` -- Vue kanban avec les 4 colonnes remplies

- `05_formulaire.png` -- Vue du formulaire d'ajout

- `06_automatisation.png` -- Config de l'automatisation email hebdo

## Voie B -- Création manuelle depuis SPECS.md

Si tu préfères sans API (pas de token à créer), suis directement `airtable/SPECS.md` section par section :

- Table SC_Prospects -> section "Table SC_Prospects" (13 champs à créer)

- Vues -> section "Vues" (4 vues)

- Formulaire -> section "Formulaire d'ajout"

- Automatisation -> section "Automatisation"

Puis exemples et captures = étapes 7 et 10 ci-dessus.

## Rappel important

Les 4 exemples sont **entièrement fictifs**. Ne pas mettre tes vraies infos ni celles d'Eva PRO ou d'un vrai prospect.

## Cas de blocage

Si un champ formule refuse d'être créé (erreur Airtable), vérifier que TOUS les champs référencés (Prenom, Nom, Statut, Dernier contact) existent déjà et sont écrits EXACTEMENT comme dans la formule (majuscules/accents).

Si l'API renvoie `INVALID_REQUEST_UNKNOWN`, vérifier que le token a bien le scope `schema.bases:write` et que la base a bien été ajoutée à ses accès.

Si la vue `À relancer` reste vide alors qu'il devrait y avoir Alice et Bob, vérifier :

- Que la date du jour est bien 2026-09-01 (ou postérieure)

- Que le champ `Prochaine relance` est bien calculé (voir formule étape 5)

- Que le filtre utilise bien `On or before today` (pas `is today`)

## Après livraison Eva PRO

Une fois les captures faites et livrées à Alegria, le repo garde la trace. Pour un vrai usage sur des vrais prospects, il faudra :

- Créer une nouvelle base ou dupliquer celle-ci

- Effacer les 4 exemples

- Ajouter les vraies infos David / prospects réels

C'est aussi le moment de tester la Voie C : bascule vers NocoDB self-host (voir `nocodb/SPECS.md`) ou vers le MVP code souverain Next.js (`az-code/defis/crm-souverain/README.md`).
