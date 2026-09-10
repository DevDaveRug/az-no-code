# RUNBOOK -- construction base Airtable Sales Closer Souverain

Version : 1.1.0
Date : 2026-09-10
Statut : Actif -- livrable défi Alegria Eva PRO 2026-09-08

> Guide copy-paste pour monter la base Airtable `Sales Closer Souverain` en 10-15 min, connectée au workflow n8n **SB_WF10 v1.1.0** (Niveau 2, accents FR + année 2026 validés end-to-end).
>
> **Architecture bases connectables (v1.1.0)** : une SEULE base par client regroupe toutes les briques de la boîte-à-outils souveraine (CR-RDV aujourd'hui, CRM demain, Facturation plus tard). Chaque brique = une table. Le champ `Prospect` linké permet de rattacher un CR à un prospect sans re-saisir. Détails architecture : §XI.
>
> Compagnon des specs complètes : [README.md](./README.md)
>
> Pré-requis : compte Airtable (plan Free suffit pour <25 CR/mois, sinon plan Team ~24€/mois).

---

## I- Ce que tu vas obtenir

Au bout de ce runbook :

-> Base Airtable `Sales Closer Souverain` avec 2 tables : `Prospects` (vide au démarrage, se remplit quand la brique CRM arrive) et `CRs de RDV` (le défi actuel, avec les 4 seeds)

-> 3 vues fonctionnelles sur `CRs de RDV` : "Nouveaux à formater", "Récents", "À envoyer au client"

-> 1 formulaire public de saisie des notes brutes (URL partageable)

-> 1 automation webhook qui appelle **SB_WF10** (le workflow n8n déjà en prod, celui que tu as validé avec Julie Marchand et Karim Benhaddad)

-> 4 seeds fictifs prêts à re-formatter en un clic pour tester le pipeline complet

-> 1 champ `Prospect` linké entre `CRs de RDV` et `Prospects` (optionnel, sert au rattachement futur sans re-saisie)

---

## II- Étape 1 -- Créer la base + import CSV (1 min)

1- Ouvre [airtable.com](https://airtable.com) -> **Create a base** -> **Start from scratch**

2- Nomme la base `Sales Closer Souverain` (une seule base pour toute la boîte-à-outils souveraine -- voir §XI)

3- Dans la table par défaut (`Table 1`), clique sur l'entête -> **Import data** -> **CSV file**

4- Upload le fichier `seed/crs-seed.csv` (dans ce dossier)

5- Airtable détecte les 6 colonnes (Notes brutes, Date RDV, Interlocuteur, Sujet, Statut, Modèle utilisé) -> **Import**

6- Renomme la table `Table 1` en `CRs de RDV` (clic droit sur l'onglet -> Rename)

À ce stade : la table `CRs de RDV` contient 4 lignes toutes en `Statut = À formater`, sans `CR formaté` ni `dateRdv`/`interlocuteur`/`sujet` parsés par le LLM (ces champs seront écrits par l'automation).

---

## III- Étape 2 -- Ajuster les types de champs + créer la table Prospects (4 min)

### 2.a -- Ajuster les types de `CRs de RDV`

Airtable détecte automatiquement mais 2 champs ont besoin d'être forcés :

-> **Statut** -> clic sur l'entête -> **Customize field type** -> **Single select** -> ajoute les 4 options : `À formater`, `Formaté`, `Envoyé au client`, `Erreur` (couleurs libres)

-> **Date RDV** -> clic sur l'entête -> **Customize field type** -> **Date** -> coche **Include a time field** -> **Save**

Ajoute maintenant les 2 champs manquants (pour recevoir le retour du webhook SB_WF10) :

-> Clic **+** en fin de tableau -> nomme `CR formaté` -> type **Long text** -> coche **Enable rich text formatting**

-> Clic **+** -> nomme `Créé le` -> type **Created time**

Optionnel (formule d'affichage) :

-> Clic **+** -> nomme `Nom` -> type **Formula** -> colle : `IF({Interlocuteur}, {Interlocuteur} & " -- " & DATETIME_FORMAT({Date RDV}, "DD/MM/YYYY"), "CR du " & DATETIME_FORMAT(CREATED_TIME(), "DD/MM/YYYY HH:mm"))` -> déplace ce champ en 1ère position (drag & drop de l'entête).

### 2.b -- Créer la table Prospects (vide au démarrage)

Cette table reste vide tant que le client n'a pas la brique CRM. Elle sert d'ancrage pour le rattachement futur (voir §XI).

1- En haut de la barre d'onglets tables (à côté de `CRs de RDV`), clique **+ Add or import** -> **Create empty table** -> nomme la table `Prospects`

2- Renomme le champ `Name` par défaut en `Nom complet` (double-clic sur l'entête)

3- Ajoute 3 champs simples (clic **+** en fin de tableau) :

-> `Entreprise` -> type **Single line text**

-> `Email` -> type **Email**

-> `Téléphone` -> type **Phone number**

Aucun seed à ajouter -- la table reste vide, c'est normal.

### 2.c -- Ajouter le champ Prospect linké dans `CRs de RDV`

Retour dans la table `CRs de RDV` :

1- Clic **+** en fin de tableau -> nomme le champ `Prospect`

2- Type -> **Link to another record** -> sélectionne la table `Prospects`

3- **Allow linking to multiple records** -> **décoché** (un CR concerne un seul prospect)

4- **Create**

Le champ apparaît vide sur les 4 seeds -- normal, c'est le mode standalone. Il pourra être rempli plus tard quand la brique CRM sera montée.

---

## IV- Étape 3 -- Créer les 3 vues (3 min)

Dans la barre latérale gauche (icône **Views**), clique sur **+ Create...** :

-> **Grid view** nommée `Nouveaux à formater` -> filtre `Statut is À formater`

-> **Grid view** nommée `Récents` -> tri `Créé le` décroissant -> **Sort with 1 field**

-> **Grid view** nommée `À envoyer au client` -> filtre `Statut is Formaté` -> group by `Interlocuteur`

---

## V- Étape 4 -- Monter l'automation webhook SB_WF10 (5 min)

1- Onglet **Automations** (en haut à droite) -> **Create automation** -> nomme `Formatage CR via SB_WF10`

2- **Trigger** -> **When record enters view** -> Table `CRs de RDV` -> View `Nouveaux à formater`

3- **Add action** -> **Send webhook request**

4- Configure l'action :

**Method** : `POST`

**URL** :

```
https://sb-n8n.coolify.salescloser.fr/webhook/cr-rdv-format
```

(vérifie l'URL exacte du webhook SB_WF10 dans ton n8n -> workflow "SB_WF10 CR-RDV formatage v1.1.0" -> node `Webhook` -> onglet **Production URL**)

**Headers** :

```
X-Webhook-Secret: <valeur du secret Bw "n8n Webhook Secret - CR-RDV">
Content-Type: application/json
```

**Body** (type `JSON`) :

```json
{
  "notesBrutes": "<clic 'Insert value' -> Trigger record -> Notes brutes>",
  "modeleLlm": "anthropic/claude-sonnet-5",
  "airtableRecordId": "<clic 'Insert value' -> Trigger record -> Airtable record ID>"
}
```

5- **Test action** -> l'automation envoie la requête au webhook, tu vois la réponse n8n en direct (attends 5-10 sec, tu dois recevoir un JSON avec `crFormate`, `dateRdv`, `interlocuteur`, `sujet`, `modeleLlm`).

6- **Add action** (après le webhook) -> **Update record** -> Table `CRs de RDV` -> Record `Trigger record` -> renseigne :

-> `CR formaté` = valeur `crFormate` du step précédent

-> `Date RDV` = valeur `dateRdv`

-> `Interlocuteur` = valeur `interlocuteur`

-> `Sujet` = valeur `sujet`

-> `Statut` = `Formaté` (valeur fixe)

-> `Modèle utilisé` = valeur `modeleLlm`

7- **Turn on** en haut de la page automation.

---

## VI- Étape 5 -- Créer le formulaire d'ajout (2 min)

1- Retourne dans la table `CRs de RDV` -> sidebar gauche -> **+ Create...** -> **Form**

2- Nomme le formulaire `Prendre un CR`

3- Décoche tous les champs sauf `Notes brutes` (les autres seront remplis par l'automation)

4- Dans les **Form settings** (roue crantée en bas) :

-> Titre : `Colle tes notes brutes de RDV`

-> Description : `Format libre. L'agent formate le CR en 5-10 sec.`

-> Bouton submit : `Formater le CR`

-> **After submit** : `Show a customizable message` -> `CR en cours de formatage. Rafraîchis la vue Récents dans 10 secondes.`

5- Ajoute une automation pour que le statut soit bien `À formater` dès la soumission :

-> Retour dans **Automations** -> **Create automation** -> nomme `Init statut sur nouveau CR`

-> Trigger : `When form is submitted` -> Form `Prendre un CR`

-> Action : `Update record` -> Table `CRs de RDV` -> Record `Trigger record` -> `Statut` = `À formater`

-> **Turn on**

6- Copie l'**Share form** URL (bouton en haut à droite du formulaire) -> c'est le lien que tu donneras au client.

---

## VII- Étape 6 -- Test end-to-end (2 min)

Test A -- via les seeds :

1- Ouvre la vue `Nouveaux à formater` -> tu vois les 4 lignes seed

2- Onglet **Automations** -> `Formatage CR via SB_WF10` -> **Run history** -> **Run for all matching records** (Airtable propose ça sur l'automation qui a un trigger "When record enters view" si des records matchent déjà)

3- Attends 30-60 sec (4 CR à générer) -> les 4 lignes passent en `Statut = Formaté` avec `CR formaté` rempli, accents FR complets, année 2026 correcte

Test B -- via le formulaire :

1- Ouvre le lien du formulaire dans un onglet incognito

2- Colle des notes brutes (n'importe lesquelles, ex : `rdv thomas milano 10 sept 11h refonte site 3k budget`) -> **Formater le CR**

3- Retour dans Airtable -> vue `Récents` -> la nouvelle ligne apparaît en `À formater` puis passe `Formaté` en 5-10 sec

---

## VIII- Ce que tu montres à Eva/Alegria

Une fois monté, tu as 3 démos possibles :

-> **Formulaire public** (lien Share Form) -> le client colle ses notes, le CR arrive en 10 sec -> aucun outil à apprendre

-> **Vue `Récents`** -> le client voit ses 20 derniers CR, tableau lisible, colonne `CR formaté` en markdown rendu

-> **Vue `À envoyer au client`** -> groupée par interlocuteur, prête à copier dans un mail

Le tout en no-code, tout dans Airtable, backend n8n mutualisé avec la version code souverain.

---

## IX- Limitations Niveau 2 (assumées)

-> **Dépendance au workflow n8n SB_WF10** : si sb-n8n.coolify.salescloser.fr tombe, les CR arrêtent de se formater (mais les CR déjà générés restent dans Airtable, aucune perte). Mitigation : fallback Niveau 1 (OpenAI natif Airtable) documenté dans le README pour les cas de secours.

-> **PDF** : Airtable ne génère pas de PDF natif propre. Contournement : impression navigateur depuis la vue détail, ou passer par Page Designer add-on (payant), ou basculer sur la version code souverain qui a un vrai rendu PDF.

-> **Quota automations** : 25 exécutions/mois plan Free, 25 000/mois plan Team. 5 CR/semaine = 20/mois -> plan Free suffit.

---

## X- Après le défi

Si le client veut aller plus loin (v0.2 du défi) :

-> Rattacher les CR à un prospect dans `crm-souverain` (Airtable ou version code) via champ `Prospect` linké -- déjà en place dès la v1.1.0 du RUNBOOK, il suffira de remplir la table `Prospects`

-> Ajouter un champ `Envoyé le` + une automation "envoyer le CR par mail à l'interlocuteur" (Gmail/Brevo)

-> Passer à la version code souverain : `az-code/defis/cr-rdv-souverain` (Next.js + Neon + Prisma + PDF via `@react-pdf/renderer`)

---

## XI- Architecture bases connectables (référence)

### Principe : isolation par défaut, rattachement optionnel via clé unique

Chaque brique de la boîte-à-outils souveraine (CR-RDV aujourd'hui, CRM demain, Facturation plus tard) se livre seule ET peut se connecter aux autres sans re-saisie. Le client démarre par la brique qu'il veut, ajoute les autres à son rythme, sans jamais dupliquer un prospect.

### Côté no-code Airtable

**Une SEULE base par client** : `Sales Closer Souverain`. Toutes les briques cohabitent en tables séparées :

| Table | Origine | État après ce runbook |
|---|---|---|
| `Prospects` | brique CRM (à venir) | vide (4 champs déclarés) |
| `CRs de RDV` | ce défi | 4 seeds + champ `Prospect` linké |
| `Entreprises` | crm-souverain v0.2 | non créée |
| `Interactions` | crm-souverain v0.2 | non créée |
| `Factures` | brique facturation (futur) | non créée |

**Pourquoi une seule base multi-tables et pas plusieurs bases** :

-> Airtable NE PERMET PAS de linker des enregistrements entre BASES différentes. Cross-base sync = plan Team payant (~24€/mois). Une seule base multi-tables = gratuit et relations natives.

-> Le client qui démarre par CR-RDV SEUL a une table `Prospects` VIDE : zéro coût, zéro contrainte, zéro doublon. Le champ `Prospect` dans `CRs de RDV` reste optionnel.

-> Quand la brique CRM arrive, il suffit de peupler `Prospects` -> les CRs existants peuvent être rattachés rétroactivement en 1 clic (dropdown searchable Airtable).

**Isolation logique par vues** : chaque brique a ses propres vues nommées de façon claire (`CR - Nouveaux à formater`, `CRM - Prospects à relancer`, etc.). Le client qui n'utilise qu'une brique voit ses vues, ignore les autres.

### Côté code souverain (Next.js + Neon)

**Une SEULE base Neon `sales-closer-souverain`** partagée entre tous les projets Next.js de la boîte-à-outils. `DATABASE_URL` identique sur tous les projets Vercel.

Isolation par **schémas PostgreSQL** :

-> Schéma `crm` -> tables `crm.prospects`, `crm.entreprises`, `crm.interactions` (owned par `az-code/defis/crm-souverain`)

-> Schéma `cr_rdv` -> table `cr_rdv.crs` avec FK optionnelle `prospect_id references crm.prospects(id) on delete set null` (owned par `az-code/defis/cr-rdv-souverain`)

-> Chaque `schema.prisma` déclare `previewFeatures = ["multiSchema"]` + `schemas = ["<son propre schéma>"]` -> les migrations Prisma des différentes briques ne se marchent pas dessus.

**Pourquoi ça marche** :

-> Une seule DB Neon = une seule facture (scale-to-zero commun), un seul backup, une seule branche Neon pour tester

-> Isolation logique via schémas = pas de collision, permissions différenciées possibles (rôle Neon par brique si besoin plus tard)

-> Le champ `prospectId` (déjà dans le schema Prisma cr-rdv-souverain v0.1) devient une vraie FK -- reste NULL si `crm-souverain` pas déployé, aucune erreur

-> Un seul `DATABASE_URL` à exporter en dev, à renseigner sur Vercel côté chaque projet

### Miroir no-code / code

| Niveau | No-code Airtable | Code souverain Neon |
|---|---|---|
| Conteneur | 1 base `Sales Closer Souverain` | 1 DB `sales-closer-souverain` |
| Séparation par brique | tables (`Prospects`, `CRs de RDV`, ...) | schémas Postgres (`crm`, `cr_rdv`, ...) |
| Lien inter-briques | `Link to another record` (Airtable natif) | FK PostgreSQL (Prisma multiSchema) |
| Coût si standalone | 0€ (table vide autorisée) | 0€ (schéma vide autorisé) |
| Migration standalone -> combo | remplir la table concernée | déployer la nouvelle brique + `prisma db push` |

### Ce que ça change pour le défi actuel

Le RUNBOOK v1.1.0 monte déjà l'infrastructure connectable (base `Sales Closer Souverain` + table `Prospects` vide + champ `Prospect` linké). Aucune migration ultérieure à prévoir : quand tu ajoutes la brique CRM (défi Alegria suivant probablement), tu peuples juste la table `Prospects` existante. Les CRs déjà générés se rattachent en 1 clic.

Côté code : le schema Prisma de `cr-rdv-souverain` v0.1 contient déjà `prospectId` en FK optionnelle. Rien à changer -- il attendra `crm-souverain` en `cr_rdv` schema séparé, DATABASE_URL partagée.

---

## Changelog

-> 1.1.0 -- 2026-09-10 (S133z-ccweb, Val David) : refonte "architecture bases connectables" (Val David) -- base renommée `Sales Closer Souverain` (une seule pour toute la boîte-à-outils), table `Prospects` créée dès l'Étape 2.b (4 champs, vide au démarrage), champ `Prospect` linké ajouté dans `CRs de RDV` (Étape 2.c), section XI "Architecture bases connectables" détaillant le miroir no-code Airtable / code Neon. Impact temps côté toi : +2-3 min à l'Étape 2 (table vide + champ linké), reste inchangé.

-> 1.0.0 -- 2026-09-10 (S133z-ccweb, Val David) : création. Runbook 10-15 min pour construction manuelle de la base Airtable connectée à SB_WF10 v1.1.0. Compagnon du CSV `seed/crs-seed.csv` (4 lignes fictives). Livrable défi Alegria Eva PRO 2026-09-08.
