# Airtable -- Demandes clients urgentes

Base cible : `Sales Closer Souverain` (`appTqLo3JDg7d1fak`) ou base dédiée si tu préfères isoler. Les 2 tables ci-dessous se créent à la main dans l'UI (2 min) ou via script API Metadata (voir `schema.json`).

## Table `AZ_Clients`

| Field | Type | Notes |
|---|---|---|
| Nom | Single line text | primary, requis |
| Email | Email | requis, considéré comme clé métier (matching automatique) |
| Telephone | Phone number | optionnel |
| DateCreation | Created time | auto |
| Demandes | Link record -> AZ_Demandes | multiple, symétrie automatique |
| NbDemandes | Count -> Demandes | calc (facultatif, joli pour la vue Top demandeurs) |

## Table `AZ_Demandes`

| Field | Type | Notes |
|---|---|---|
| Ref | Autonumber | primary, format `#{NUMBER}` en Airtable natif suffit |
| Client | Link record -> AZ_Clients | 1 seul, requis (lien complété par automatisation post-form) |
| DateDemande | Created time | auto |
| Description | Long text | requis, enable rich text non nécessaire |
| UrgenceClient | Single select | options : `Basse`, `Moyenne` (défaut), `Haute`, `Critique`. Déclaré par le client, non modifié après soumission. |
| UrgenceReelle | Single select | mêmes options, défaut = valeur de UrgenceClient à la création. Éditable par le propriétaire (formule initiale au Copy via automatisation). |
| Statut | Single select | options : `Nouveau` (défaut), `En cours`, `Bloque`, `Fait` |
| Commentaires | Long text | interne propriétaire, jamais montré au client |
| DateTraitement | Date | rempli à la main quand le statut passe à `Fait` |
| NomClientTemp | Single line text | rempli par le formulaire, sert au matching automation |
| EmailClientTemp | Email | idem |
| TelephoneClientTemp | Phone number | idem |

Note : les 3 champs `*Temp` sont peuplés par le formulaire public (l'utilisateur ne peut pas créer/choisir un `Link record` dans un formulaire Airtable). L'automatisation post-création (voir plus bas) les utilise pour matcher un `AZ_Clients` existant par email ou créer un nouveau `AZ_Clients`, puis remplir le field `Client` (Link record). Les 3 champs peuvent être masqués dans les vues opérationnelles après remplissage.

## Vues `AZ_Demandes`

### Grid `Urgentes en premier` (vue par défaut à ouvrir en dashboard)

- Sort : UrgenceReelle desc (Critique en haut), puis DateDemande desc

- Filter : Statut != `Fait`

- Fields affichés : Ref, Client, Description, UrgenceReelle, DateDemande, Statut

- Group : par UrgenceReelle (facultatif, très lisible)

### Kanban `Par statut`

- Group by : Statut

- Fields sur carte : Ref, Client, Description (tronqué), UrgenceReelle (avec couleur), DateDemande

### Grid `Nouvelles à traiter`

- Filter : Statut = `Nouveau`

- Sort : UrgenceReelle desc, DateDemande desc

- Fields : idem Grid Urgentes

### Grid `Toutes` (audit)

- Aucun filter, tri par DateDemande desc

## Vues `AZ_Clients`

### Grid `Tous les clients`

- Tri par NbDemandes desc puis DateCreation desc

### Grid `Top demandeurs`

- Filter : NbDemandes >= 3

- Tri NbDemandes desc

## Formulaire partageable

Créer un Airtable Form sur la table `AZ_Demandes`.

Champs à inclure dans le form (dans cet ordre) :

- NomClientTemp (label affiché : `Ton nom`), requis

- EmailClientTemp (label : `Ton email`), requis

- TelephoneClientTemp (label : `Ton téléphone (optionnel)`), optionnel

- Description (label : `Ta demande`), requis

- UrgenceClient (label : `À quel point c'est urgent selon toi ?`), requis, aide : `Basse = pas pressé, Moyenne = cette semaine, Haute = 24-48h, Critique = maintenant`

Champs à masquer : Client (auto), Statut (auto), UrgenceReelle (auto), Commentaires (interne), DateTraitement, Ref (auto), toutes les colonnes CreatedTime.

Message après soumission : `Merci, ta demande est bien reçue. On te recontacte dès que possible.`

Redirect : URL de ton choix (optionnel).

Partager l'URL publique du form : bouton `Share form` en haut de la config.

## Automatisation 1 -- Lier ou créer `AZ_Clients` post-form

Trigger : `When a record is created` sur `AZ_Demandes`.

Action 1 : `Find records` dans `AZ_Clients` avec condition `Email = {EmailClientTemp du record déclencheur}`.

Action 2 : Branche conditionnelle sur `Length of records found`.

- Si `Length > 0` : `Update record` sur le record déclencheur (`AZ_Demandes`), field `Client` = premier record trouvé.

- Si `Length = 0` : `Create record` dans `AZ_Clients` avec Nom = NomClientTemp, Email = EmailClientTemp, Telephone = TelephoneClientTemp. Puis `Update record` sur le déclencheur, field `Client` = record créé.

Action 3 (les 2 branches) : `Update record` sur le déclencheur, field `UrgenceReelle` = valeur de `UrgenceClient` (copie initiale, propriétaire ajustera à la main si besoin).

## Automatisation 2 -- Email de confirmation au client

Trigger : `When a record is created` sur `AZ_Demandes` (peut être fusionné avec Automatisation 1 en une chaîne d'actions, au choix).

Action : `Send email`.

- To : `{EmailClientTemp}`

- Subject : `Ta demande #{Ref} est bien reçue`

- Body :

```
Bonjour {NomClientTemp},

Ta demande a bien été reçue. Voici le récapitulatif :

Description : {Description}
Niveau d'urgence : {UrgenceClient}

On te recontacte dès que possible.

Merci pour ta confiance.
```

## Automatisation 3 -- Notif Telegram sur urgence Haute ou Critique

Trigger : `When a record matches conditions`. Table `AZ_Demandes`, condition `UrgenceReelle` est `Haute` OU `Critique`.

Action : `Run script` (Airtable Script Extension) OU `Send Slack message` OU webhook n8n vers Telegram, selon ton stack. Si tu utilises Telegram Bot personnel :

```javascript
// Script Airtable Send Telegram
const record = input.config().record;
const token = 'TELEGRAM_BOT_TOKEN_ICI';
const chatId = 'TON_TG_ID';
const text = `Demande urgente #${record.Ref} (${record.UrgenceReelle}) de ${record.NomClientTemp} :\n${record.Description}`;
await fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
  method: 'POST',
  headers: {'Content-Type': 'application/json'},
  body: JSON.stringify({chat_id: chatId, text})
});
```

Note : le token TG et ton chatId sont à stocker en dur dans le script pour la démo Alegria (pas de secret manager Airtable natif). En prod, préférer un webhook n8n qui garde le token côté serveur.

## Interface Airtable native

Voir `INTERFACE.md` pour la spec de la page Dashboard partageable (Layout `Dashboard` + éléments `Number`, `Grid`, `Timeline`).

## Recréation via API Metadata (script automatisé)

Voir `schema.json` pour le payload Metadata API. Réutilise le pattern `az-code/scripts/airtable-migrate-portfolio.mjs` (idempotent, non destructif) en adaptant les constantes `FIELDS_CLIENTS` et `FIELDS_DEMANDES`.
