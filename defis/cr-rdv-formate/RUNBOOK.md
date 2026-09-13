# RUNBOOK -- extension base Airtable Sales Closer Souverain (défi 2 CR-RDV)

Version : 1.5.4
Date : 2026-09-12
Statut : Actif -- livrable défi Alegria Eva PRO 2026-09-08

> Guide copy-paste pour étendre la base Airtable existante **`CRM Souverain`** (défi 1 crm-souverain livré 2026-09-05, avec correction Eva) en y ajoutant la table CR-RDV connectée au workflow n8n **SB_WF10-2 v1.0.0** (dérivé de SB_WF10 v1.1.0, adapté Airtable Trigger + Update Record -- importable en 30 sec via URL raw GitHub, Niveau 2, accents FR + année 2026 validés end-to-end sur Julie Marchand + Karim Benhaddad).
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

Airtable détecte automatiquement mais 1 champ a besoin d'être forcé :

-> **Statut** -> clic entête -> **Personnaliser le type de champ** -> **Sélection unique** -> ajoute les 4 options : `À formater`, `Formaté`, `Envoyé au client`, `Erreur` (couleurs libres)

-> **Date RDV** -> Airtable détecte le format ISO du CSV et inclut déjà le champ heure par défaut -> **rien à faire, passer à la suite** (vérifie que la case "Inclure un champ heure" est bien cochée si tu veux double-check).

Ajoute maintenant les 2 champs manquants (pour recevoir le retour du workflow SB_WF10) :

-> Clic **+** en fin de tableau -> nomme `CR formaté` -> type **Texte long** -> coche **Activer le formatage enrichi (rich text)**

-> Clic **+** -> nomme `Date création` -> type **Date de création**

   Note : le nom `Date création` (avec accent aigu sur le `é`) est safe côté API Airtable. Les noms `Créé le` (avec espace + majuscule + accent + minuscule) ont eu des soucis d'encoding en session S133z-ccweb (débug live) -- prend `Date création` par défaut.

Optionnel (formule d'affichage) :

-> Clic **+** -> nomme `Nom` -> type **Formule** -> colle : `IF({Interlocuteur}, {Interlocuteur} & " -- " & DATETIME_FORMAT({Date RDV}, "DD/MM/YYYY"), "CR du " & DATETIME_FORMAT(CREATED_TIME(), "DD/MM/YYYY HH:mm"))` -> déplace ce champ en 1ère position (drag & drop de l'entête).

### 2.b -- Ajouter le champ Prospect linké vers SC_Prospects existante

Toujours dans `SC_CRs_de_RDV` :

1- Clic **+** en fin de tableau -> nomme le champ `Prospect`

2- Type -> **Lien vers un autre enregistrement** -> sélectionne la table **`SC_Prospects`** (existante, celle du défi 1 avec Emma/Alice/Bob/Chloé)

3- **Autoriser la liaison à plusieurs enregistrements** -> **décoché** (un CR concerne un seul prospect)

4- **Créer**

**Note comportement Airtable** : à la création du champ lié, Airtable IMPOSE de choisir un champ de la table `SC_Prospects` à afficher en "lookup" dans `SC_CRs_de_RDV` (typiquement `Nom complet`). Tu peux le supprimer immédiatement après création si tu ne veux pas de colonne lookup redondante -- le lien fonctionne indépendamment (le nom du prospect s'affiche déjà dans le champ `Prospect` linké lui-même).

Le champ apparaît vide sur les 4 seeds -- normal. Il pourra être rempli à l'usage : quand tu prendras un CR de RDV avec Emma Petit (par exemple), tu tapes son nom dans ce champ et le dropdown searchable Airtable propose l'entrée `SC_Prospects` existante -> 1 clic, rattachement fait, pas de re-saisie.

---

## IV- Étape 3 -- Créer les 3 vues sur SC_CRs_de_RDV (3 min)

Dans la barre latérale gauche (icône **Vues**) de la table `SC_CRs_de_RDV`, clique **+ Créer...** :

-> **Vue Grille** nommée `Nouveaux à formater` -> filtre `Statut est À formater`

-> **Vue Grille** nommée `Récents` -> tri `Date création` décroissant -> **Trier par 1 champ**

   Note : trier par `Date création` (le champ créé à l'Étape 2.a) plutôt que par `Créé le` (champ système Airtable implicite) garde la cohérence avec le Trigger Field du workflow n8n §V.4.d. Airtable propose les deux dans les options de tri, ils fonctionnent tous les deux -- mais un seul est cité partout dans ce runbook.

-> **Vue Grille** nommée `À envoyer au client` -> filtre `Statut est Formaté` -> groupé par `Interlocuteur`

---

## V- Étape 4 -- Brancher SB_WF10-2 sur Airtable via n8n Airtable Trigger (6 min via import JSON)

### 4.a -- Pourquoi cette approche (contexte v1.5.0)

Airtable a déplacé l'action `Envoyer une requête webhook` sur le plan **Team payant (~24€/mois)** en 2024. Pour rester sur plan **Free**, on inverse la logique : au lieu qu'Airtable pousse vers n8n, c'est **n8n qui vient chercher dans Airtable** via son node `Airtable Trigger` natif (polling API). Zéro action payante Airtable côté client. **n8n est accessible en no-code** via 2 chemins :

-> **n8n Cloud tier free** : `n8n.cloud` -> signup gratuit -> 5000 exécutions/mois, workflows illimités, hosting managé. Idéal élèves Alegria débutants.

-> **n8n self-host gratuit** : container Docker sur Coolify (VPS Hostinger 4€/mois), Railway (5$/mois avec crédit gratuit), ou même Render tier free. Souveraineté totale pour ceux qui veulent maîtriser leur infra.

### 4.b -- Prérequis élève Alegria (10 min setup une seule fois)

1- **Compte Airtable Free** -- déjà fait si tu as la base `Sales Closer Souverain` montée aux Étapes 1-3

2- **Compte n8n gratuit** :

-> Débutants -> `https://n8n.cloud` -> Sign up -> tier free automatique

-> Avancés self-host -> voir `dr-context/docs/DR/DR_Professionnel/Pr_Outils/PrOu_SecondBrain/PrOuSb_Docs/260911_PrOu_N8N-COOLIFY-SETUP.md` (guide 20 min : VPS 4-6€/mois + Coolify one-click + HTTPS auto + n8n déployé, illimité en volume)

3- **Compte OpenRouter** (LLM au token) -> `https://openrouter.ai` -> Sign up -> **Credits** -> charge 5€ (couvre ~1000-5000 CR selon modèle). Copie la clé API `sk-or-v1-XXX`, range dans Bw sous `OpenRouter API Key`.

4- **PAT Airtable** (Personal Access Token) -> ton compte Airtable -> icône profil (haut droite) -> **Developer hub** -> **Personal access tokens** -> **Create new token** :

-> Nom : `n8n SB_WF10 CR-RDV`

-> Scopes : coche `data.records:read` + `data.records:write` + `schema.bases:read`

-> Access : sélectionne la base `Sales Closer Souverain`

-> **Create** -> copie le token `patXXXXXXXXXXXX` (il ne s'affiche qu'une fois), range dans Bw sous `n8n Airtable PAT - SC Souverain`

### 4.c -- Importer le template SB_WF10-2 depuis dr-context (30 sec)

Le workflow **SB_WF10-2 CR-RDV via Airtable Trigger v1.1.0** est fourni comme template JSON prêt à l'emploi dans `dr-context` (dérivé de SB_WF10 v1.1.0 avec Airtable Trigger + Update Record déjà câblés, **bulletproof après debug live 5 pièges S133z**) :

**URL raw GitHub** :

```
https://raw.githubusercontent.com/DevDaveRug/dr-context/main/docs/DR/DR_Professionnel/Pr_Outils/PrOu_SecondBrain/PrOuSb_Workflows/260911_PrOu_SB_WF10-2-cr-rdv-airtable-trigger-v1_1_0.json
```

Import dans ton n8n :

1- Ouvre ton n8n (`n8n.cloud` ou ton self-host) -> **+ New workflow**

2- Menu 3 points (haut droite) -> **Import from URL** -> colle l'URL raw ci-dessus -> **Import**

3- Le workflow apparaît avec 6 nodes déjà câblés :

`Airtable Trigger` -> `Preparer prompts + inputs` -> `OpenRouter formatage CR` -> `OpenRouter extraction metadonnees` -> `Assembler valeurs Airtable` -> `Airtable Update Record`

Alternative si l'import URL ne marche pas (versions anciennes de n8n) : télécharge le JSON en local (bouton `Raw` sur GitHub -> Ctrl+S), puis Menu 3 points -> **Import from File** -> sélectionne le `.json`.

### 4.d -- Configurer les credentials + Base/Table/View (5 min)

Le template contient des placeholders explicites à remplacer via l'UI n8n :

**1- Credentials Airtable (sur les nodes `Airtable Trigger` ET `Airtable Update Record`)**

-> Clic sur le node `Airtable Trigger` -> section **Credentials** -> **Create New**

-> Type : **Airtable Token API**

-> Colle ton PAT Airtable (`patXXXXXXXXXXXX` de l'Étape 4.b.4)

-> Nomme le credential : `Airtable PAT - Sales Closer Souverain`

-> **Save**

-> Retourne sur le node `Airtable Update Record` -> le même credential apparaît dans le dropdown -> sélectionne-le (un seul credential pour les 2 nodes).

**2- Credentials OpenRouter (sur les nodes `OpenRouter formatage CR` ET `OpenRouter extraction metadonnees`)**

-> Clic sur le node `OpenRouter formatage CR` -> section **Credentials** -> **Create New**

-> Type : **OpenRouter API**

-> Colle ta clé OpenRouter (`sk-or-v1-XXX` de l'Étape 4.b.3)

-> Nomme : `OpenRouter API`

-> **Save**

-> Retourne sur le node `OpenRouter extraction metadonnees` -> sélectionne le même credential dans le dropdown.

**3- Base + Table + View (sur les 2 nodes Airtable)**

Sur `Airtable Trigger` :

-> **Base** : sélectionne `Sales Closer Souverain` dans le dropdown (n8n liste tes bases via ton PAT)

-> **Table** : `SC_CRs_de_RDV`

-> **Trigger Field** : `Date création` (le nom du champ Airtable créé à l'Étape 2.a -- le template pré-remplit cette valeur)

-> **Poll Times** : `Every Minute` par défaut du template (ou passe à `Every X Minutes` = `5` pour économiser le quota Airtable API sur plan Free -- voir §X)

-> **Additional Fields** :

   -> **Fields** : **laisser VIDE** -- n8n retourne tous les fields de la vue par défaut, ce qui inclut `Notes brutes`, `Date création`, et le reste. Le node Preparer + Assembler en aval ne lisent que `Notes brutes` + `id`, le reste passe silencieusement.

   -> **Formula** : **laisser VIDE** -- ce champ est un `filterByFormula` Airtable (filtre de records), pas un sélecteur de fields. Y mettre un array n8n `["Date création", "Notes brutes"]` déclenche une 422 à l'activation du workflow (mais passe silencieusement en Fetch Test Event, faux positif traître -- piège corrigé S133z-ccweb).

   -> **View ID** : `Nouveaux à formater` (déjà pré-rempli par le template)

Sur `Airtable Update Record` :

-> **Base** : même `Sales Closer Souverain`

-> **Table** : même `SC_CRs_de_RDV`

-> **Mapping Column Mode** : `Map Automatically` (déjà par défaut du template v1.1.0)

-> **Columns to match on** : `id` (déjà par défaut du template) -- n8n extrait la clé `id` du JSON entrant (produite par le node Assembler) pour matcher la ligne à mettre à jour

-> Aucun mapping manuel de colonnes à configurer : le node auto-map les 6 clés (`CR formaté`, `Date RDV`, `Interlocuteur`, `Sujet`, `Statut`, `Modèle utilisé`) vers les colonnes Airtable homonymes. Le node Assembler du template émet directement ces noms exacts.

### 4.e -- Activation + test (30 sec)

1- Menu haut droite -> **Save** (Ctrl+S) -> nom du workflow : `SB_WF10-2 CR-RDV via Airtable Trigger v1.0.0` (déjà positionné par le template)

2- Toggle **Active** (haut droite) -> vert

3- **Test end-to-end** : dans Airtable, ouvre la vue `Nouveaux à formater` de `SC_CRs_de_RDV` -> tu vois les 4 seeds -> attends 1-5 min (délai polling) -> les 4 lignes passent en `Statut = Formaté` avec `CR formaté` rempli, accents FR complets, année 2026 correcte.

Pour forcer un test immédiat sans attendre le polling : sur le node `Airtable Trigger` -> bouton **Execute node** (play) -> déclenche manuellement une passe complète sur les records matchant la vue.

### 4.f -- Alternative : duplication manuelle depuis SB_WF10 (pour comprendre chaque node)

Cette alternative est utile si tu veux comprendre chaque node en le construisant à la main (utile pédagogiquement), OU si tu as déjà SB_WF10 v1.1.0 monté avec des credentials OpenRouter que tu veux réutiliser.

**Attention** : si tu prends ce chemin, applique les **5 corrections listées dans le README dr-context** (`260911_PrOu_SB_WF10-2-README.md` §VI Troubleshooting) sinon tu vas re-vivre le debug live de la session S133z-ccweb (30 min perdues sur des pièges d'UI n8n Airtable v2.1). Résumé des 5 pièges à connaître : (a) `Trigger Field` = `Date création` (pas `createdTime`) ; (b) `Additional Fields > Formula` (array) au lieu de `Fields` (single input) ; (c) node Assembler émet directement les noms Airtable + `id` top-level ; (d) node Update Record en `Map Automatically` (pas Manual) ; (e) filtre vue `Nouveaux à formater` inclut aussi `Statut vide`.

1- Importe d'abord SB_WF10 v1.1.0 (si pas déjà présent) depuis :

```
https://raw.githubusercontent.com/DevDaveRug/dr-context/main/docs/DR/DR_Professionnel/Pr_Outils/PrOu_SecondBrain/PrOuSb_Workflows/260910_PrOu_SB_WF10-cr-rdv-formatage-v1_1_0.json
```

2- Duplique-le en `SB_WF10-2 CR-RDV via Airtable Trigger` (Menu -> Duplicate)

3- Remplace le node `Webhook` en tête par un node **Airtable Trigger** (config identique à 4.d.3 sur `Airtable Trigger`)

4- Adapte le node `Preparer prompts + inputs` (Code JavaScript) pour lire depuis `$json.fields` (structure Airtable) au lieu de `$json.body` (structure Webhook) -- la première ligne devient :

```javascript
const fields = $json.fields || $json;
const notesBrutes = String(fields['Notes brutes'] || fields.notesBrutes || '').trim();
const modeleLlm = 'anthropic/claude-sonnet-5';
```

5- Remplace le node `Respond to webhook` final par un node **Airtable Update Record** (config identique à 4.d.3 sur `Airtable Update Record`), avec mapping des 6 fields sur les valeurs sortantes du node Assembler.

6- Save + Active.

Temps setup : ~10 min (versus 30 sec via l'import template). Les deux méthodes aboutissent au même workflow fonctionnel. Voir aussi le README compagnon : `dr-context/docs/DR/DR_Professionnel/Pr_Outils/PrOu_SecondBrain/PrOuSb_Docs/260911_PrOu_SB_WF10-2-README.md`.

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

-> Action : `Mettre à jour un enregistrement` -> Table `SC_CRs_de_RDV` -> Enregistrement `Enregistrement déclencheur` -> **Champ `Statut`** -> bascule du mode **par défaut** vers le mode **Custom** (case à cocher / icône crayon à droite du champ, selon la version Airtable) -> saisis manuellement `À formater` dans le champ texte.

   **Piège S133z-ccweb à éviter** : en mode par défaut, Airtable exige une source de données (un champ du record déclencheur) et refuse une chaîne littérale -> l'automation plante à l'exécution avec `Réception d'entrées non valides` (l'erreur est silencieuse tant que tu n'as pas testé). Le mode Custom accepte une valeur en dur, c'est ce qu'il faut pour initialiser un statut fixe.

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

### 8.a -- Créer l'interface + composition (2 min)

1- Onglet **Interfaces** (barre de nav en haut) -> **+ Nouvelle interface** -> nomme-la `Sales Closer Souverain`

2- Choisis la composition **`Tableau de bord`** (PAS `Présentation` ni `Personnaliser` -- seul `Tableau de bord` fournit le combo dashboard + sidebar navigable qu'on veut).

   **Piège S133z-ccweb à éviter** : `Présentation` et `Personnaliser` sont proposés au-dessus dans la même modale et paraissent équivalents. Ils donnent des interfaces sans sidebar auto ou sans dashboard central -> re-monter tout à la main pour rien. Prendre `Tableau de bord` d'entrée.

### 8.b -- Ajouter les 6 pages dans la sidebar gauche (6 min)

L'ordre ci-dessous est celui de la sidebar de haut en bas. Toutes les pages se réfèrent à la base `Sales Closer Souverain` (celle où tu as `SC_Prospects` du défi 1 + `SC_CRs_de_RDV` du défi 2).

1- **Page 1 -- `Accueil`** (la première créée par défaut à l'étape 8.a) : vue d'ensemble

-> Élément **`Compteur`** -> nombre de prospects à relancer (`SC_Prospects` filtré Statut = `Nouveau` OR `En cours` + Prochaine relance <= aujourd'hui)

-> Élément **`Compteur`** -> nombre de CRs à formater (`SC_CRs_de_RDV` filtré Statut = `À formater`)

-> Élément **`Compteur`** -> nombre de CRs à envoyer au client (`SC_CRs_de_RDV` filtré Statut = `Formaté`)

-> Élément **`Bouton d'action`** `+ Prendre un CR` -> ouvre la page `Prendre un CR` (8.c ci-dessous)

-> Élément **`Bouton d'action`** `+ Ajouter un prospect` -> ouvre la page `Ajouter un prospect` (8.c ci-dessous)

2- **Page 2 -- `Prospects à relancer`** : réutilise la vue Grille du défi 1 (`SC_Prospects` filtre Statut Nouveau/En cours + Prochaine relance <= aujourd'hui, tri par urgence, coloration rouge/orange/jaune)

3- **Page 3 -- `Pipeline`** : réutilise la vue Kanban du défi 1 (`SC_Prospects` groupé par `Statut`)

4- **Page 4 -- `CRs à formater`** : Grille sur `SC_CRs_de_RDV` filtre Statut = `À formater` (colonnes : `Date création`, `Notes brutes` tronquées 200 chars, `Statut`)

5- **Page 5 -- `CRs récents 30j`** : Grille sur `SC_CRs_de_RDV` tri `Date création` décroissant, filtre `Date création est antérieur à nombre de jours à compter d'aujourd'hui = 20` (colonnes : `Interlocuteur`, `Sujet`, `Date RDV`, `Prospect` linké, `CR formaté`).

   **Piège S133z-ccweb à éviter (formulation Airtable FR ambiguë)** : dans l'interface française d'Airtable, le sélecteur de filtre pour une date propose `est antérieur à` + `nombre de jours à compter d'aujourd'hui`. Malgré la formulation qui semble dire l'inverse en français littéral (« antérieur à = plus vieux que »), ce couple garde **les records dont `Date création` est postérieure ou égale à `aujourd'hui - N jours`** (= les N derniers jours). C'est ce qu'on veut. La valeur `20` (au lieu de `30`) est un choix conservateur pour un défi Alegria : la vue reste courte et lisible même après plusieurs semaines d'usage. Bumper à 30/60/90 selon volume réel du client.

6- **Page 6 -- `CRs à envoyer au client`** : Grille sur `SC_CRs_de_RDV` filtre Statut = `Formaté`, groupé par `Interlocuteur` (prêt à copier dans un mail).

### 8.c -- Ajouter les 2 formulaires dans la sidebar (1 min)

Les 2 formulaires (celui du défi 1 `Ajouter un prospect` + celui du défi 2 `Prendre un CR`) sont ajoutés en bas de la sidebar comme deux entrées supplémentaires, pas comme des pages dashboard classiques. Ils sont invoqués par les boutons d'action de l'Accueil ET directement accessibles pour un opérateur qui préfère taper dans la sidebar.

1- Dans la sidebar gauche de l'interface -> **+ Ajouter une page** -> composition **`Formulaire`** -> choisis le formulaire `Prendre un CR` de la table `SC_CRs_de_RDV`. Nomme la page `Prendre un CR`.

2- Répète avec le formulaire `Ajouter un prospect` (défi 1, table `SC_Prospects`). Nomme la page `Ajouter un prospect`.

### 8.d -- Partager l'interface (1 min)

**Piège S133z-ccweb à éviter (URL publique 100% payante)** : le bouton `Publier -> URL publique` en haut à droite de l'interface donne un lien accessible sans compte Airtable... mais **uniquement sur le plan Team payant (~24€/mois)**. En plan Free (celui du défi Alegria), le bouton existe mais ne rend pas l'interface publique -> le lien renvoie un écran de login Airtable = inutilisable pour un client final ou un élève Alegria qui veut juste jeter un œil.

À la place, on partage via un **lien d'invitation en lecture**, gratuit et illimité en plan Free :

1- En haut à droite de l'interface -> **`Partager`** (icône silhouette / bouton `Share`).

2- Dans la modale, section **`Inviter par lien`** -> permission par défaut **`Read only`** (lecteur) -> **`Créer un lien`**.

3- Copie le lien `https://airtable.com/invite/l?inviteId=...` -> c'est celui-là que tu envoies à Eva et aux élèves Alegria.

   L'invité crée un compte Airtable Free en 30 sec s'il n'en a pas, puis a un accès lecture à la base ET à l'interface `Sales Closer Souverain`. Pour un déploiement client payant (plan Team), le bouton `Publier` fonctionne alors comme prévu et l'URL publique devient utilisable.

---

## IX- Ce que tu montres à Eva/Alegria

Une fois monté, tu as 4 démos possibles à partager en canal :

-> **Interface unifiée `Sales Closer Souverain`** (lien d'invitation en lecture, §VIII.8.d) -> le client voit tout d'un dashboard : prospects à relancer + CRs à formater/envoyer + boutons d'action `+ Prendre un CR` et `+ Ajouter un prospect`

-> **Formulaire public `Prendre un CR`** (lien Share Form -> URL publique du formulaire, celle-là est bien gratuite en plan Free contrairement à l'URL publique d'interface) -> le client colle ses notes, le CR arrive en 1-5 min -> aucun outil à apprendre

-> **Page `CRs récents 30j`** dans l'interface -> le client voit ses derniers CR sur les 20 derniers jours, tableau lisible, colonne `CR formaté` en markdown rendu

-> **Page `CRs à envoyer au client`** dans l'interface -> groupée par interlocuteur, prête à copier dans un mail

Le tout en no-code, tout dans Airtable + n8n (Airtable Free + n8n Cloud tier free = 0€/mois, seul coût = OpenRouter ~5€ crédit initial pour ~1000-5000 CR).

**Pour les élèves Alegria** : ils ont 2 chemins accessibles selon leur niveau :

-> **Débutants** -> Chemin C (Prompt standalone) -> ils collent [PROMPT_LLM.md](https://github.com/DevDaveRug/az-code/blob/main/defis/cr-rdv-souverain/PROMPT_LLM.md) dans leur ChatGPT/Claude/Mistral existant. Setup 30 sec, 0€, aucun outil à installer.

-> **Ambitieux** -> Chemin A (Airtable + n8n) -> ils suivent ce RUNBOOK avec leur propre compte n8n gratuit. Setup 20-25 min total (10 min prérequis + 6 min Étape 4 via import JSON SB_WF10-2 + Étapes 5-7 Airtable), 0€ récurrent, ~5€ OpenRouter au démarrage. Ils apprennent le vrai stack souverain (Airtable + n8n + OpenRouter) = compétence transférable à N autres cas d'usage.

**Rappel Alegria** : comme pour le défi 1, tu partages sur Dd Alegria l'interface v2 comme partage de réussite, ET tu rappelles dans un autre salon les autres livrables (RUNBOOK, PROMPT_LLM.md portable, code souverain Next.js, SB_WF10 workflow n8n, 3 chemins d'usage §XII).

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

Setup : 6 min via ce RUNBOOK (import JSON template SB_WF10-2 + config credentials). Coût : ~0,001-0,005€/CR (OpenRouter) + plan Airtable Free suffit jusqu'à 25 CR/mois.

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
| Setup client | 6 min (import JSON SB_WF10-2 + credentials) | 0 (déjà déployé) | 30 sec (copier-coller) |
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

-> 1.5.4 -- 2026-09-12 (S134z-ccweb, backlog S133z : 7 corrections restantes hors urgence) : PATCH -- finalise les corrections de la §V.5 et de la §VIII qui n'étaient pas passées dans v1.5.2/v1.5.3 (celles-ci s'étaient concentrées sur §III.2.a nom du champ `Date création`, §V.4.d Fields+Formula VIDES, §V.4.d Update Record Map Automatically). Livré :
   -> §IV.b vue `Récents` : tri par `Date création` (cohérence avec le Trigger Field n8n) au lieu du champ système `Créé le` -- note ajoutée expliquant que les deux fonctionnent mais qu'un seul est cité partout dans ce runbook.
   -> §V.5 automation `Init statut sur nouveau CR` : mode **`Custom`** obligatoire pour saisir la chaîne littérale `À formater` -- en mode par défaut Airtable exige un champ source et plante à l'exécution avec `Réception d'entrées non valides`. Piège S133z-ccweb documenté.
   -> §VIII.8.a : composition **`Tableau de bord`** explicite (PAS `Présentation` ni `Personnaliser` -- seul `Tableau de bord` fournit sidebar navigable + dashboard central). Piège S133z-ccweb documenté.
   -> §VIII.8.b : la sidebar de gauche liste 6 pages dashboard dans un ordre imposé (Accueil / Prospects à relancer / Pipeline / CRs à formater / CRs récents 30j / CRs à envoyer au client). Renommages : `Nouveaux CRs à formater` -> `CRs à formater`, `CRs récents` (limite 20 items non filtré par date) -> `CRs récents 30j` (avec filtre 20 derniers jours), `+ Ajouter Prospect` -> `+ Ajouter un prospect` (cohérence avec les labels formulaire du défi 1).
   -> §VIII.8.b page 4 + page 5 : colonne `Créé le` -> `Date création` (cohérence avec §IV.b).
   -> §VIII.8.b page 5 filtre 20 derniers jours : formulation Airtable FR ambiguë `est antérieur à nombre de jours à compter d'aujourd'hui = 20` documentée (le libellé français fait littéralement penser à l'inverse mais garde bien les records récents).
   -> §VIII.8.c : les 2 formulaires (`Prendre un CR` + `Ajouter un prospect`) sont ajoutés comme entrées sidebar séparées de la composition `Formulaire`, PAS comme pages dashboard classiques -- accessibles par les boutons d'action de l'Accueil ET directement dans la sidebar.
   -> §VIII.8.d : partage via **lien d'invitation en lecture** gratuit en plan Free (`https://airtable.com/invite/l?inviteId=...`) au lieu de l'URL publique de l'interface qui nécessite en réalité le plan Team payant (~24€/mois). Le bouton `Publier` existe en plan Free mais rend un lien qui renvoie sur écran de login -> piège S133z-ccweb documenté. L'URL publique du formulaire reste gratuite (`Share Form`).
   -> §IX : mentions "URL publique" et libellés de pages alignés sur §VIII v1.5.4.

-> 1.5.3 -- 2026-09-11 (S133z-ccweb, Cor David "SB_WF10-2 accepte pas d'être publiée -- 422 à l'activation") : PATCH -- correction §V.4.d Additional Fields : Fields ET Formula laissés VIDES (au lieu de Formula pré-remplie avec array v1.5.2). Le champ Formula est un `filterByFormula` Airtable (filtre de records), pas un sélecteur de fields ; y mettre un array n8n `["Date création", "Notes brutes"]` déclenche une 422 à l'activation du workflow (piège traître : passe silencieusement en Fetch Test Event). Correction alignée avec dr-context PR#449 v1.1.1 (retrait de la clé `formula` du template JSON). Test end-to-end reste validé sur les 4 seeds -- n8n retourne tous les fields de la vue par défaut, le Preparer + Assembler ne lisent que ce dont ils ont besoin.

-> 1.5.2 -- 2026-09-11 (S133z-ccweb, Cor David "pense à corriger les docs et le json du WF avec ces corrections !") : bulletproof de l'Étape 4 après debug live de 5 pièges de config n8n Airtable v2.1 découverts en session : (a) §III.2.a : nom du champ Airtable = `Date création` (accent aigu safe) au lieu de `Créé le` (encoding fragile) ; (b) §V.4.c : URL raw pointe vers `260911_PrOu_SB_WF10-2-cr-rdv-airtable-trigger-v1_1_0.json` (bump v1.0.0 -> v1.1.0 dans dr-context PR#449) ; (c) §V.4.d Trigger Field = `Date création` (au lieu de `Trigger On : View`) ; (d) §V.4.d Additional Fields = Formula avec array `={{ ["Date création", "Notes brutes"] }}` (au lieu de Fields single input buggé) ; (e) §V.4.d Update Record = Map Automatically + Columns to match on = `id` (au lieu de Record ID + fields customisés en Manual buggé) ; (f) §V.4.f note ajoutée pointant vers README dr-context §VI Troubleshooting pour la duplication manuelle. Test end-to-end validé sur 4 seeds Marie Dupont / Karim Bouziane / Chloé Renaud / JP Huber -> passés en Statut = Formaté avec CR formaté rempli.

-> 1.5.1 -- 2026-09-11 (S133z-ccweb, Cor David "N8N-COOLIFY-SETUP.md absent du clone") : fix dette technique cachée v1.5.0 §V.4.b -- le fichier `N8N-COOLIFY-SETUP.md` était référencé avec "(à créer si absent)" mais n'existait pas. Créé dans dr-context (PR#448) sous le nom code_archi `260911_PrOu_N8N-COOLIFY-SETUP.md` (guide 20 min : VPS 4-6€/mois + Coolify one-click + HTTPS auto + n8n déployé, illimité en volume). Chemin corrigé dans le RUNBOOK §V.4.b.

-> 1.5.0 -- 2026-09-11 (S133z-ccweb, Val David "y a t-il possibilité d'importer un json") : bascule §V (Étape 4) sur l'**import JSON template SB_WF10-2** comme méthode par défaut, unifiée pour élèves Alegria débutants ET David / power users. §4.c réduit à 30 sec (import URL raw GitHub du template `260911_PrOu_SB_WF10-2-cr-rdv-airtable-trigger-v1_0_0.json` dans `dr-context`). §4.d = configuration credentials Airtable + OpenRouter + Base/Table/View via UI n8n (5 min). §4.e = activation + test (30 sec). Ancienne procédure de remplacement de nodes (v1.4.0 §4.d) déplacée en §4.f "alternative duplication manuelle" pour ceux qui veulent comprendre chaque node. Section IX chemin A élèves ambitieux : setup passe de 30-45 min à 20-25 min total. Section XII chemin A + tableau comparatif : setup client passe de 15 min à 6 min. Compagnon dr-context : PR#447 (template JSON SB_WF10-2 v1.0.0 + README `260911_PrOu_SB_WF10-2-README.md`).

-> 1.4.0 -- 2026-09-10 (S133z-ccweb, Val David "Option A + n8n gratuit pour élèves Alegria") : refonte Étape 4 complète intégrant corrections v1.3.0 (jamais mergée) + pivot pédagogique. (a) Étape 2.a : Date RDV heure déjà par défaut à l'import CSV, skip la manip. (b) Étape 2.b : précision Airtable IMPOSE un lookup à la création du champ lié, supprimable ensuite. (c) Étape 4 refondue en Option A only (Airtable Trigger natif dans n8n) avec 2 sous-cas : (4.b-d) élève Alegria débutant qui crée son propre compte n8n gratuit (n8n Cloud tier free ou self-host Coolify) + PAT Airtable + clé OpenRouter, importe le workflow template SB_WF10 v1.1.0 depuis dr-context via URL raw GitHub ; (4.e) David / power users avec n8n existant qui dupliquent SB_WF10 en SB_WF10-2 en 5 min. Option B (Email trigger) retirée du corps principal (fallback documenté sur demande). Section IX mise à jour : élèves Alegria ont 2 chemins accessibles selon niveau (C débutants prompt standalone, A ambitieux Airtable + n8n). Impact temps toi : 30-45 min setup complet (versus 20 min v1.2.0 avec webhook payant), 0€ récurrent, ~5€ OpenRouter au démarrage.

-> 1.3.0 -- 2026-09-10 (S133z-ccweb, PR#8 mergée 2026-09-11) : proposait 3 corrections + 2 options A/B pour l'Étape 4. **Superseded** par v1.4.0 (PR#9) qui simplifie en Option A only après validation David que n8n gratuit est accessible aux élèves Alegria. Contenu des corrections 2.a + 2.b conservé dans v1.4.0, Étape 4 refondue en Option A seule (Option B email retirée).

-> 1.2.0 -- 2026-09-10 (S133z-ccweb, Val David après Cor "ignore l'existant") : refonte complète après reconnaissance que crm-souverain (défi 1) était déjà livré avec base `CRM Souverain` + table `SC_Prospects` (14 champs, correction Eva : Emma Petit / Alice Martin / Bob Durand / Chloé Dubois) + 4 vues + formulaire + automation email récap + Interface `CRM Souverain` (4 pages). Le RUNBOOK v1.2.0 **étend** cette base existante (renommage `CRM Souverain` -> `Sales Closer Souverain`, ajout table `SC_CRs_de_RDV`, FK Prospect vers SC_Prospects). Nouvelle Étape 7 : Interface unifiée `Sales Closer Souverain` (rattrape défi 1 + livre défi 2). Nouvelle section XII : 3 chemins d'usage SB_WF10 (A no-code Airtable / B code souverain / C prompt standalone). Section XIII (ex-XI) : architecture bases connectables mise à jour avec vrais objets existants. Labels UI adaptés en français (Airtable de David en FR). Impact temps toi : 20 min (10 min étapes 1-6 + 10 min Étape 7 Interface).

-> 1.1.0 -- 2026-09-10 (S133z-ccweb, Val David) : refonte "architecture bases connectables" (Val David) -- base renommée `Sales Closer Souverain` (une seule pour toute la boîte-à-outils), table `Prospects` créée dès l'Étape 2.b (4 champs, vide au démarrage), champ `Prospect` linké ajouté dans `CRs de RDV` (Étape 2.c), section XI "Architecture bases connectables" détaillant le miroir no-code Airtable / code Neon. **Obsolète v1.2.0** : cette version ignorait le défi 1 déjà livré et proposait de créer une base from scratch. Cor David S133z-ccweb.

-> 1.0.0 -- 2026-09-10 (S133z-ccweb, Val David) : création. Runbook 10-15 min pour construction manuelle de la base Airtable connectée à SB_WF10 v1.1.0. Compagnon du CSV `seed/crs-seed.csv` (4 lignes fictives). Livrable défi Alegria Eva PRO 2026-09-08.