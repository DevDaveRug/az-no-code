# Missions et CA par client -- audit + restructuration no-code

Projet portfolio : diagnostic d'une base Airtable de suivi de missions « qui part en vrille » (chiffre d'affaires par client impossible à sortir, filtres faux, doublons) et structure cible en 2 tables liées. Défi Alegria n°5, semaine 40 (énoncé publié le 28/09/2026, ouvert jusqu'au 02/10/2026 17h00).

## Énoncé (résumé interne)

Brief de la cliente : « Ma base marche mais tout le monde se plaint. On n'arrive pas à sortir le chiffre d'affaires par client, les filtres ne donnent jamais le bon résultat et la moitié des trucs sont en double. Est-ce qu'il faut tout refaire ? »

Base d'origine : 1 table, 7 colonnes (`Mission`, `Client`, `Email client`, `Statut`, `Montant`, `Date de fin`, `Facturé`), toutes en `Texte sur une ligne`, 5 lignes.

Livrable attendu : texte (pas de PDF), diagnostic en 5 points maximum + structure cible (tables, champs, types). Barème : diagnostic des types de champs (6), besoin d'une table Clients dédiée (6), structure cible cohérente (6), formulation compréhensible par une non-technicienne (2).

Indice : une seule des 7 colonnes a le bon type (`Mission`), et le CA par client impossible n'est pas un problème de formule (c'est l'absence de table Clients).

## Ce que contient ce dossier

- `DIAGNOSTIC.md` : le diagnostic en 5 points + la structure cible + le plan de migration, rédigés pour une non-technicienne. Sert aussi de grille d'audit réutilisable pour toute base no-code.

- `airtable/` : structure cible dans la base `Sales Closer Souverain` (réutilise `AZ_Clients` du projet 4 + nouvelle table `AZ_Missions`), `schema.json` Metadata API, exemples, spec Interface.

- `nocodb/` : miroir souverain dans la base unifiée NocoDB (`phwalskbrftv4o4`), spec Interface adaptée au plan Free, `API.md` avec les commandes curl prêtes (identifiants réels des tables et colonnes).

- `import-csv/` : `missions-avant.csv` (la base « en vrille », anonymisée), `missions.csv` (la même après nettoyage), `missions-ca-par-client-portfolio-row.csv` (ligne AZ_Portfolio).

Stack code souverain équivalent : `github.com/DevDaveRug/az-code/tree/main/defis/missions-ca-par-client` (Next.js + Prisma + Neon). Le script de seed y rejoue la migration « avant -> après » avec les mêmes règles de nettoyage que `DIAGNOSTIC.md`.

## Choix de conception

- **Pas de 2e table Clients** : la base unifiée a déjà `AZ_Clients` (projet 4, demandes urgentes). `AZ_Missions` s'y relie. Créer une 2e table clients reproduirait exactement le défaut diagnostiqué (le même client à 2 endroits). Un client voit ainsi ses demandes et ses missions au même endroit.

- **Pas d'automatisation** : la valeur de ce projet est dans la structure (types de champs, table liée, cumuls). Le seul garde-fou ajouté est une vue « À vérifier » sur le champ `NotesMigration`.

- **CA facturé, reste à facturer, en cours** : 3 cumuls distincts plutôt qu'un « CA » ambigu. Le chiffre d'affaires facturé est celui qui intéresse la comptabilité, le reste à facturer est celui qui intéresse la trésorerie.

## Livraison

Jeudi 01/10/2026 (convention DateLivraison = jeudi de la semaine ISO 40). Réponse Discord à poster avant le 02/10/2026 17h00.
