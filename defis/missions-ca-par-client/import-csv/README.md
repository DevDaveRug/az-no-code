# Import CSV -- données de démonstration + ligne portfolio

3 fichiers :

-> `missions-avant.csv` : la base « en vrille » anonymisée (1 table, 7 colonnes texte). Sert aux captures « avant » et à rejouer le diagnostic

-> `missions.csv` : les mêmes missions après nettoyage (types corrects, client rapproché, notes de migration)

-> `missions-ca-par-client-portfolio-row.csv` : la ligne `AZ_Portfolio` du projet 5 (18 colonnes, ordre du skill `/defi-hebdo-alegria` v1.7.1+, `Captures` vide)

Voie la plus rapide quand un jeton est disponible : `../airtable/API.md` et `../nocodb/API.md` (création de la table, des liens et des 7 missions en une passe). La voie CSV ci-dessous reste utile à la main.

## Airtable

1- Base `Sales Closer Souverain` -> `+ Add or import` -> `CSV file` -> `missions.csv` -> nouvelle table, la renommer `AZ_Missions`.

2- Vérifier les types détectés et corriger si besoin :

   -> `Statut` -> `Sélection unique` (4 options : À faire, En cours, Terminée, Arrêtée ; ajouter les couleurs de `../airtable/SPECS.md`)

   -> `Montant` -> `Devise`, `€`, 2 décimales

   -> `DateFin` -> `Date`, format `European`

   -> `Facturé` -> `Case à cocher` (les cellules `checked` deviennent cochées ; contrôler les 3 lignes attendues : Refonte site, Création d'une app mobile, Maintenance page)

   -> `NotesMigration` -> `Texte long`

3- Convertir `Client` en `Lien vers un autre enregistrement` -> `AZ_Clients`, un seul enregistrement par cellule. Airtable rapproche chaque nom (`Alice Martin`, `Bob Durand`, `Chloe Dubois`, `Emma Petit`, `Fabien Roux`) de l'enregistrement `AZ_Clients` qui porte exactement ce nom. C'est l'étape 3 du plan de migration du diagnostic : les noms doivent être harmonisés avant, sinon Airtable crée une fiche en double.

4- Contrôler dans `AZ_Clients` qu'aucune nouvelle fiche n'a été créée (toujours 5 clients), puis ajouter les champs calculés de `../airtable/SPECS.md`.

5- Ligne portfolio : ouvrir `missions-ca-par-client-portfolio-row.csv` dans un tableur, copier la ligne 2, coller dans une nouvelle ligne de `AZ_Portfolio` à partir de la colonne `Slug` (la colonne `Nom du projet` est une formule, ne rien y coller). Vérifier ensuite qu'aucune option parasite n'a été créée dans `Client`, `Statut`, `AnnéeSaison`.

## NocoDB

1- Base `Sales Closer Souverain` -> `+` -> `Import` -> `CSV` -> `missions.csv` -> table `AZ_Missions`.

2- Corriger les types comme pour Airtable (`SingleSelect`, `Currency` EUR, `Date`, `Checkbox`, `LongText`). Si `Facturé` ne se coche pas à la conversion, cocher à la main les 3 lignes attendues.

3- Le lien vers `AZ_Clients` ne s'importe pas par CSV : créer le champ `Links` depuis `AZ_Clients` (`Missions`, has many), puis relier les 7 missions (5 clics) ou utiliser `../nocodb/API.md` étape 5. Supprimer ensuite la colonne texte `Client` importée.

4- Ajouter les 3 formules d'aide et les 3 cumuls (`../nocodb/SPECS.md`).

## Rejouer le diagnostic sur la version « avant »

Importer `missions-avant.csv` dans une base de test (jamais dans `Sales Closer Souverain`, pour ne pas polluer la base unifiée) : filtrer `Statut` contient `terminé` (la mission « fini » disparaît), grouper par `Email client` (3 lignes pour la même cliente, e-mail recopié à la main), tenter un total sur `Montant` (impossible en texte). Ce sont les captures « avant » du portfolio.
