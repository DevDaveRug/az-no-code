# Diagnostic -- une base de suivi de missions qui « part en vrille »

Contexte : une base Airtable d'une seule table sert à suivre les missions d'une activité de services. Elle « marche », mais personne n'arrive à sortir le chiffre d'affaires par client, les filtres donnent des résultats faux et la moitié des lignes semblent en double. Question posée : faut-il tout refaire ?

Réponse courte : **non**. Les données se gardent. Il faut changer la façon dont la base est rangée : 4 types de champs à corriger et une table Clients à sortir.

Données d'exemple ci-dessous : version anonymisée de la base d'origine (voir `import-csv/missions-avant.csv`).

---

## I- Diagnostic en 5 points

### 1- Les 7 colonnes sont toutes en « Texte sur une ligne ». Seule « Mission » a le bon type.

Pour Airtable, « 2500 € », « 80k€ » ou « juin » sont des mots, pas des montants ni des dates. Il sait les afficher, mais pas les additionner, les trier ni les filtrer correctement.

| Colonne | Type actuel | Type cible | Pourquoi |
|---|---|---|---|
| Mission | Texte sur une ligne | Texte sur une ligne | Déjà bon |
| Client | Texte sur une ligne | Lien vers un autre enregistrement (table Clients) | Voir point 2 |
| Email client | Texte sur une ligne | E-mail, déplacé dans la table Clients | Une info du client, pas de la mission |
| Statut | Texte sur une ligne | Sélection unique | Voir point 4 |
| Montant | Texte sur une ligne | Devise (€) | Voir point 3 |
| Date de fin | Texte sur une ligne | Date | Voir point 5 |
| Facturé | Texte sur une ligne | Case à cocher | Voir point 4 |

### 2- Le client est retapé à la main à chaque mission : c'est LA raison pour laquelle le CA par client est impossible.

Ce n'est pas un problème de formule. « Alice martin », « Alice Martin » : pour Airtable, ce n'est qu'un texte tapé dans une case. Une majuscule, un espace en trop ou une faute, et il ne sait plus que c'est la même cliente. Son e-mail est recopié 3 fois. Tant que les clients n'ont pas leur propre table, rien ne permet de regrouper leurs missions. C'est aussi l'origine des « doublons » : ce ne sont pas des lignes en double, c'est le même client écrit de plusieurs façons.

La colonne mélange en plus des personnes (Alice Martin) et des entreprises (TechFlow SAS, dont le contact est Bob Durand ; Studio Zenith, dont le contact est Chloé).

### 3- Montant : 5 façons d'écrire un prix.

« 2500 € », « 80k€ », « 500 EUR », « 1200 € », « 450 », « 3 200,00 € ». Même avec la bonne formule, le total est faux tant que la colonne est en texte.

### 4- Statut et Facturé : des mots libres là où il faudrait une liste.

-> Statut : « terminé », « fini », « Terminé », « arrêté », « en cours », « EN COURS », « à faire » pour 4 situations réelles

-> Facturé : « oui », « OUI », « x », « non » ou vide pour une simple question oui/non

Un filtre « terminé » ne voit pas la mission « fini » : c'est pour ça que les filtres ne donnent jamais le bon résultat.

### 5- Date de fin : « 12/03/2026 », « mai 2026 », « juin », « Juillet ».

Sans jour ni année, Airtable ne peut pas répondre à « qu'est-ce qui se termine ce mois-ci ? » ni trier les missions par échéance.

---

## II- Structure cible : 2 tables reliées au lieu d'une

### Table Clients (chaque client n'existe qu'une fois)

| Champ | Type | Exemple / rôle |
|---|---|---|
| Nom du client | Texte sur une ligne (champ principal) | Alice Martin, TechFlow SAS, Studio Zenith |
| Contact | Texte sur une ligne | Bob Durand chez TechFlow SAS |
| E-mail | E-mail | une seule fois par client |
| Téléphone | Numéro de téléphone | optionnel |
| Missions | Lien vers la table Missions | se remplit tout seul |
| Nombre de missions | Count (compte les missions liées) | calculé |
| CA facturé | Cumul (Rollup) : somme de Montant, missions cochées « Facturé » | calculé |
| Reste à facturer | Cumul : somme de Montant, missions « Terminée » non cochées « Facturé » | calculé |
| En cours | Cumul : somme de Montant, missions « En cours » | calculé |

### Table Missions (une ligne par mission)

| Champ | Type | Règle |
|---|---|---|
| Mission | Texte sur une ligne (champ principal) | déjà bon |
| Client | Lien vers la table Clients (1 seul client) | on choisit dans une liste, on ne retape plus |
| Statut | Sélection unique | 4 choix : À faire, En cours, Terminée, Arrêtée |
| Montant | Devise (€, 2 décimales) | on tape 80000, jamais « 80k€ » |
| Date de fin | Date | format jour/mois/année |
| Facturé | Case à cocher | coché = facturé |
| E-mail du client (optionnel) | Recherche (Lookup) depuis Clients | pour le voir sans le retaper |

Résultat : le chiffre d'affaires de chaque client s'affiche tout seul dans sa fiche, les filtres deviennent fiables parce qu'on choisit au lieu de taper, et une vue Calendrier sur « Date de fin » devient possible.

---

## III- Plan de migration (environ 30 min, sans rien perdre)

1- **Dupliquer la table** avant de commencer (clic droit sur l'onglet > Dupliquer la table) : filet de sécurité.

2- **Harmoniser les valeurs à la main** : un seul « Alice Martin », des montants en chiffres (80000, pas 80k€), une date complète pour « mai 2026 », « juin », « Juillet » (demander le vrai jour ; à défaut, dernier jour du mois et une note « à confirmer »).

3- **Transformer la colonne Client** en « Lien vers un autre enregistrement » vers une nouvelle table Clients. Airtable crée une fiche par nom différent : c'est pour ça que l'étape 2 passe avant.

4- **Changer le type des autres colonnes une par une** : Statut en Sélection unique, Montant en Devise, Date de fin en Date, Facturé en Case à cocher. Vérifier après chaque conversion qu'aucune cellule ne s'est vidée.

5- **Ranger les e-mails dans la table Clients**, puis supprimer « Email client » de la table Missions.

6- **Ajouter les cumuls** dans la table Clients (CA facturé, Reste à facturer, En cours).

7- **Vérifier les vrais doublons** : « Maintenance page » et « Maintenance » chez la même cliente, même mission saisie 2 fois ou deux missions distinctes ?

---

## IV- Grille d'audit réutilisable (toute base no-code)

-> Chaque colonne a-t-elle le type qui correspond à ce qu'on veut en faire (additionner = nombre ou devise, trier par échéance = date, filtrer = sélection ou case à cocher) ?

-> Une même information (client, fournisseur, produit) est-elle retapée sur plusieurs lignes ? Si oui, elle mérite sa propre table.

-> Une colonne contient-elle des valeurs qui décrivent autre chose que la ligne (l'e-mail du client dans une table de missions) ?

-> Les statuts sont-ils une liste fermée, ou chacun écrit-il le sien ?

-> Les dates sont-elles toutes complètes (jour, mois, année) ?

-> Les chiffres que l'on veut « sortir » (CA, marge, nombre) existent-ils comme champs calculés, ou faut-il les recalculer à la main à chaque fois ?

-> Existe-t-il une vue « à vérifier » qui attrape les lignes incomplètes avant qu'elles faussent les totaux ?
