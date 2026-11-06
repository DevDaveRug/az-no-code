# Consignes pour Claude Code (az-no-code)

## Démarrage de session (Cor David, 06/11/2026, BLOQUANTE)

-> Ces règles valent même si `dr-context` n'est pas attaché à la session : c'est le cas qui les a fait sauter en S261105c-ccweb.

-> Au premier tour, avant tout autre travail : dériver l'identifiant `S<AAMMJJ><lettre>[<n>]-<support>`, renommer la session avec `set_session_title` au format `<support>-<lettre>[<n>]-s<AAMMJJ>-<dépôts>-<sujet>` (dépôts : `nc` az-no-code, `co` az-code, `lm` liam-, `dr` dr-context, reliés par `+`).

-> Si la plateforme impose une branche hors format (`claude/<mots-au-hasard>`) : travailler sur une branche `claude/<support>-<lettre>[<n>]-s<AAMMJJ>-nc-<slug>`, avant le premier push.

-> Titre de PR : `<support>-<lettre>[<n>]-s<AAMMJJ>-nc : <sujet>`.

-> Toute action que David doit faire hors du chat passe par `/pas-a-pas` (skill de `dr-context`) : une question par action, la commande ou le lien entier dans la question.

## Git et PR (Cor David, 05/10/2026)

-> Toujours committer et pousser son travail sur la branche de la session, sans attendre qu'on le demande.

-> Une PR ouverte et non mergée sur cette branche : y ajouter les commits suivants, ne pas en ouvrir une autre.

-> Aucune PR ouverte (ou la précédente déjà mergée) : repartir de `main` sur la même branche et ouvrir la PR soi-même.

-> Jamais de push sur `main`, jamais de push forcé.

-> **Le merge appartient à David. CC ne merge jamais de sa propre initiative** -- la création de la PR est automatique, le merge ne l'est pas et ne le deviendra pas.

   -> **Seule exception (Val David S170z-ccweb)** : CC peut merger quand David le lui demande **nommément pour CETTE PR précise** (ex. « merge la 32 »). Une instruction donnée une fois ne vaut pas blanc-seing pour les PR suivantes : CC redemande ou attend à chaque fois.

   -> Même règle que `dr-context/CLAUDE.md` VI.1, à laquelle ce fichier s'aligne. La formulation « ne jamais merger » d'avant S170z était plus stricte que VI.1 et créait une contradiction entre les deux repos.

## Changelog

-> 1.2.0 -- 2026-11-06 (S261105c-ccweb, Cor David) : section « Démarrage de session ». Titre de session et branche au format dès le premier tour, même sans `dr-context` ; `/pas-a-pas` pour les actions de David. Cause : session partie avec az-no-code seul, branche `intelligent-pascal-gt3o05` et titre générique jamais corrigés, David a dû le demander.

-> 1.1.0 -- 2026-10-06 (S170z-ccweb, Val David) : la règle de merge s'aligne sur `dr-context/CLAUDE.md` VI.1. CC ne merge toujours jamais de lui-même, mais peut merger une PR que David désigne nommément. Lève la contradiction constatée en S170z : `dr-context` posait l'exception, ce fichier disait « ne jamais merger ».

-> 1.0.0 -- 2026-10-05 (Cor David) : création. Push systématique sur la branche de session, réutilisation de la PR ouverte, merge réservé à David.
