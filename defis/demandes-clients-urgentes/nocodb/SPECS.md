# NocoDB -- Demandes clients urgentes (self-host)

Équivalent souverain d'Airtable. Sert :

- de démonstration `je peux te refaire ça chez toi sans dépendre d Airtable`

- de fallback si Airtable devient payant / coupé pour un client

- de brique réutilisable pour d'autres clients

## Cible d'hébergement

Instance : `sb-nocodb.coolify.salescloser.fr` (à provisionner selon `IDEE_infra_196` de `dr-context`, PR #532 mergée S137z-ccdd, bloquée par IDEE_infra_195 fix TLS Coolify -- livrée S137z-ccdd).

En attendant l'instance : la spec ci-dessous est autoportante et applicable à toute instance NocoDB (0.263.x+).

## Base

Nom : `Demandes clients urgentes`.

Backend : SQLite embarqué pour la démo. Migration Postgres Neon en v2 (`NC_DB=pg://<neondb_owner>@ep-...neon.tech:5432/neondb?schema=demandes`).

## Table `AZ_Clients`

| Field | Type NocoDB | Notes |
|---|---|---|
| Id | ID (auto) | primary key |
| Nom | SingleLineText | requis |
| Email | Email | requis, indexé |
| Telephone | PhoneNumber | optionnel |
| DateCreation | CreatedTime | auto |
| Demandes | Links -> AZ_Demandes | has many |
| NbDemandes | Rollup on Demandes | count field |

## Table `AZ_Demandes`

| Field | Type NocoDB | Notes |
|---|---|---|
| Id | ID (auto) | primary key |
| Ref | Formula | `CONCAT("DR#", {Id})` pour un affichage lisible |
| Client | Links -> AZ_Clients | belongs to |
| DateDemande | CreatedTime | auto |
| Description | LongText | requis |
| UrgenceClient | SingleSelect | options `Basse` (défaut), `Moyenne`, `Haute`, `Critique` |
| UrgenceReelle | SingleSelect | même options, défaut = valeur de UrgenceClient (rempli par workflow post-create) |
| Statut | SingleSelect | options `Nouveau` (défaut), `En cours`, `Bloque`, `Fait` |
| Commentaires | LongText | interne |
| DateTraitement | Date | manuel ou workflow |
| NomClientTemp | SingleLineText | rempli par form public |
| EmailClientTemp | Email | idem, sert au matching workflow |
| TelephoneClientTemp | PhoneNumber | idem |

## Vues `AZ_Demandes`

### Grid `Urgentes en premier` (défaut Dashboard)

Sort : UrgenceReelle desc, DateDemande desc. Filter : Statut != `Fait`.

### Kanban `Par statut`

Group by : Statut.

### Grid `Nouvelles à traiter`

Filter : Statut = `Nouveau`. Sort UrgenceReelle desc.

### Grid `Toutes` (audit)

Aucun filter, tri DateDemande desc.

## Vues `AZ_Clients`

### Grid `Tous les clients`

Tri NbDemandes desc.

### Grid `Top demandeurs`

Filter : NbDemandes >= 3. Tri NbDemandes desc.

## Formulaire partageable

NocoDB permet le partage public de vues Form. Config identique à Airtable :

- Champs : NomClientTemp, EmailClientTemp, TelephoneClientTemp (optionnel), Description, UrgenceClient

- Message post-submit : `Merci, ta demande est bien reçue.`

- Partage : NocoDB `Share View` -> Public -> Read-only.

L'URL générée : `https://sb-nocodb.coolify.salescloser.fr/#/nc/form/<uuid>`. Cette URL est celle à donner au client.

## Workflow post-form (n8n)

NocoDB émet un webhook `After Insert` sur `AZ_Demandes`. Cible : n8n `sb-n8n.coolify.salescloser.fr`.

Workflow n8n `SB_WF_demandes_client_match` (à créer) :

1. Webhook trigger : reçoit le record `AZ_Demandes` créé.

2. HTTP GET NocoDB API : `GET /api/v2/tables/{AZ_Clients_id}/records?where=(Email,eq,{EmailClientTemp})`.

3. IF : records.list.length > 0 ?

   - True : HTTP PATCH NocoDB API sur le record AZ_Demandes, `Client = records.list[0].Id`.

   - False : HTTP POST NocoDB API `/api/v2/tables/{AZ_Clients_id}/records` avec Nom, Email, Telephone. Puis PATCH `AZ_Demandes.Client = new_client.Id`.

4. HTTP PATCH NocoDB API : `AZ_Demandes.UrgenceReelle = UrgenceClient` (copie initiale).

5. HTTP POST Resend ou SMTP IONOS pour envoyer l'email de confirmation au client.

6. IF UrgenceReelle in Haute, Critique : Telegram Bot `sendMessage` vers ton chat ID.

## Interface Dashboard NocoDB natif

Voir `INTERFACE.md` pour la spec Dashboard partageable (Widgets Number + Kanban + Table).

## Alternative sans n8n

Si tu ne veux pas monter un workflow n8n, remplacer les webhooks par des Cron jobs NocoDB (`Scheduled Jobs`) qui polling chaque minute la table `AZ_Demandes` où `Client IS NULL` et applique la même logique en script custom (NocoDB Scripts extension). Plus lourd à maintenir, mais autonome.
