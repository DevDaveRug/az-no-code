# RUNBOOK -- extension base Airtable Sales Closer Souverain (défi 2 CR-RDV)

Version : 1.2.0
Date : 2026-09-10
Statut : Actif -- livrable défi Alegria Eva PRO 2026-09-08

> Guide copy-paste pour étendre la base Airtable existante **`CRM Souverain`** (défi 1 crm-souverain livré 2026-09-05, avec correction Eva) en y ajoutant la table CR-RDV connectée au workflow n8n **SB_WF10 v1.1.0** (Niveau 2, accents FR + année 2026 validés end-to-end sur Julie Marchand + Karim Benhaddad).
>
> **Architecture bases connectables (v1.1.0+)** : une SEULE base par client regroupe toutes les briques de la boîte-à-outils souveraine. Le défi 1 a livré `SC_Prospects` (14 champs, 4 vues, formulaire, automation email récap lundi 9h, 4 seeds réels Emma Petit / Alice Martin / Bob Durand / Chloé Dubois, Interface Airtable `CRM Souverain` avec 4 pages). Le défi 2 ajoute la table `SC_CRs_de_RDV` + rebranding base en `Sales Closer Souverain` + Interface unifiée. Détails §XII.
>
> Compagnon des specs complètes : [README.md](./README.md)
>
> Pré-requis : ta base Airtable `CRM Souverain` existante (défi 1) doit être fonctionnelle. Airtable en français (labels UI : Données / Automatisations / Interfaces / Formulaires).

---

## I- Ce que tu vas obtenir

Au bout de ce runbook, ta base sera étendue avec :

-> Base renommée `CRM Souverain` → **`Sales Closer Souverain`** (garde tout l'existant du défi 1)

-> Nouvelle table `SC_CRs_de_RDV` (CSV auto-détection colonnes + 4 seeds fictifs à re-formatter)

-> Champ `Prospect` linké dans `SC_CRs_de_RDV` -> `SC_Prospects` existante (rattachement optionnel des CRs aux prospects du CRM en 1 clic)

-> 3 vues sur `SC_CRs_de_RDV` : `Nouveaux à formater`, `Récents`, `À envoyer au client`

-> 1 automation Airtable qui appelle **SB_WF10** (webhook n8n en prod) pour formater automatiquement chaque CR

-> 1 formulaire public de saisie des notes brutes (URL partageable)

-> **Nouvelle Interface unifiée `Sales Closer Souverain`** dans Airtable Interfaces (rattrape défi 1 + livre défi 2) : pages CRM (Prospects à relancer / Kanban pipeline) + pages CR-RDV (Prendre un CR / CRs récents / CR à envoyer) -- meilleure démo Eva

---

## II- Étape 1 -- Renommer la base + importer le CSV (2 min)

### 1.a -- Renommer la base

1- Ouvre ta base Airtable `CRM Souverain` (celle du défi 1)

2- Clique sur le nom `CRM Souverain` en haut à gauche -> **Renommer la base** -> saisis `Sales Closer Souverain` -> Entrée

3- Confirme -- toutes les données du défi 1 (`SC_Prospects`, 4 vues, formulaire, automation email récap, Interface `CRM Souverain`, 4 seeds Emma/Alice/Bob/Chloé) restent intactes

Note : l'Interface Airtable existante garde son nom `CRM Souverain` pour l'instant (elle sera remplacée à l'Étape 6 par une nouvelle Interface unifiée `Sales Closer Souverain`).

### 1.b -- Importer le CSV de la nouvelle table

1- En bas de la barre d'onglets de tables (à côté de `SC_Prospects`), clique **+ Ajouter ou importer** -> **Importer les données** -> **Fichier CSV**

2- Upload le fichier `seed/crs-seed.csv` (dans ce dossier az-no-code/defis/cr-rdv-formate)

3- Airtable détecte les 6 colonnes (Notes brutes / Date RDV / Interlocuteur / Sujet / Statut / Modèle utilisé) -> **Importer**

4- La nouvelle table est créée -- clique droit sur son onglet -> **Renommer la table** -> `SC_CRs_de_RDV` (underscore cohérent avec `SC_Prospects`)

À ce stade : la table `SC_CRs_de_RDV` contient 4 lignes toutes en `Statut = À formater`, sans `CR formaté` ni métadonnées parsées (ces champs seront écrits par l'automation à l'Étape 4).

---

## III- Étape 2 -- Ajuster les types de champs + FK Prospect (4 min)

### 2.a -- Ajuster les types de `SC_CRs_de_RDV`

Airtable détecte automatiquement mais 2 champs ont besoin d'être forcés :

-> **Statut** -> clic entête -> **Personnaliser le type de champ** -> **Sélection unique** -> ajoute les 4 options : `À formater`, `Formaté`, `Envoyé au client`, `Erreur` (couleurs libres)

-> **Date RDV** -> clic entête -> **Personnaliser le type de champ** -> **Date** -> coche **Inclure un champ heure** -> **Enregistrer**

Ajoute maintenant les 2 champs manquants (pour recevoir le retour du webhook SB_WF10) :

-> Clic **+** en fin de tableau -> nomme `CR formaté` -> type **Texte long** -> coche **Activer le formatage enrichi (rich text)**

-> Clic **+** -> nomme `Créé le` -> type **Date de création**

Optionnel (formule d'affichage) :

-> Clic **+** -> nomme `Nom` -> type **Formule** -> colle : `IF({Interlocuteur}, {Interlocuteur} & " -- " & DATETIME_FORMAT({Date RDV}, "DD/MM/YYYY"), "CR du " & DATETIME_FORMAT(CREATED_TIME(), "DD/MM/YYYY HH:mm"))` -> déplace ce champ en 1ère position (drag & drop de l'entête).

### 2.b -- Ajouter le champ Prospect linké vers SC_Prospects existante

Toujours dans `SC_CRs_de_RDV` :

1- Clic **+** en fin de tableau -> nomme le champ `Prospect`

2- Type -> **Lien vers un autre enregistrement** -> sélectionne la table **`SC_Prospects`** (existante, celle du défi 1 avec Emma/Alice/Bob/Chloé)

3- **Autoriser la liaison à plusieurs enregistrements** -> **décoché** (un CR concerne un seul prospect)

4- **Créer**

Le champ apparaît vide sur les 4 seeds -- normal. Il pourra être rempli à l'usage : quand tu prendras un CR de RDV avec Emma Petit (par exemple), tu tapes son nom dans ce champ et le dropdown searchable Airtable propose l'entrée `SC_Prospects` existante -> 1 clic, rattachement fait, pas de re-saisie.

---

## IV- Étape 3 -- Créer les 3 vues sur SC_CRs_de_RDV (3 min)

Dans la barre latérale gauche (icône **Vues**) de la table `SC_CRs_de_RDV`, clique **+ Créer...** :

-> **Vue Grille** nommée `Nouveaux à formater` -> filtre `Statut est À formater`

-> **Vue Grille** nommée `Récents` -> tri `Créé le` décroissant -> **Trier par 1 champ**

-> **Vue Grille** nommée `À envoyer au client` -> filtre `Statut est Formaté` -> groupé par `Interlocuteur`

---

## V- Étape 4 -- Monter l'automation webhook SB_WF10 (5 min)

1- Onglet **Automatisations** (en haut de la base) -> **Créer une automatisation** -> nomme `Formatage CR via SB_WF10`

2- **Déclencheur** -> **Lorsqu'un enregistrement entre dans une vue** -> Table `SC_CRs_de_RDV` -> Vue `Nouveaux à formater`

3- **Ajouter une action** -> **Envoyer une requête webhook**

4- Configure l'action :

**Méthode** : `POST`

**URL** :

```
https://sb-n8n.coolify.salescloser.fr/webhook/cr-rdv-format
```

(vérifie l'URL exacte du webhook SB_WF10 dans ton n8n -> workflow "SB_WF10 CR-RDV formatage v1.1.0" -> node `Webhook` -> onglet **Production URL**)

**En-têtes (Headers)** :

```
X-Webhook-Secret: <valeur du secret Bw "n8n Webhook Secret - CR-RDV">
Content-Type: application/json
```

**Corps (Body)** (type `JSON`) :

```json
{
  "notesBrutes": "<clic 'Insérer une valeur' -> Enregistrement déclencheur -> Notes brutes>",
  "modeleLlm": "anthropic/claude-sonnet-5",
  "airtableRecordId": "<clic 'Insérer une valeur' -> Enregistrement déclencheur -> Airtable record ID>"
}
```

5- **Tester l'action** -> l'automation envoie la requête au webhook, tu vois la réponse n8n en direct (attends 5-10 sec, tu dois recevoir un JSON avec `crFormate`, `dateRdv`, `interlocuteur`, `sujet`, `modeleLlm`).

6- **Ajouter une action** (après le webhook) -> **Mettre à jour un enregistrement** -> Table `SC_CRs_de_RDV` -> Enregistrement `Enregistrement déclencheur` -> renseigne :

-> `CR formaté` = valeur `crFormate` du step précédent

-> `Date RDV` = valeur `dateRdv`

-> `Interlocuteur` = valeur `interlocuteur`

-> `Sujet` = valeur `sujet`

-> `Statut` = `Formaté` (valeur fixe)

-> `Modèle utilisé` = valeur `modeleLlm`

7- **Activer** en haut à droite de la page automatisation.

---

## VI- Étape 5 -- Créer le formulaire d'ajout (2 min)

1- Retourne dans la table `SC_CRs_de_RDV` -> sidebar gauche -> **+ Créer...** -> **Formulaire**

2- Nomme le formulaire `Prendre un CR`

3- Décoche tous les champs sauf `Notes brutes` (les autres seront remplis par l'automation)

4- Dans les **Paramètres du formulaire** (roue crantée en bas) :

-> Titre : `Colle tes notes brutes de RDV`

-> Description : `Format libre. L'agent formate le CR en 5-10 sec.`

-> Bouton envoi : `Formater le CR`

-> **Après envoi** : `Afficher un message personnalisable` -> `CR en cours de formatage. Rafraîchis la vue Récents dans 10 secondes.`

5- Ajoute une automation pour que le statut soit bien `À formater` dès la soumission :

-> Retour dans **Automatisations** -> **Créer une automatisation** -> nomme `Init statut sur nouveau CR`

-> Déclencheur : `Lorsqu'un formulaire est envoyé` -> Formulaire `Prendre un CR`

-> Action : `Mettre à jour un enregistrement` -> Table `SC_CRs_de_RDV` -> Enregistrement `Enregistrement déclencheur` -> `Statut` = `À formater`

-> **Activer**

6- Copie l'**URL de partage du formulaire** (bouton en haut à droite du formulaire) -> c'est le lien que tu donneras au client.

---

## VII- Étape 6 -- Test end-to-end (2 min)

Test A -- via les 4 seeds :

1- Ouvre la vue `Nouveaux à formater` -> tu vois les 4 lignes seed

2- Onglet **Automatisations** -> `Formatage CR via SB_WF10` -> **Historique des exécutions** -> **Exécuter pour tous les enregistrements correspondants** (Airtable propose ça sur l'automation qui a un déclencheur "Lorsqu'un enregistrement entre dans une vue" si des enregistrements matchent déjà)

3- Attends 30-60 sec (4 CR à générer) -> les 4 lignes passent en `Statut = Formaté` avec `CR formaté` rempli, accents FR complets, année 2026 correcte

Test B -- via le formulaire :

1- Ouvre le lien du formulaire dans un onglet incognito

2- Colle des notes brutes (n'importe lesquelles, ex : `rdv thomas milano 10 sept 11h refonte site 3k budget`) -> **Formater le CR**

3- Retour dans Airtable -> vue `Récents` -> la nouvelle ligne apparaît en `À formater` puis passe `Formaté` en 5-10 sec

---

## VIII- Étape 7 -- Monter l'Interface unifiée Sales Closer Souverain (10 min)

L'interface actuelle `CRM Souverain` (livrée défi 1 avec 4 pages : `CRM A relancer`, `Tous Prospects - Sales Closer`, `Prospects A Relancer`, `Nouveau Prospect Sales Closer`) reste en place comme historique. On monte une **nouvelle interface unifiée** qui embarque le CRM ET le CR-RDV -- c'est celle-là que tu partageras à Eva.

1- Onglet **Interfaces** (barre de nav en haut) -> **+ Nouvelle interface** -> nomme-la `Sales Closer Souverain`

2- Choisis la mise en page **Application de suivi** (Dashboard + pages)

3- **Page 1 -- `Accueil`** : vue d'ensemble

-> Éléments : `Compteur` -> nombre de prospects à relancer (`SC_Prospects` filtré Statut = Nouveau OR En cours + Prochaine relance <= aujourd'hui)

-> `Compteur` -> nombre de CRs à formater (`SC_CRs_de_RDV` filtré Statut = À formater)

-> `Compteur` -> nombre de CRs à envoyer au client (`SC_CRs_de_RDV` filtré Statut = Formaté)

-> Bouton d'action `+ Prendre un CR` -> ouvre le formulaire `Prendre un CR` (URL publique)

-> Bouton d'action `+ Ajouter Prospect` -> ouvre le formulaire du défi 1

4- **Page 2 -- `Prospects à relancer`** : réutilise la vue Grille du défi 1 (filtre Statut Nouveau/En cours + Prochaine relance <= aujourd'hui, tri par urgence, coloration rouge/orange/jaune)

5- **Page 3 -- `Pipeline`** : réutilise la vue Kanban du défi 1 (`SC_Prospects` groupé par Statut)

6- **Page 4 -- `Nouveaux CRs à formater`** : Grille sur `SC_CRs_de_RDV` filtre Statut = À formater (colonnes : Créé le, Notes brutes tronquées 200 chars, Statut)

7- **Page 5 -- `CRs récents`** : Grille sur `SC_CRs_de_RDV` tri Créé le décroissant, limite 20 (colonnes : Interlocuteur, Sujet, Date RDV, Prospect linké, CR formaté)

8- **Page 6 -- `CRs à envoyer au client`** : Grille sur `SC_CRs_de_RDV` filtre Statut = Formaté, groupé par Interlocuteur (prêt à copier dans un mail)

9- **Page 7 -- `Prendre un CR`** : embed du formulaire `Prendre un CR` (ou lien vers son URL publique)

10- **Publier** l'interface -> URL partageable pour Eva et démo Alegria

---

## IX- Ce que tu montres à Eva/Alegria

Une fois monté, tu as 4 démos possibles à partager en canal :

-> **Interface unifiée `Sales Closer Souverain`** (lien partagé) -> le client voit tout d'un dashboard : prospects à relancer + CRs à formater/envoyer + boutons d'action `+ Prendre un CR` et `+ Ajouter Prospect`

-> **Formulaire public `Prendre un CR`** (lien Share Form) -> le client colle ses notes, le CR arrive en 10 sec -> aucun outil à apprendre

-> **Vue `CRs récents`** dans l'interface -> le client voit ses 20 derniers CR, tableau lisible, colonne `CR formaté` en markdown rendu

-> **Vue `CRs à envoyer au client`** dans l'interface -> groupée par interlocuteur, prête à copier dans un mail

Le tout en no-code, tout dans Airtable, backend n8n mutualisé avec la version code souverain.

**Rappel Alegria** : comme pour le défi 1, tu partages sur Dd Alegria l'interface v2 comme partage de réussite, ET tu rappelles dans un autre salon les autres livrables (RUNBOOK, PROMPT_LLM.md portable, code souverain Next.js, SB_WF10 workflow n8n, 3 chemins d'usage §XI).

---

## X- Limitations Niveau 2 (assumées)

-> **Dépendance au workflow n8n SB_WF10** : si sb-n8n.coolify.salescloser.fr tombe, les CR arrêtent de se formater (mais les CR déjà générés restent dans Airtable, aucune perte). Mitigation : fallback Niveau 1 (OpenAI natif Airtable) documenté dans le README pour les cas de secours.

-> **PDF** : Airtable ne génère pas de PDF natif propre. Contournement : impression navigateur depuis la vue détail, ou passer par Page Designer add-on (payant), ou basculer sur la version code souverain qui a un vrai rendu PDF.

-> **Quota automations** : 25 exécutions/mois plan Free, 25 000/mois plan Team. 5 CR/semaine = 20/mois -> plan Free suffit.

-> **Interface Designer** : limite de 3 interfaces publiées sur plan Free (une par défi = ok pour 2-3 défis, à surveiller au 4e). Plan Team = illimité.

---

## XI- Après le défi

Si le client veut aller plus loin (v0.2 du défi) :

-> Renommer l'ancienne interface `CRM Souverain` en `CRM Souverain (historique défi 1)` puis la supprimer une fois que l'interface unifiée `Sales Closer Souverain` est validée en usage

-> Champ `Envoyé le` + automation "envoyer le CR par mail à l'interlocuteur" (Gmail/Brevo)

-> Passer à la version code souverain : `az-code/defis/cr-rdv-souverain` (Next.js + Neon + Prisma + PDF via `@react-pdf/renderer`)

-> Ajouter la brique facturation (défi Alegria futur) -> table `SC_Factures` + FK vers `SC_Prospects`, alimentée par les CRs signés

---

## XII- Les 3 chemins d'usage SB_WF10 (référence -- même workflow, 3 clients)

Le workflow n8n **SB_WF10 v1.1.0** (validé end-to-end sur Julie Marchand + Karim Benhaddad avec accents FR complets et année 2026) est le même quel que soit le chemin. Ce qui change : qui l'appelle et où le résultat est stocké.

### Chemin A -- No-code Airtable (ce RUNBOOK)

Public cible : Eva PRO, formateurs Alegria, entrepreneurs no-code.

Flow : Airtable formulaire `Prendre un CR` -> automation Airtable **Send webhook** -> **SB_WF10** -> réponse JSON -> automation Airtable **Update record** -> ligne mise à jour dans `SC_CRs_de_RDV`.

Setup : 15 min via ce RUNBOOK. Coût : ~0,001-0,005€/CR (OpenRouter) + plan Airtable Free suffit jusqu'à 25 CR/mois.

Livrable client : URL Interface `Sales Closer Souverain` + URL formulaire `Prendre un CR`.

### Chemin B -- Code souverain (Next.js + Neon)

Public cible : entrepreneurs qui veulent le contrôle, la portabilité, l'API, le rendu PDF premium.

Flow : page Next.js `cr-rdv.davidruggieri.com` -> formulaire notes -> server action `creerCr` -> **SB_WF10** -> réponse JSON -> écriture dans Neon Postgres schéma `cr_rdv.crs` -> affichage markdown rendu -> bouton `Télécharger PDF` (`@react-pdf/renderer`).

Setup : deploy Vercel + variables env DATABASE_URL + N8N_WEBHOOK_URL + N8N_WEBHOOK_SECRET. Coût : ~0,001-0,005€/CR (OpenRouter) + Neon scale-to-zero (0€ tant qu'aucun trafic) + Vercel Hobby (0€ tant qu'aucun trafic).

Livrable client : URL `cr-rdv-souverain.vercel.app` (ou domaine personnalisé), historique CR consultable, PDF téléchargeable, rattachement futur à `crm-souverain` via cross-schema FK.

Voir `az-code/defis/cr-rdv-souverain/` (MVP livré v0.1) + `README.md`.

### Chemin C -- Prompt standalone (aucun outil)

Public cible : entrepreneurs qui veulent tester sans rien installer.

Flow : le client copie [PROMPT_LLM.md v1.1.0](https://github.com/DevDaveRug/az-code/blob/main/defis/cr-rdv-souverain/PROMPT_LLM.md) dans son propre ChatGPT / Claude / Mistral / Gemini -> colle ses notes -> reçoit le CR formaté.

Setup : 0 min. Coût : dans la limite du plan gratuit de l'IA choisie.

Livrable client : le fichier PROMPT_LLM.md, portable et réutilisable N fois.

### Tableau comparatif

| Critère | Chemin A (Airtable) | Chemin B (Code) | Chemin C (Prompt seul) |
|---|---|---|---|
| Setup client | 15 min RUNBOOK | 0 (déjà déployé) | 30 sec (copier-coller) |
| Interface | Interface Airtable native + Formulaire | Page Next.js + rendu markdown + PDF | L'IA choisie par le client |
| Persistance des CRs | Airtable | Neon Postgres | Aucune (à copier ailleurs) |
| Rattachement prospect CRM | oui (FK linkée SC_Prospects) | oui (FK cr_rdv.prospect_id -> crm.prospects) | non |
| PDF | non (impression navigateur) | oui (react-pdf) | non |
| Souveraineté | moyenne (Airtable + n8n) | forte (Neon self-host possible) | totale (aucune dépendance produit) |
| Coût par CR | ~0,001-0,005€ | ~0,001-0,005€ | plan gratuit de l'IA |
| Cible commerciale | démo Alegria / formateurs | prospects Enterprise / tech | prospects zero-friction |

**Point commun** : tous les 3 utilisent le même prompt système SB_WF10 v1.1.0 -- si tu bumpes le prompt (v1.2 avec section "Décisions prises" par exemple), les 3 chemins en bénéficient sans redéploiement client.

---

## XIII- Architecture bases connectables (référence)

### Principe : isolation par défaut, rattachement optionnel via clé unique

Chaque brique de la boîte-à-outils souveraine se livre seule ET peut se connecter aux autres sans re-saisie. Le client démarre par la brique qu'il veut, ajoute les autres à son rythme, sans jamais dupliquer un prospect.

### Côté no-code Airtable

**Une SEULE base par client** : `Sales Closer Souverain` (renommée depuis `CRM Souverain` à l'Étape 1). Toutes les briques cohabitent en tables séparées :

| Table | Origine | État après ce runbook |
|---|---|---|
| `SC_Prospects` | défi 1 crm-souverain (livré 2026-09-05) | 14 champs + 4 vues + formulaire + automation email récap lundi 9h + 4 seeds réels (Emma/Alice/Bob/Chloé) |
| `SC_CRs_de_RDV` | ce défi 2 | 6 champs importés CSV + 2 forcés + 2 ajoutés (CR formaté + Créé le) + FK Prospect + 3 vues + automation SB_WF10 + formulaire + 4 seeds fictifs |
| `SC_Entreprises` | crm-souverain v0.2 (à venir) | non créée |
| `SC_Interactions` | crm-souverain v0.2 (à venir) | non créée |
| `SC_Factures` | brique facturation future | non créée |

**Interface unifiée `Sales Closer Souverain`** : Interface Designer natif Airtable qui embarque toutes les tables ci-dessus dans un dashboard cohérent. L'ancienne interface `CRM Souverain` (défi 1) reste comme historique jusqu'à validation en usage.

### Côté code souverain (Next.js + Neon)

**Une SEULE base Neon `sales-closer-souverain`** partagée entre tous les projets Next.js de la boîte-à-outils. `DATABASE_URL` identique sur tous les projets Vercel.

Isolation par **schémas PostgreSQL** :

-> Schéma `crm` -> tables `crm.prospects`, `crm.entreprises`, `crm.interactions` (owned par `az-code/defis/crm-souverain`)

-> Schéma `cr_rdv` -> table `cr_rdv.crs` avec FK optionnelle `prospect_id references crm.prospects(id) on delete set null` (owned par `az-code/defis/cr-rdv-souverain`)

-> Chaque `schema.prisma` déclare `previewFeatures = ["multiSchema"]` + `schemas = ["<son propre schéma>"]` -> les migrations Prisma des différentes briques ne se marchent pas dessus.

### Miroir no-code / code

| Niveau | No-code Airtable | Code souverain Neon |
|---|---|---|
| Conteneur | 1 base `Sales Closer Souverain` | 1 DB `sales-closer-souverain` |
| Séparation par brique | tables `SC_*` (SC_Prospects, SC_CRs_de_RDV, ...) | schémas Postgres (`crm`, `cr_rdv`, ...) |
| Lien inter-briques | `Lien vers un autre enregistrement` (Airtable natif) | FK PostgreSQL (Prisma multiSchema) |
| Coût si standalone | 0€ (table vide autorisée) | 0€ (schéma vide autorisé) |
| Migration standalone -> combo | remplir la table concernée | déployer la nouvelle brique + `prisma db push` |

---

## Changelog

-> 1.2.0 -- 2026-09-10 (S133z-ccweb, Val David après Cor "ignore l'existant") : refonte complète après reconnaissance que crm-souverain (défi 1) était déjà livré avec base `CRM Souverain` + table `SC_Prospects` (14 champs, correction Eva : Emma Petit / Alice Martin / Bob Durand / Chloé Dubois) + 4 vues + formulaire + automation email récap + Interface `CRM Souverain` (4 pages). Le RUNBOOK v1.2.0 **étend** cette base existante (renommage `CRM Souverain` -> `Sales Closer Souverain`, ajout table `SC_CRs_de_RDV`, FK Prospect vers SC_Prospects). Nouvelle Étape 7 : Interface unifiée `Sales Closer Souverain` (rattrape défi 1 + livre défi 2). Nouvelle section XII : 3 chemins d'usage SB_WF10 (A no-code Airtable / B code souverain / C prompt standalone). Section XIII (ex-XI) : architecture bases connectables mise à jour avec vrais objets existants. Labels UI adaptés en français (Airtable de David en FR). Impact temps toi : 20 min (10 min étapes 1-6 + 10 min Étape 7 Interface).

-> 1.1.0 -- 2026-09-10 (S133z-ccweb, Val David) : refonte "architecture bases connectables" (Val David) -- base renommée `Sales Closer Souverain` (une seule pour toute la boîte-à-outils), table `Prospects` créée dès l'Étape 2.b (4 champs, vide au démarrage), champ `Prospect` linké ajouté dans `CRs de RDV` (Étape 2.c), section XI "Architecture bases connectables" détaillant le miroir no-code Airtable / code Neon. **Obsolète v1.2.0** : cette version ignorait le défi 1 déjà livré et proposait de créer une base from scratch. Cor David S133z-ccweb.

-> 1.0.0 -- 2026-09-10 (S133z-ccweb, Val David) : création. Runbook 10-15 min pour construction manuelle de la base Airtable connectée à SB_WF10 v1.1.0. Compagnon du CSV `seed/crs-seed.csv` (4 lignes fictives). Livrable défi Alegria Eva PRO 2026-09-08.