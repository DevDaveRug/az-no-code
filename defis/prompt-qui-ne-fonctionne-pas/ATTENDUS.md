# Grille des attendus -- défi « Le prompt qui ne fonctionne pas »

Version : 1.0.0
Date : 2026-10-05
Session : S170z-ccweb
Compagnon : `/defi-hebdo-alegria` v1.8.1 (Étape 1, grille BLOQUANTE ; Étape 10 bis, défi de type texte sans base ni code)

Fichier interne (le mot « défi » y est permis). Ne se poste pas.

## Énoncé (citation exacte)

Titre : Le prompt qui ne fonctionne pas

Contexte : « un client t'a envoyé un message. "Bonjour, j'ai récupéré ce prompt sur LinkedIn, tout le monde disait qu'il était incroyable. Chez moi il me sort des posts qui se ressemblent tous, pleins de phrases creuses, et en plus il me met des emojis alors que je lui ai écrit de ne pas en mettre. Je ne comprends pas ce qui cloche. Vous pouvez me le réparer pour que ça marche enfin ? Sonia, formatrice en gestion du stress pour dirigeants de PME". »

Son prompt : « Tu es un expert en copywriting de renommée mondiale. Écris un post LinkedIn engageant sur mon sujet. Sois percutant et impactant. Pas d'emojis. Le post doit être viral. »

Ce qu'il faut faire : « lister ce qui ne va pas dans ce prompt ; écrire ta version corrigée ; montrer le post obtenu avec ta version, pour prouver la différence. Pas de PDF ni de docx : un message normal, séparé en deux messages si besoin. 15 points de participation + 15 points de réussite. Ouvert jusqu'au 09/10/2026 à 17h00. Poste ta réponse dans ce fil. »

## Grille

| Attendu (citation exacte) | Points | Où c'est rendu | Statut |
|---|---|---|---|
| « lister ce qui ne va pas dans ce prompt » | - | Message 1 : 3 blocs, un par plainte, chacun relié à la cause dans le prompt (sujet et cible absents, 4 adjectifs d'effet, interdiction négative isolée) | fait |
| « écrire ta version corrigée » | - | Message 2 : prompt en 4 blocs (CONTEXTE, LE POST, FORMAT, STYLE) + ligne de relecture, 843 caractères, 5 crochets à remplir | fait |
| « montrer le post obtenu avec ta version, pour prouver la différence » | - | Message 2 : post de 942 caractères réellement généré par le prompt corrigé, mis en face des mesures du post d'origine données au message 1 | fait |
| « Pas de PDF ni de docx : un message normal » | - | 2 messages texte, aucune pièce jointe | fait |
| « séparé en deux messages si besoin » | - | 2 messages de 1966 et 1976 caractères (`LC_ALL=C.UTF-8 wc -m` moins le saut de ligne final), limite Discord de 2000 respectée | fait |
| « Vous pouvez me le réparer pour que ça marche enfin ? » | - | 1re ligne du message 1 : « Bonjour Sonia, oui, ça se répare. Mais ton prompt n'est pas cassé : il est vide. » | fait |
| « des posts qui se ressemblent tous » | - | Message 1, bloc 1 : « sur mon sujet » ne porte ni sujet ni cible, donc rien ne change d'une fois sur l'autre ; preuve par les 2 exécutions du prompt d'origine | fait |
| « pleins de phrases creuses » | - | Message 1, bloc 2 : les 4 adjectifs d'effet (engageant, percutant, impactant, viral) ne décrivent pas le texte ; citation d'une phrase creuse réellement produite | fait |
| « il me met des emojis alors que je lui ai écrit de ne pas en mettre » | - | Message 1, bloc 3 : 3 causes (le style viral commandé par le reste du prompt, la consigne négative isolée en fin de prompt, l'historique ou la mémoire de la conversation) | fait |
| « Sonia, formatrice en gestion du stress pour dirigeants de PME » | - | Tutoiement de Sonia, bloc CONTEXTE du prompt corrigé rédigé à son métier, lecteur visé = dirigeant de PME | fait |
| « 15 points de participation + 15 points de réussite » | 30 | Les 3 livrables de l'énoncé sont rendus dans l'ordre demandé | fait |
| « Ouvert jusqu'au 09/10/2026 à 17h00. Poste ta réponse dans ce fil » | - | À poster par David dans le fil (Fait David) | à faire |

## Protocole de preuve (les 2 prompts réellement exécutés)

Les 3 exécutions ci-dessous ont tourné dans des contextes neufs, sans historique, avec le même cadrage : « tu simules un assistant de chat généraliste grand public qui reçoit ce message dans une conversation neuve ». Sorties archivées dans `MESSAGES-DISCORD.md`, annexe.

-> Exécution 1 -- prompt d'origine, mot pour mot, sans aucun autre message. Résultat : aucun post. L'outil pose 5 questions (sujet, cible, objectif, histoire, ton) puis livre une trame de post viral générique. C'est la preuve directe que « sur mon sujet » ne porte pas de sujet.

-> Exécution 2 -- prompt d'origine précédé de la seule ligne « Je suis Sonia, formatrice en gestion du stress pour dirigeants de PME » (la situation réelle de Sonia, dont l'outil connaît le métier). Résultat : un post de 1884 caractères, mesuré à 3 flèches U+2192, 5 hashtags, 4 questions, 0 fait concret, 0 cas vécu. Les 3 plaintes de Sonia y sont visibles.

-> Exécution 3 -- prompt corrigé, crochets remplis avec un cas d'exemple. Résultat : un post de 942 caractères, mesuré à 0 emoji, 0 pictogramme hors ponctuation, 0 hashtag, 0 question, 0 astérisque, 0 puce. Dans la fourchette 900-1300 demandée.

Mesures faites avec `LC_ALL=C.UTF-8 wc -m` et un contrôle Python des points de code au-dessus de U+2000 hors ponctuation typographique.

## Écarts assumés (à ne pas maquiller)

-> Aucun emoji littéral n'est sorti de l'exécution 2 : ce qui est revenu, ce sont 3 flèches et 5 hashtags. Le message 1 le dit tel quel (« pas d'emoji, mais 3 flèches, 5 hashtags, 4 questions. La consigne n'a tenu que sur le mot exact ») au lieu de prétendre reproduire les emojis de Sonia. Le mécanisme expliqué est le même et la démonstration est plus solide ainsi : l'interdiction n'a tenu que sur le mot écrit, pas sur la famille de caractères décoratifs qu'elle visait.

-> Le crochet « cas réel » du prompt corrigé n'est pas rempli par un témoignage présenté comme vécu. Le post de l'exécution 3 repose sur un cas d'exemple, annoncé comme tel dans le message 2 (« le cas réel est le tien, ici un exemple »). Sonia remplace le crochet par son propre cas.

-> L'outil de Sonia n'est pas connu (ChatGPT, Claude, autre). Les 3 causes données au message 1 valent pour tous : elles portent sur le prompt et sur la conversation, pas sur un produit.

## Hors énoncé : pas de base, pas de code, pas de lien

Défi de type texte. Aucune base Airtable ou NocoDB, aucun déploiement, aucune capture, aucune ligne de portfolio : il n'y a pas de livrable à montrer, seulement un prompt et deux posts. La règle des 6 liens minimum ne s'applique pas.

## Changelog

-> 1.0.0 -- 2026-10-05 (S170z-ccweb) : création avant rédaction de la réponse. 12 attendus, 11 faits, 1 à faire (postage par David).
