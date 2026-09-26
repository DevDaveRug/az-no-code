# NocoDB - CR de RDV formatés (self-host)

Équivalent souverain d'Airtable pour le défi 2. Sert :

- de démonstration "je peux formater automatiquement mes CR de RDV chez moi sans dépendre d'Airtable / OpenAI direct"

- de fallback si l'Airtable Interface unifiée devient payant pour un client

- de sous-brique du portfolio "boîte à outils souveraine" (3 chemins d'usage documentés dans le RUNBOOK.md du défi 2)

Livrable central du défi 2 reste `PROMPT_LLM.md` (voir `az-code/defis/cr-rdv-souverain/PROMPT_LLM.md`) : la version NocoDB permet de le stocker + versionner + l'exécuter via automation, sans dépendre d'un compte Airtable Team.

## Cible d'hébergement

Instance à provisionner sur la même infrastructure Coolify que défi 1 (mutualisée) :

- URL : `https://sb-nocodb.coolify.salescloser.fr/` (livrée S153c-ccdd, IDEE_infra_196)

- Auth : compte admin déjà créé par David en S153c

- Base par défaut : SQLite embarqué (v1), migration Postgres Neon schema-isolé prévue v2

## Base

Nom : `CR de RDV formatés` (ou `Sales Closer Souverain - CRs` si mutualisation avec défi 1)

## Table SC_CRs_de_RDV

Vocabulaire NocoDB : "table" = "table", "champ" = "field", "vue" = "view", "formulaire" = "form", "webhook" = "webhook", automatisation planifiée = n8n externe (NocoDB Community sans cron interne).

| Field | Type NocoDB | Notes |
|---|---|---|
| Id | ID (auto) | primary key |
| DateRDV | Date | date du RDV, required |
| Client | SingleLineText | nom du contact (peut être Link Record vers SC_Prospects si base mutualisée) |
| ContexteRDV | SingleSelect | `Découverte`, `Négociation`, `Suivi`, `Closing`, `Support` |
| NotesBrutes | LongText | notes tapées à la volée pendant / après le RDV, formatage libre |
| CRFormate | LongText | version formatée en 2 colonnes (Points client / Actions à faire), remplie par webhook n8n LLM |
| Statut | SingleSelect | `À formater`, `Formaté`, `Validé`, `Envoyé au client` |
| DateFormatage | DateTime | timestamp du formatage, rempli par webhook n8n |
| Cout_LLM | Decimal | coût en euros du call LLM (~0,001-0,005€ selon modèle), rempli par n8n |
| Notes | LongText | annotations internes David |

## Vues

### View 1 - Grid "À formater"

- filter : `Statut = À formater`

- tri : `DateRDV desc`

- champs affichés : `DateRDV`, `Client`, `ContexteRDV`, `NotesBrutes`

### View 2 - Grid "Historique récent"

- tri : `DateRDV desc`

- champs affichés : `DateRDV`, `Client`, `ContexteRDV`, `Statut`, `CRFormate`

- limit visuel : 30 derniers

### View 3 - Kanban "Par statut"

- group by : `Statut`

- colonnes : À formater, Formaté, Validé, Envoyé au client

## Formulaire public (read-write)

Form "Nouveau CR de RDV" partagé en public, permet au client (ou à David) de saisir un CR brut sans se connecter à NocoDB :

- champs exposés : `DateRDV`, `Client`, `ContexteRDV`, `NotesBrutes`

- champ pré-rempli : `Statut = À formater`

- webhook n8n "After Insert" -> déclenche SB_WF10 (LLM formatage + Update CRFormate + Update Statut = Formaté)

URL publique : à générer en session (`Share form -> Enable public sharing`).

## Automation (webhook n8n -> LLM)

Le workflow SB_WF10 v1.1.0 existant (livré défi 2 v0.6, référencé RUNBOOK.md §Niveau 2) est déjà compatible NocoDB :

- Trigger : Webhook NocoDB "After Insert on SC_CRs_de_RDV"

- Étape 1 : appel OpenRouter (modèle défaut `anthropic/claude-3.5-sonnet`, fallback `mistralai/mistral-large`)

- Étape 2 : prompt = contenu de `PROMPT_LLM.md` + `NotesBrutes` en input

- Étape 3 : PATCH NocoDB `/api/v2/tables/<tableId>/records/{Id}` avec `CRFormate` + `Statut = Formaté` + `DateFormatage` + `Cout_LLM`

Ce workflow est déjà en prod côté ce-n8n (Sales Closer) et peut être dupliqué / adapté pour lire depuis NocoDB au lieu d'Airtable, en changeant seulement le trigger + les credentials d'écriture.

## Exemples de démarrage (5 records)

Voir `exemples.md` -- 5 CR bruts anonymisés couvrant les 5 contextes.

## Notes migration Airtable -> NocoDB

Le champ Link Record vers SC_Prospects (livré côté Airtable dans le RUNBOOK.md du défi 2) est **omis** en v1 NocoDB (base standalone, pas de mutualisation avec défi 1 pour ce démo). En v2 (Postgres Neon multi-schema), on peut rétablir le Link Record vers SC_Prospects de la même façon qu'Airtable.

## Statut

Déployé en S138z-ccdd (rattrapage rétroactif IDEE_infra_196 défis 1+2+3, 2026-09-26).

- Base ID NocoDB : `pezcail7wrs5cuq`
- Table SC_CRs_de_RDV ID : `mua8v3enwrm37dg`
- 5 records d'exemples déjà insérés via API bulk
- URL publique base (read-only) : `https://sb-nocodb.coolify.salescloser.fr/#/base/79d50c05-8361-4e52-b8a4-1763feaffeb7`
- URL publique formulaire read-write : à créer via UI (Add form view -> Enable public share) puis remplacer ici
- Statut AZ_Portfolio : `Livré` (Lien_NocoDB_Demo patché via API)
