# AZNcDf_README -- projet « scénario Make qui envoie en double et perd des prospects »

Version : 1.0.0
Date : 2026-10-13
Session : S261013z-ccdd

Décrit ce dossier, et lui seul. Préfixe `AZNcDf` validé par David (S170z-ccweb, `DR_Codes_Archivage` v0.43).

## Objet du projet

Dépanner un scénario Make (Airtable -> Iterator -> Filtre -> Gmail -> Airtable) qui notifie les coachs plusieurs fois pour le même prospect, envoie un mail par centre d'intérêt et ignore les prospects venus du formulaire du site.

Projet de type texte (diagnostic) : aucune base, aucun code, aucun déploiement. Le livrable est l'explication des 3 causes et la correction module par module.

## Contenu du dossier

-> `ATTENDUS.md` : grille des attendus de l'énoncé, écarts assumés, auto-check.

-> `MESSAGES-DISCORD.md` : archive des 2 messages de la réponse (1548 et 1736 caractères).

-> `DIAGNOSTIC.md` : version publique et anonymisée, grille d'audit réutilisable pour tout scénario « surveiller une table -> notifier -> marquer comme traité ».

## Ce qui fait la valeur de ce projet

-> La boucle est expliquée jusqu'au bout : le scénario se relance lui-même parce que son dernier module modifie la fiche que son premier module surveille.

-> Les prospects « perdus » ne le sont pas : ils sont restés dans la table, et la correction dit comment les rattraper sans les envoyer en rafale.

-> Un piège Make que l'énoncé ne dit pas : Airtable n'envoie ni une case décochée ni un champ vide, donc le filtre doit tester « n'existe pas », pas « égal à vide ».

## Changelog

-> 1.0.0 -- 2026-10-13 (S261013z-ccdd) : création.
