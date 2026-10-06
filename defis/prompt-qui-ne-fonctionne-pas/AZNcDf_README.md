# AZNcDf_README -- projet « le prompt qui ne fonctionne pas »

Version : 1.0.0
Date : 2026-10-06
Session : S170z-ccweb

Décrit ce dossier, et lui seul. Nom au format `[Prefixe]_README.md` sans date, conformément à `dr-context/CLAUDE.md` IV.

> Préfixe `AZNcDf` **validé par David (S170z-ccweb)** et enregistré dans `dr-context/docs/DR/DR_Professionnel/DR_Codes_Archivage.md` v0.43, section NIVEAU 1-quater : `AZ` (De A à Zen, NIVEAU 1) + `Nc` (az-no-code) + `Df` (defis).

## Objet du projet

Réparer un prompt de rédaction LinkedIn récupéré en ligne qui ne produit pas ce qu'on lui demande : des posts tous identiques, des phrases creuses, des emojis malgré l'interdiction écrite dans le prompt.

Projet de type texte : aucune base Airtable, aucune base NocoDB, aucun code, aucun déploiement. Le livrable est un diagnostic, un prompt corrigé et la preuve mesurée de la différence.

## Contenu du dossier

-> `ATTENDUS.md` : grille des 13 attendus de l'énoncé, avec pour chacun sa citation exacte, son emplacement dans la réponse et son statut. Porte aussi le protocole de preuve des 3 exécutions, les écarts assumés et l'auto-check de l'Étape 11 du skill.

-> `MESSAGES-DISCORD.md` : archive des messages tels qu'ils sont à poster. Messages 1/2 et 2/2 du fil (1966 et 1978 caractères), message 3 pour le salon Défis (1691 caractères, méthode sans lien), et en annexe les 3 sorties brutes des exécutions.

-> `DIAGNOSTIC.md` : version publique et anonymisée du diagnostic, exigée par l'Étape 10 bis du skill. Grille d'audit de prompt en 7 points, gabarit de prompt en 4 blocs, protocole de mesure. Réutilisable pour auditer n'importe quel prompt de rédaction.

## Ce qui fait la valeur de ce projet

-> Les deux prompts ont été réellement exécutés en contexte neuf, sans historique, et les sorties mesurées. Le prompt d'origine seul ne produit aucun post, il renvoie 5 questions ; précédé de la seule ligne de présentation du métier, il produit 1884 caractères avec 3 flèches, 5 hashtags, 4 questions et aucun fait concret. Le prompt corrigé produit 964 caractères sans aucun caractère décoratif.

-> La cause des caractères interdits qui reviennent est expliquée, pas seulement constatée : le reste du prompt commande le style dont ces caractères font partie, l'interdiction est négative et isolée, et l'historique de la conversation pèse plus lourd qu'une ligne.

-> Le prompt corrigé repose sur des crochets que la cliente remplit à chaque post. Le crochet du cas vécu reste le sien : un modèle ne peut pas inventer ce qu'elle a vécu sans lui faire signer un faux témoignage.

## Nommage des autres fichiers

`ATTENDUS.md`, `MESSAGES-DISCORD.md` et `DIAGNOSTIC.md` ne sont pas conformes au format `AAMMJJ_[Prefixe]_[Description].[ext]`. Ils sont laissés en l'état pour rester alignés sur les 5 autres projets du repo : leur renommage, et celui de leurs équivalents dans les 4 repos, se fait en un seul mouvement par la session **S179a**, après l'inventaire soumis à David. Les codes nécessaires sont désormais enregistrés (`DR_Codes_Archivage` v0.43, `CLAUDE.md` v1.16.0 portée 4 repos).

## Changelog

-> 1.1.0 -- 2026-10-06 (S170z-ccweb) : préfixe `AZNcDf` validé par David et enregistré au registre v0.43. Renvoi vers la session S179a pour le renommage groupé des 3 autres fichiers.

-> 1.0.0 -- 2026-10-06 (S170z-ccweb) : création, après lecture de `dr-context/CLAUDE.md` IV (forme `[Prefixe]_README.md`, sans date, un seul par dossier).
