# Une seule table de personnes : SC_Prospects -- guide d'uniformisation des 3 formats

Version : 1.2.2
Date : 2026-10-02
Session : S163z-ccweb (Cor David : « sélectionner un client ou un prospect et en ajouter un, il me faut ça partout »)
Compagnons : `/defi-hebdo-alegria` v1.8.1+ (règle table unique), `interfaces/AZ_PORTFOLIO_INTERFACE.md`

## La règle

-> Une seule table de personnes : `SC_Prospects`. Un client est un prospect au statut `Gagne`.

-> Chaque table de projet (missions, demandes, inscriptions, CR de RDV) porte un champ lien `Prospect` et ne recopie jamais l'identité (nom, e-mail, téléphone).

-> Le modèle du formulaire dépend de **qui le remplit** :

   -> **Toi ou ton équipe** (Nouvelle mission, Nouveau CR de RDV) : on choisit la personne dans la liste des prospects ; si elle manque, on l'ajoute (modèle du code projet 5 : « + Nouveau client »).

   -> **Le prospect lui-même** (Inscription masterclass, Demande urgente) : il ne voit jamais la liste (il verrait tous tes contacts) ; il tape son nom et son e-mail, puis une automatisation retrouve sa fiche par e-mail ou la crée, et pose le lien `Prospect`. Même résultat dans la base : tout est relié à la table unique.

## Vocabulaire et lien « Ajouter un contact »

-> Libellé visible `Contact` dans les formulaires (neutre : prospect ou client). Renommage des champs liens `Prospect` en `Contact` et de la table `SC_Prospects` en `SC_Contacts` : Val David le 02/10, exécution en S170z après inventaire des références (n8n, docs, code).

-> Lien cliquable « Ajouter un contact » sur chaque formulaire rempli par quelqu'un de la société :

   -> NocoDB : description du formulaire en markdown `[Ajouter un contact](https://sb-nocodb.coolify.salescloser.fr/#/nc/form/0476d0c5-1db4-4cd7-b62a-552adbdf818c)` (posée par API le 01/10 sur « Nouvelle mission » et « Nouveau CR de RDV ») + description du champ

   -> Airtable (formulaire d'interface) : dans l'éditeur, ajouter un élément Texte en haut du formulaire s'il est proposé, avec le lien https://airtable.com/appTqLo3JDg7d1fak/page28aUl1MwHBuqv/form ; sinon le mettre dans la description du formulaire ou le texte d'aide du champ

   -> Code : option « + Nouveau contact » directement dans la liste déroulante

## État par formulaire et par format (au 01/10/2026)

| Formulaire | Qui remplit | Airtable | NocoDB | Code |
|---|---|---|---|---|
| Ajouter un prospect (projet 1) | Équipe | `page28aUl1MwHBuqv` : ajouter `Statut` (David) | fait : `Statut` ajouté, liens et cumuls masqués (API) | `/nouveau` : table elle-même |
| Nouveau CR de RDV (projet 2) | Équipe | `pag5VRfyhBBi1lKfy` : champ `Prospect` + aide (David) | fait : `Prospect` ajouté, obligatoire, aide avec lien (API) | à faire S170z : liste + « + Nouveau prospect » |
| Inscription masterclass (projet 3) | Prospect | champs nom/e-mail + automatisation (S170z) | champs nom/e-mail + n8n (S170z) | à faire S170z : rattachement par e-mail côté serveur |
| Demande urgente (projet 4) | Prospect | automatisation à recibler sur `SC_Prospects` (David) | champs temporaires en place ; rattachement n8n (S170z) | à faire S170z : rattachement par e-mail côté serveur |
| Nouvelle mission (projet 5) | Équipe | `pagv1Q04c5KbdNJkM` : `Client` -> `Prospect` (David) | fait : `Prospect` libellé « Client », obligatoire, aide avec lien (API) | fait : liste + « + Nouveau client » |

Côté code, le rattachement par e-mail se fait dans l'API du formulaire (recherche du prospect par e-mail, création s'il n'existe pas) : aucune automatisation externe.

## I- Airtable (interface, David)

Ordre impératif : formulaires et automatisation d'abord, suppressions en dernier (sinon le formulaire et l'automatisation cassent).

### 1- Formulaire « Nouvelle mission » (`pagv1Q04c5KbdNJkM`)

1- Interfaces -> page du formulaire -> Modifier.

2- Retirer l'élément `Client`.

3- Ajouter le champ `Prospect`, libellé « Client », obligatoire.

4- Texte d'aide : « Absent de la liste ? Ajoute-le d'abord : https://airtable.com/appTqLo3JDg7d1fak/page28aUl1MwHBuqv/form puis reviens ici. »

5- Si le panneau du champ propose une option du type « Autoriser la création de nouveaux enregistrements » (Allow creating new records), l'activer : c'est l'équivalent exact du « + Nouveau client » du code. Sinon, le texte d'aide suffit.

6- Publier.

### 2- Formulaire « Ajouter un CR de RDV » (`pag5VRfyhBBi1lKfy`)

Même réglage : champ `Prospect` présent, obligatoire, même texte d'aide, même option de création si elle existe.

### 3- Formulaire « Ajouter un prospect » (`page28aUl1MwHBuqv`)

Ajouter le champ `Statut`, facultatif, aide « Gagne = client (il apparaît alors dans Clients et CA) ».

### 4- Automatisation du projet 4 (demandes urgentes) : à créer, aucune n'existe

But : SI l'e-mail saisi existe déjà dans `SC_Prospects` ALORS la demande est reliée à ce contact, SINON le contact est créé avec son prénom, son nom, son e-mail et son téléphone, puis relié.

Prérequis (fait par API le 01/10) : champ `PrenomClientTemp` créé dans `AZ_Demandes` (`fld3d0imQgZOtvuYq`). À faire : l'ajouter au formulaire « Demande urgente » (`pagBUbRHnk0WYY4yM`), libellé « Ton prénom », obligatoire. Vérifier que le champ `Prospect` n'est PAS dans ce formulaire public.

Automations -> + Créer une automatisation, nom « Demande urgente : rattacher le contact » :

1- Déclencheur « Lorsqu'un enregistrement correspond à des conditions » (When a record matches conditions) : table `AZ_Demandes`, conditions `EmailClientTemp` n'est pas vide ET `Prospect` est vide.

2- Action « Rechercher des enregistrements » (Find records) : table `SC_Prospects`, recherche par condition : `Email` est (is) -> valeur dynamique `EmailClientTemp` du déclencheur.

3- « Ajouter une logique avancée ou une action » -> « Groupe conditionnel » (Conditional group) :

   -> Si : résultat de l'étape 2, `Records` n'est pas vide -> action « Mettre à jour l'enregistrement » (Update record) : table `AZ_Demandes`, ID d'enregistrement = `Airtable record ID` du déclencheur, champ `Prospect` = liste `Airtable record ID` des enregistrements trouvés à l'étape 2.

   -> Sinon (Otherwise) -> action « Créer un enregistrement » (Create record) : table `SC_Prospects`, `Prénom` = `PrenomClientTemp`, `Nom` = `NomClientTemp`, `Email` = `EmailClientTemp`, `Téléphone` = `TelephoneClientTemp`, `Statut` = `Nouveau`, `Source` = `Site`. Puis action « Mettre à jour l'enregistrement » : table `AZ_Demandes`, ID = `Airtable record ID` du déclencheur, champ `Prospect` = `Airtable record ID` de l'enregistrement créé juste avant.

Pièges constatés au montage (02/10, capture David) :

-> « Rechercher des entrées » : la valeur de `Email` doit être le champ `EmailClientTemp` pris dans la source « Lorsqu'une entrée correspond aux conditions » (le déclencheur = ce que le contact a tapé, différent à chaque demande). Jamais la source « Tableaux et champs de cette base » (affichée « Métadonnées de la base ») : elle insère l'identifiant du champ (`ID de champ fldC79MKlH9j7gVD7`), un texte fixe ; la recherche ne trouverait jamais personne et chaque demande créerait un doublon de contact. Contrôle : au test de l'étape, la condition affiche une vraie adresse e-mail, pas `fld...`

-> « Limite maximale d'entrées » : 1 (une demande se relie à un seul contact)

-> Valeur tapée au clavier : si le résumé de l'étape affiche `Lorsque Email est "EmailClientTemp"` (entre guillemets), le mot a été tapé comme du texte ; la recherche porte alors sur le mot « EmailClientTemp » et ne trouve jamais personne. Une valeur dynamique s'insère uniquement par le « + » et apparaît comme une étiquette colorée

-> Sélecteur « Utiliser les données de... » : colonne de gauche = la source, colonne de droite = le champ. Pour tout ce que le contact a tapé (prénom, nom, e-mail, téléphone) et pour l'ID de la demande : source « Lorsqu'une entrée correspond aux conditions ». Pour le contact trouvé : source « Rechercher des entrées ». Pour le contact créé : source « Créer une entrée ». Jamais « Structure de la base » (liste les tables et leurs identifiants, pas les données). `Statut` et `Source` se choisissent directement dans leur liste, sans « + »

-> 2e branche du groupe : « + Ajouter une condition » crée un « Otherwise if » qui demande une condition. Condition à mettre : `Records` (de « Rechercher des entrées ») -> `length` -> `=` -> `0`. Elle couvre exactement le cas « aucun contact trouvé »

-> Triangle rouge sur le déclencheur : normal tant qu'aucune demande ne remplit les conditions (les 10 demandes existantes ont déjà leur contact, vérifié par API le 02/10). Il disparaît après le 1er test avec une demande soumise par le formulaire

4- Test, dans cet ordre :

   a- Soumettre le formulaire public avec un contact fictif nouveau (ex. prénom Test, nom Rattachement, e-mail `test.rattachement@exemple.fr`).

   b- Dans l'éditeur : « Tester le déclencheur » -> choisir cette demande ; puis tester « Rechercher des entrées » (résultat attendu : 0 entrée) ; puis tester les actions de la branche `length = 0`. Attention : un test d'action est réel, il crée la fiche contact et pose le lien.

   c- Vérifier : la demande a son `Prospect`, la fiche existe dans `SC_Prospects` (Statut Nouveau, Source Site).

   d- Activer l'automatisation (interrupteur en haut).

   e- Soumettre à nouveau le formulaire avec le même e-mail : en moins d'une minute, la 2e demande est reliée à la même fiche, aucune nouvelle fiche (branche `length ≠ 0`).

   f- Historique des exécutions (Run history) : 2 exécutions vertes.

   g- Nettoyage : CC vérifie par API puis supprime les demandes et la fiche de test sur le Go de David.

Si le groupe conditionnel n'est pas proposé par ton offre Airtable, le dire à CC : le rattachement passe alors par n8n (même logique que NocoDB).

Même automatisation, à l'identique, pour l'inscription masterclass (`AZ_Inscrits`) en S170z : champs sas `Prenom`, `Nom`, `Email`, `Entreprise` existants, lien `Prospects`.

### 5- Cumuls dans `SC_Prospects`

| Champ | Type | Réglage |
|---|---|---|
| `NbMissions` | Comptage (Count) | champ lié `Missions` |
| `CA facturé` | Cumul (Rollup) | `Missions` -> `Montant`, `SUM(values)`, condition : `Facturé` coché |
| `Reste à facturer` | Cumul | `Missions` -> `Montant`, `SUM(values)`, conditions : `Statut` = `Terminée` ET `Facturé` non coché |
| `En cours` | Cumul | `Missions` -> `Montant`, `SUM(values)`, condition : `Statut` = `En cours` |

Les conditions se règlent par « N'inclure que les enregistrements liés qui remplissent certaines conditions ». Format devise, euro.

### 6- Vues partagées en lecture seule (colonne `Lien_Airtable_Base`)

Dupliquer la vue de travail, masquer dans la copie `Email`, `Téléphone` et `Notes`, puis Partager la vue -> Créer un lien :

-> Projet 5 : `SC_Prospects`, vue « Clients et CA », filtre `Statut` = `Gagne`, colonnes `Missions` + les 4 cumuls

-> Projet 1 : `SC_Prospects`, vue « Prospects »

-> Projet 2 : `SC_CRs_de_RDV`, vue « Récents »

-> Projet 3 : `AZ_Inscrits`, vue « Par masterclass »

-> Projet 4 : `AZ_Demandes`, vue « Urgentes en premier » (masquer aussi `EmailClientTemp`, `TelephoneClientTemp`)

Envoyer les 5 liens à CC : il les reporte dans `AZ_Portfolio` et dans les messages Discord.

### 7- Suppressions (seulement après 1 à 4)

1- `AZ_Missions` : supprimer le champ `Client`.

2- `AZ_Demandes` : supprimer `Nom (from AZ_Clients)`, puis `AZ_Clients`. Si la vue « Par client » était regroupée sur `AZ_Clients`, la regrouper sur `Prospect`.

3- Table `AZ_Clients` : clic droit sur l'onglet -> Supprimer la table.

Vérifié par API le 01/10 : les 7 missions et les 10 demandes ont déjà leur lien `Prospect`, rien ne se perd. Retour arrière : corbeille de la base (Trash) pour restaurer un champ ou une table supprimés ; données d'origine aussi dans `defis/demandes-clients-urgentes/import-csv/clients.csv`.

## II- NocoDB

### Fait par API le 01/10/2026 (trace et retour arrière)

| Objet | Avant | Après | Retour arrière |
|---|---|---|---|
| Formulaire « Nouvelle mission », champ `Prospect` (`fvc9fffalhkajuy9a`) | libellé vide, non obligatoire | libellé « Client », obligatoire, aide avec lien « Ajouter un prospect » | `PATCH /api/v2/meta/form-columns/fvc9fffalhkajuy9a {"label":"","required":false,"description":""}` |
| Formulaire « Nouvelle mission », colonnes `SC_Prospects` (texte) et clé interne | affichées | masquées | `PATCH ... {"show":true}` |
| Formulaire « Nouveau CR de RDV », champ `Prospect` (`fvc250jqc89z6xpy0`) | masqué | affiché, obligatoire, aide avec lien | `PATCH ... {"show":false,"required":false,"description":""}` |
| Formulaire « Ajouter un prospect », `Statut` (`fvczgpoqfzcjaqama`) | masqué | affiché, facultatif | `PATCH ... {"show":false}` |
| Formulaire « Ajouter un prospect », `Missions`, `Demandes` + 5 cumuls | affichés | masqués | `PATCH ... {"show":true}` |
| Formulaires « Nouvelle mission » et « Nouveau CR de RDV », description | vide | « Le contact n'est pas dans la liste ? [Ajouter un contact](lien) puis reviens ici. » | `PATCH /api/v2/meta/forms/{vw8ry33iwqmjl3fx,vwpo6k6790xpwxhy} {"subheading":""}` |
| `AZ_Demandes`, colonne `PrenomClientTemp` (`covw8xmy3qo3v90`) + formulaire « Soumettre une demande », libellé « Ton prénom », obligatoire | absente | créée, affichée | `DELETE /api/v2/meta/columns/covw8xmy3qo3v90` |
| Airtable `AZ_Demandes`, champ `PrenomClientTemp` (`fld3d0imQgZOtvuYq`) | absent | créé | suppression du champ dans l'interface |
| Colonnes texte vides `SC_Prospects` dans `AZ_Missions` (`cgz7t8foe6b0bo8`) et `AZ_Demandes` (`czy42qkotvf2bpc`), restes d'un lien recréé le 30/09 | vides sur toutes les lignes | supprimées | recréer une colonne texte `SC_Prospects` (aucune donnée à restaurer) |

Le formulaire « Nouveau CR de RDV » garde son champ texte `Client` : l'automatisation de mise en forme du CR peut le lire. Il sera retiré en S170z après vérification.

### En attente du Go de David

-> Suppression de la table `AZ_Clients` (`my2gc6wwrlaqlh2`) : retire aussi les liens `Client` de `AZ_Missions` et `AZ_Demandes`, le lien `AZ_Clients` de `SC_Prospects` et le partage `ebd6edc5`. Vérifié par API : 7 missions sur 7 et 10 demandes sur 10 ont leur lien `Prospect`.

-> Désactivation du partage de la base entière `22ca37d4` (plus utilisé par le portfolio depuis le 30/09).

-> Retour arrière : pas de corbeille NocoDB pour une table supprimée ; sauvegarde JSON des 5 enregistrements prise par CC juste avant la suppression, versionnée ici.

## III- Code (session S170z)

-> CR de RDV (projet 2) : sélecteur de prospect + « + Nouveau prospect », même composant que le projet 5.

-> Masterclass (projet 3) et Demandes (projet 4) : l'API du formulaire cherche le prospect par e-mail et le crée s'il n'existe pas.

-> Chaque projet garde son schéma Neon (`crm`, `masterclass`, `demandes`, `missions`, `public`) : la table de prospects commune côté code est un chantier de feuille de route (schéma partagé `crm` lu par les autres projets).

## Changelog

-> 1.2.2 -- 2026-10-02 (S163z-ccweb) : pièges « valeur tapée entre guillemets » et « source du sélecteur » (déclencheur, Rechercher, Créer ; jamais Structure de la base).

-> 1.2.1 -- 2026-10-02 (S163z-ccweb) : piège « source de la valeur » détaillé (déclencheur vs « Tableaux et champs de cette base » qui insère l'ID du champ), limite de recherche à 1.

-> 1.2.0 -- 2026-10-02 (S163z-ccweb) : automatisation du projet 4, pièges constatés au montage (valeur de recherche, 2e branche `length = 0`, triangle rouge du déclencheur) et procédure de test en 7 temps ; renommage `Contact` validé par David pour S170z.

-> 1.1.0 -- 2026-10-01 (S163z-ccweb) : vocabulaire `Contact` (renommage en attente de Val), lien « Ajouter un contact » par format, automatisation du projet 4 à créer pas à pas (aucune n'existait), champ `PrenomClientTemp` créé (Airtable + NocoDB), description des 2 formulaires NocoDB internes.

-> 1.0.0 -- 2026-10-01 (S163z-ccweb) : création. Règle « qui remplit le formulaire », état des 5 formulaires dans les 3 formats, guide Airtable en 7 étapes, trace des écritures NocoDB du 01/10, plan code S170z.
