# RUNBOOK -- construction base Airtable CR-RDV Formaté

Version : 1.0.0
Date : 2026-09-10
Statut : Actif -- livrable défi Alegria Eva PRO 2026-09-08

> Guide copy-paste pour monter la base Airtable `CR-RDV Formaté` en 10-15 min, connectée au workflow n8n **SB_WF10 v1.1.0** (Niveau 2, accents FR + année 2026 validés end-to-end).
>
> Compagnon des specs complètes : [README.md](./README.md)
>
> Pré-requis : compte Airtable (plan Free suffit pour <25 CR/mois, sinon plan Team ~24€/mois).

---

## I- Ce que tu vas obtenir

Au bout de ce runbook :

-> Base Airtable `CR-RDV Formaté` avec 1 table `CRs` (colonnes détectées auto à l'import CSV)

-> 3 vues fonctionnelles : "Nouveaux à formater", "Récents", "À envoyer au client"

-> 1 formulaire public de saisie des notes brutes (URL partageable)

-> 1 automation webhook qui appelle **SB_WF10** (le workflow n8n déjà en prod, celui que tu as validé avec Julie Marchand et Karim Benhaddad)

-> 4 seeds fictifs prêts à re-formatter en un clic pour tester le pipeline complet

---

## II- Étape 1 -- Import CSV (30 secondes)

1- Ouvre [airtable.com](https://airtable.com) -> **Create a base** -> **Start from scratch**

2- Nomme la base `CR-RDV Formaté`

3- Dans la table par défaut (`Table 1`), clique sur l'entête -> **Import data** -> **CSV file**

4- Upload le fichier `seed/crs-seed.csv` (dans ce dossier)

5- Airtable détecte les 6 colonnes (Notes brutes, Date RDV, Interlocuteur, Sujet, Statut, Modèle utilisé) -> **Import**

6- Renomme la table `Table 1` en `CRs` (clic droit sur l'onglet -> Rename)

À ce stade : 4 lignes visibles, toutes en `Statut = À formater`, sans `CR formaté` ni `dateRdv`/`interlocuteur`/`sujet` parsés par le LLM (ces champs seront écrits par l'automation).

---

## III- Étape 2 -- Ajuster les types de champs (2 min)

Airtable détecte automatiquement mais 2 champs ont besoin d'être forcés :

-> **Statut** -> clic sur l'entête -> **Customize field type** -> **Single select** -> ajoute les 4 options : `À formater`, `Formaté`, `Envoyé au client`, `Erreur` (couleurs libres)

-> **Date RDV** -> clic sur l'entête -> **Customize field type** -> **Date** -> coche **Include a time field** -> **Save**

Ajoute maintenant les 2 champs manquants (pour recevoir le retour du webhook SB_WF10) :

-> Clic **+** en fin de tableau -> nomme `CR formaté` -> type **Long text** -> coche **Enable rich text formatting**

-> Clic **+** -> nomme `Créé le` -> type **Created time**

Optionnel (formule d'affichage) :

-> Clic **+** -> nomme `Nom` -> type **Formula** -> colle : `IF({Interlocuteur}, {Interlocuteur} & " -- " & DATETIME_FORMAT({Date RDV}, "DD/MM/YYYY"), "CR du " & DATETIME_FORMAT(CREATED_TIME(), "DD/MM/YYYY HH:mm"))` -> déplace ce champ en 1ère position (drag & drop de l'entête).

---

## IV- Étape 3 -- Créer les 3 vues (3 min)

Dans la barre latérale gauche (icône **Views**), clique sur **+ Create...** :

-> **Grid view** nommée `Nouveaux à formater` -> filtre `Statut is À formater`

-> **Grid view** nommée `Récents` -> tri `Créé le` décroissant -> **Sort with 1 field**

-> **Grid view** nommée `À envoyer au client` -> filtre `Statut is Formaté` -> group by `Interlocuteur`

---

## V- Étape 4 -- Monter l'automation webhook SB_WF10 (5 min)

1- Onglet **Automations** (en haut à droite) -> **Create automation** -> nomme `Formatage CR via SB_WF10`

2- **Trigger** -> **When record enters view** -> Table `CRs` -> View `Nouveaux à formater`

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

6- **Add action** (après le webhook) -> **Update record** -> Table `CRs` -> Record `Trigger record` -> renseigne :

-> `CR formaté` = valeur `crFormate` du step précédent

-> `Date RDV` = valeur `dateRdv`

-> `Interlocuteur` = valeur `interlocuteur`

-> `Sujet` = valeur `sujet`

-> `Statut` = `Formaté` (valeur fixe)

-> `Modèle utilisé` = valeur `modeleLlm`

7- **Turn on** en haut de la page automation.

---

## VI- Étape 5 -- Créer le formulaire d'ajout (2 min)

1- Retourne dans la table `CRs` -> sidebar gauche -> **+ Create...** -> **Form**

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

-> Action : `Update record` -> Table `CRs` -> Record `Trigger record` -> `Statut` = `À formater`

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

-> Rattacher les CR à un prospect dans `crm-souverain` (Airtable ou version code) via champ `Prospect` linké

-> Ajouter un champ `Envoyé le` + une automation "envoyer le CR par mail à l'interlocuteur" (Gmail/Brevo)

-> Passer à la version code souverain : `az-code/defis/cr-rdv-souverain` (Next.js + Neon + Prisma + PDF via `@react-pdf/renderer`)

---

## Changelog

-> 1.0.0 -- 2026-09-10 (S133z-ccweb, Val David) : création. Runbook 10-15 min pour construction manuelle de la base Airtable connectée à SB_WF10 v1.1.0. Compagnon du CSV `seed/crs-seed.csv` (4 lignes fictives). Livrable défi Alegria Eva PRO 2026-09-08.
