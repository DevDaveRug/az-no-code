# Consignes pour Claude Code (az-no-code)

## Git et PR (Cor David, 05/10/2026)

-> Toujours committer et pousser son travail sur la branche de la session, sans attendre qu'on le demande.

-> Une PR ouverte et non mergée sur cette branche : y ajouter les commits suivants, ne pas en ouvrir une autre.

-> Aucune PR ouverte (ou la précédente déjà mergée) : repartir de `main` sur la même branche et ouvrir la PR soi-même.

-> Jamais de push sur `main`, jamais de push forcé.

-> **Le merge appartient à David. CC ne merge jamais de sa propre initiative** -- la création de la PR est automatique, le merge ne l'est pas et ne le deviendra pas.

   -> **Seule exception (Val David S170z-ccweb)** : CC peut merger quand David le lui demande **nommément pour CETTE PR précise** (ex. « merge la 32 »). Une instruction donnée une fois ne vaut pas blanc-seing pour les PR suivantes : CC redemande ou attend à chaque fois.

   -> Même règle que `dr-context/CLAUDE.md` VI.1, à laquelle ce fichier s'aligne. La formulation « ne jamais merger » d'avant S170z était plus stricte que VI.1 et créait une contradiction entre les deux repos.

## Changelog

-> 1.1.0 -- 2026-10-06 (S170z-ccweb, Val David) : la règle de merge s'aligne sur `dr-context/CLAUDE.md` VI.1. CC ne merge toujours jamais de lui-même, mais peut merger une PR que David désigne nommément. Lève la contradiction constatée en S170z : `dr-context` posait l'exception, ce fichier disait « ne jamais merger ».

-> 1.0.0 -- 2026-10-05 (Cor David) : création. Push systématique sur la branche de session, réutilisation de la PR ouverte, merge réservé à David.
