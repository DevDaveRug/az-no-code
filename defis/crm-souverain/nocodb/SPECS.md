# NocoDB - CRM Souverain (self-host)

Équivalent souverain d'Airtable, à créer sur infra Coolify. Sert :

- de démonstration "je peux le refaire chez moi sans dépendre d'Airtable"

- de fallback si Airtable devient payant/coupé pour un client

## Cible d'hébergement

Instance à provisionner :

- URL cible : `sb-nocodb.coolify.salescloser.fr` (à ajouter DNS + Coolify service)

- Auth : login/password créé au premier accès

- Base par défaut : Postgres embarquée dans le service NocoDB (option "connect data source" pour lier une base Neon en prod)

Étape David (30 min) :

1. Coolify -> New Resource -> One click service -> NocoDB

2. Set env : `NC_DB=pg://...` si on veut du Postgres Neon, sinon laisser SQLite embarqué

3. DNS : ajouter CNAME `sb-nocodb -> coolify.salescloser.fr`

4. Attendre certificat TLS (~2 min)

5. Ouvrir l'URL, créer le compte admin

## Base

Nom : `CRM Souverain`

## Table SC_Prospects

Vocabulaire NocoDB : "table" = "table", "champ" = "field", "vue" = "view", "formulaire" = "form", "automatisation" = "webhook + custom" (NocoDB Community n'a pas d'automatisation planifiée native, cf note ci-dessous).

| Field | Type NocoDB | Notes |
|---|---|---|
| Id | ID (auto) | primary key |
| Prenom | SingleLineText | required |
| Nom | SingleLineText | required |
| Entreprise | SingleLineText | |
| Email | Email | required |
| Telephone | PhoneNumber | |
| Statut | SingleSelect | options : `Nouveau`, `En cours`, `Gagné`, `Perdu`, default = `Nouveau` |
| Source | SingleSelect | `LinkedIn`, `Recommandation`, `Site`, `Autre` |
| DateEntree | Date | default = `now()` |
| DernierContact | Date | |
| ProchaineRelance | Formula | `IF(OR({Statut}='Nouveau',{Statut}='En cours'), DATEADD({DernierContact}, 7, 'day'), '')` |
| JoursRestants | Formula | `DATEDIFF({ProchaineRelance}, NOW(), 'day')` |
| Notes | LongText | |
| MontantPotentiel | Decimal | 2 décimales |

NocoDB tolère les accents dans les noms de champs mais on garde ASCII pour éviter les surprises côté formules et exports.

## Vues

### View 1 - Grid "Tous les prospects"

Type : Grid

Sort : `DateEntree` desc

### View 2 - Grid "À relancer"

Type : Grid

Filter :

- (`Statut` = `Nouveau` OR `Statut` = `En cours`)

- AND `ProchaineRelance` <= `today`

Sort : `ProchaineRelance` asc

### View 3 - Calendar "Relances"

Type : Calendar

Date field : `ProchaineRelance`

### View 4 - Kanban "Pipeline"

Type : Kanban

Stack by : `Statut`

## Formulaire d'ajout

Type : Form view

Champs visibles : Prenom, Nom, Entreprise, Email, Telephone, Source, Notes

Champs cachés (default) : Statut = Nouveau, DateEntree = today, DernierContact = today

URL publique : générée par NocoDB, à partager avec un prospect

## Automatisation - option 1 : webhook natif NocoDB

Webhook déclenché sur `after insert` de `SC_Prospects` : POST vers un endpoint qui gère l'envoi mail.

Insuffisant pour un envoi HEBDOMADAIRE d'un récap. NocoDB Community n'a pas de scheduler natif.

## Automatisation - option 2 : n8n branché sur NocoDB (recommandé)

Créer un workflow n8n sur `sb-n8n.coolify.salescloser.fr` :

- Trigger : Cron chaque lundi 09:00 Europe/Paris

- Node NocoDB (get records) : filter `Statut IN (Nouveau, En cours) AND ProchaineRelance <= today`

- Node Send Email (SMTP IONOS ou Resend) : template avec la liste des prospects

- Ne pas envoyer si liste vide

Import n8n export : voir `azien-workflows/relance-hebdo.json` (à créer par David ou lors d'un prochain skill n8n).

## Captures attendues (dans `captures/nocodb_*.png`)

- `nocodb_01_base.png` - table peuplée

- `nocodb_02_a_relancer.png` - vue "À relancer" avec les prospects en retard

- `nocodb_03_calendrier.png` - vue calendrier

- `nocodb_04_kanban.png` - vue kanban

- `nocodb_05_form.png` - formulaire d'ajout

- `nocodb_06_n8n_workflow.png` - workflow n8n de relance hebdo
