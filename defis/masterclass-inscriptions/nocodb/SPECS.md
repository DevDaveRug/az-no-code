# NocoDB - Masterclass Inscriptions (self-host)

Équivalent souverain d'Airtable. Sert :

-> de démonstration "je peux le refaire chez toi sans dépendre d'Airtable"

-> de fallback si Airtable devient payant/coupé pour un client

-> de brique réutilisable pour d'autres clients Alegria

## Cible d'hébergement

Instance à provisionner (si pas déjà fait sur `sb-nocodb.coolify.salescloser.fr`) :

1. Coolify -> New Resource -> One click service -> NocoDB

2. Env (option souverain complet) : `NC_DB=pg://<neon-url>` pour brancher Neon Postgres, sinon SQLite embarqué (suffit pour la démo)

3. DNS : CNAME `sb-nocodb -> coolify.salescloser.fr`

4. Attendre certificat TLS (~2 min)

5. Créer compte admin au premier accès

## Base

Nom : `Masterclass Inscriptions`

## Table AZ_Inscrits

Champs (ASCII pour éviter les surprises côté formules/exports) :

| Field | Type NocoDB | Notes |
|---|---|---|
| Id | ID (auto) | primary key |
| Prenom | SingleLineText | required |
| Email | Email | required |
| DateMasterclass | Date | required, format ISO |
| StatutEmail | SingleSelect | options : `En attente` (default), `Envoyé`, `Erreur` |
| DateInscription | CreatedTime | auto |
| Notes | LongText | |

## Vues

### View 1 - Grid "Tous les inscrits"

Type : Grid

Sort : `DateInscription` desc

### View 2 - Grid "Par masterclass"

Type : Grid

Group by : `DateMasterclass` asc

Sort interne : `Prenom` asc

### View 3 - Grid "Erreurs d'envoi"

Type : Grid

Filter : `StatutEmail` = `Erreur`

## Formulaire d'ajout

Type : Form view

Champs visibles : `Prenom`, `Email`, `DateMasterclass`

Champs cachés (default) : `StatutEmail` = `En attente`

URL publique : générée par NocoDB, à partager (LinkedIn, site).

## Automatisation (la partie qui change vs Airtable)

NocoDB Community n'a pas d'automation native "Send email on record created". Deux options :

### Option 1 - Webhook natif NocoDB -> n8n (recommandée)

Sur la table `AZ_Inscrits` :

-> onglet `Webhooks` -> `Create new webhook`

-> Nom : `inscrit-cree`

-> Event : `After Insert`

-> Method : POST

-> URL : `https://sb-n8n.coolify.salescloser.fr/webhook/masterclass-inscrit-cree`

-> Body : `{{ record }}` (payload JSON par défaut avec tous les champs de la ligne)

-> Headers : `X-NocoDB-Secret: <secret partagé>` pour valider côté n8n

Puis workflow n8n (à créer sur `sb-n8n.coolify.salescloser.fr`) :

1. **Webhook** node : reçoit le POST, valide le header `X-NocoDB-Secret`

2. **Send Email** node (SMTP IONOS ou Resend) :

   -> to : `{{$json.Email}}`

   -> subject : `Ta place pour la masterclass du {{$json.DateMasterclass}} est confirmée`

   -> body : (identique au wording Airtable, cf `airtable/SPECS.md`)

3. **NocoDB** node (update record) :

   -> table : `AZ_Inscrits`

   -> record id : `{{$json.Id}}`

   -> field : `StatutEmail` = `Envoyé` (ou `Erreur` si le step 2 a échoué -- gérer via IF node)

Export du workflow attendu : `azien-workflows/masterclass-inscrit-cree.json` (à créer par David ou lors d'un prochain skill n8n).

### Option 2 - Cron n8n (fallback si le webhook NocoDB coince)

Workflow n8n déclenché chaque minute :

-> `NocoDB list` : filter `StatutEmail = En attente`

-> pour chaque ligne, envoyer l'email + updater `StatutEmail`

Moins réactif (délai jusqu'à 1 min) mais robuste si les webhooks NocoDB ne sont pas fiables.

## Exemples de données

Voir `exemples.md` -- identiques à `airtable/exemples.md` (5 lignes dont David).

## Captures attendues (dans `captures/nocodb_*.png`)

- `nocodb_01_base.png` -- table peuplée

- `nocodb_02_par_masterclass.png` -- vue groupée

- `nocodb_03_form.png` -- formulaire d'ajout

- `nocodb_04_webhook_config.png` -- webhook NocoDB configuré sur `After Insert`

- `nocodb_05_n8n_workflow.png` -- workflow n8n complet (Webhook -> Send Email -> Update)

- `nocodb_06_email_recu.png` -- capture de l'email de confirmation reçu

## Angle "souverain" à raconter à Eva

Airtable : rapide, bien pour démarrer, mais tu dépends d'un compte, d'un quota d'automations (100/mois en gratuit), et d'un vendor US. Si Airtable augmente ses prix ou coupe une fonctionnalité, tu bouges rien.

NocoDB + n8n sur Coolify : même produit fonctionnel, chez toi, sans quota, sans facturation par utilisateur, tu peux tordre le workflow n8n autant que tu veux (ajouter un SMS Twilio, une notif Telegram, un log dans Google Sheets, une entrée dans Notion...). C'est ce qu'on livre à un vrai client qui veut son système à lui.
