# Messages Discord -- projet 5 « Missions et CA par client »

Version : 1.0.0
Date : 2026-10-01
Session : S163z-ccweb
Compagnon : `ATTENDUS.md` (grille des attendus), `/defi-hebdo-alegria` v1.8.1 (Étape 10, archive des messages)

Archive des messages tels que postés (ou à poster). Règle des liens (v1.8.1) : minimum 6 liens, minimum 2 par forme (au moins un formulaire et au moins une table, pour Airtable, NocoDB et code). Les messages déjà postés se corrigent en éditant le message dans Discord, pas en le repostant.

## Message 1 : fil du projet (livrable noté)

Statut : posté par David dans le fil le 01/10/2026 (2 messages, 1829 et 1767 caractères), avant la limite du 02/10 17h00. Pas de lien : diagnostic en texte.

### 1/2

Bonjour Léa,

Bonne nouvelle : pas besoin de tout refaire. Tes missions se gardent, il faut surtout changer la façon dont la base est rangée. Voici le diagnostic en 5 points.

**1- Tes 7 colonnes sont toutes en "Texte sur une ligne". Seule "Mission" a le bon type.**

Pour Airtable, "2500 €", "80k€" ou "juin" sont des mots, pas des montants ni des dates. Il peut les afficher, mais pas les additionner, les trier ni les filtrer correctement.

**2- Le client est retapé à la main à chaque mission. C'est LA raison pour laquelle le chiffre d'affaires par client est impossible à sortir.**

Ce n'est pas un problème de formule. "Marie dupont", "Marie Dupont" : un nom tapé à la main ne dit pas à Airtable que c'est la même cliente, et il suffit d'un espace en trop ou d'une faute pour qu'elle apparaisse deux fois. Son e-mail est recopié 3 fois. Tant que les clients n'ont pas leur propre table, rien ne permet de regrouper leurs missions. C'est aussi de là que viennent tes doublons. Et la colonne mélange des personnes (Marie Dupont) et des entreprises (Orange, dont le contact est J. Lefebvre).

**3- Montant : 5 façons d'écrire un prix.**

"2500 €", "80k€", "500 EUR", "1200 €", "450". Même avec la bonne formule, le total est faux tant que la colonne est en texte.

**4- Statut et Facturé : des mots libres là où il faudrait une liste.**

"terminé", "fini", "arrêté", "en cours", "EN COURS" pour 3 situations réelles. "oui", "OUI", "x", "non" ou vide pour une simple question oui/non. Un filtre "terminé" ne voit pas la mission "fini" : voilà pourquoi tes filtres ne donnent jamais le bon résultat.

**5- Date de fin : "12/03/2026", "mai 2026", "juin", "Juillet".**

Sans jour ni année, Airtable ne peut pas répondre à "qu'est-ce qui se termine ce mois-ci ?".

La structure que je te recommande arrive dans le message suivant.

### 2/2

**La structure cible : 2 tables reliées au lieu d'une.**

**Table Clients** (chaque client n'existe qu'une fois)

-> Nom du client : Texte sur une ligne (Marie Dupont, Orange, Les Esgourdes)

-> Contact : Texte sur une ligne (J. Lefebvre chez Orange, Maud chez Les Esgourdes)

-> E-mail : E-mail

-> Téléphone : Numéro de téléphone

-> Missions : Lien vers la table Missions (se remplit tout seul)

-> CA facturé : Cumul (Rollup), somme des montants des missions cochées "Facturé"

-> Reste à facturer : Cumul, somme des missions terminées pas encore facturées

**Table Missions** (une ligne par mission)

-> Mission : Texte sur une ligne (le seul champ déjà bon)

-> Client : Lien vers la table Clients (on choisit dans une liste, on ne retape plus)

-> Statut : Sélection unique, 4 choix : À faire, En cours, Terminée, Arrêtée

-> Montant : Devise en € (on tape 80000, jamais "80k€")

-> Date de fin : Date

-> Facturé : Case à cocher

**Pour passer à la nouvelle structure sans rien perdre (environ 30 min) :**

1- Harmoniser les valeurs : un seul "Marie Dupont", des montants en chiffres, une date complète pour "mai 2026", "juin" et "Juillet"

2- Transformer la colonne Client en "Lien vers un autre enregistrement" vers une nouvelle table Clients : Airtable crée une fiche par nom, d'où l'étape 1

3- Changer le type des autres colonnes une par une

4- Ranger les e-mails dans la table Clients, puis supprimer "Email client" de Missions

5- Vérifier "Maintenance page" et "Maintenance" chez Marie Dupont : doublon ou deux missions ?

**Tes 3 soucis, réglés :**

-> CA par client : il s'affiche tout seul dans chaque fiche client

-> Filtres faux : on choisit dans une liste au lieu de taper, le filtre voit tout

-> Doublons : chaque client n'existe qu'une fois

## Message 2 : salon Défis

Statut : version corrigée du 01/10 (6 liens, 2 par forme). Si l'ancienne version à 3 liens est déjà postée, l'éditer dans Discord. Le crochet Airtable se remplace par le lien de la vue partagée « Clients et CA » (à créer par David, voir `interfaces/SC_PROSPECTS_TABLE_UNIQUE.md` étape 6).

Livré cette semaine : une base de suivi de missions qui partait en vrille (7 colonnes toutes en texte, CA par client impossible à sortir, doublons) diagnostiquée puis reconstruite sans rien ressaisir.

La même structure cible dans les 3 : une seule table de contacts (un client, c'est un prospect gagné), une table Missions reliée, chaque champ dans son vrai type (montant en devise, statut en liste, date en date, facturé en case à cocher), et le CA par client calculé tout seul : facturé, reste à facturer, en cours.

3 façons de le construire, 3 niveaux d'engagement :

1- Airtable natif -> 15 min à assembler. Les cumuls conditionnels font le CA par client sans formule. Joli d'entrée, mais tu es locataire du produit et tu payes par utilisateur.
   Formulaire (le client se choisit dans une liste, impossible de recréer un doublon) : https://airtable.com/appTqLo3JDg7d1fak/pagv1Q04c5KbdNJkM/form
   Table Clients et CA : [LIEN VUE AIRTABLE CLIENTS ET CA]

2- NocoDB sur ton VPS -> 30 min si l'infra tourne déjà. Quota illimité, tu es propriétaire. Contrepartie : pas de cumul conditionnel en version gratuite, donc 3 petites formules d'aide avant la somme.
   Formulaire : https://sb-nocodb.coolify.salescloser.fr/#/nc/form/83e067cf-0f2d-44e5-a645-04aa6906203c
   Table Clients et CA : https://sb-nocodb.coolify.salescloser.fr/#/nc/view/bc824ee4-dadd-42b3-9377-e4c7e7479914

3- Code souverain Next.js -> 1 jour, à ta marque, sur ton domaine, aucune limite. La page avant / après rejoue la migration depuis la base d'origine, chaque décision de conversion reste visible.
   Formulaire : https://missions-ca-par-client.demo.salescloser.fr/missions/nouvelle
   Table CA par client : https://missions-ca-par-client.demo.salescloser.fr

Choisis selon où tu en es. La réponse à "faut-il tout refaire ?" est la même partout : non, on convertit ce qui existe.

## Message 3 : salon partage-tes-victoires

Statut : version corrigée du 01/10 (6 liens, 2 par forme, ZDR = Zero Data Retention). À poster une fois le crochet Airtable remplacé.

Petite victoire : le projet "Missions et CA par client" est en ligne sous 3 formes, dont 2 qui m'appartiennent entièrement (NocoDB sur mon serveur + code Next.js).

Ce que je retiens de la semaine :

-> Une base qui part en vrille ne se refait pas, elle se convertit : aucune ligne ressaisie, et chaque doute est noté au lieu d'être caché

-> Une seule table de contacts : un client, c'est un prospect gagné. Missions, demandes et inscriptions s'y relient au lieu de recopier les noms

-> J'ai rangé mes 5 projets dans une seule base, un schéma chacun : un déploiement ne peut plus toucher les tables du voisin. Moins de bases à surveiller, plus de calme

-> ZDR (Zero Data Retention) : pour ne pas laisser tes données de clients faire du tourisme dans des serveurs qui ne t'appartiennent pas. Et avoir une version de ton outil que personne ne peut couper est un vrai levier de calme.

6 liens pour tester, un formulaire (ajouter une mission) et une table (le CA par client) pour chaque version :

Airtable
-> Formulaire : https://airtable.com/appTqLo3JDg7d1fak/pagv1Q04c5KbdNJkM/form
-> Table Clients et CA : [LIEN VUE AIRTABLE CLIENTS ET CA]

NocoDB souverain
-> Formulaire : https://sb-nocodb.coolify.salescloser.fr/#/nc/form/83e067cf-0f2d-44e5-a645-04aa6906203c
-> Table Clients et CA : https://sb-nocodb.coolify.salescloser.fr/#/nc/view/bc824ee4-dadd-42b3-9377-e4c7e7479914

Code Next.js
-> Formulaire : https://missions-ca-par-client.demo.salescloser.fr/missions/nouvelle
-> Table CA par client : https://missions-ca-par-client.demo.salescloser.fr

## Changelog

-> 1.0.0 -- 2026-10-01 (S163z-ccweb) : création. Message 1 tel que posté ; messages 2 et 3 corrigés (minimum 6 liens, minimum 2 par forme : formulaire + table).
