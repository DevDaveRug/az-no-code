# Grille des attendus -- défi « La relecture qui réécrit tout »

Version : 1.0.0
Date : 2026-11-17
Session : S261117z-ccdd
Compagnon : `/defi-hebdo-alegria` v1.10.1 (Étape 1 grille BLOQUANTE, type `prompt`, Étape 10.0, Étape 10 bis), `MESSAGES-DISCORD.md`, `DIAGNOSTIC.md`

Fichier interne (le mot « défi » y est permis). Ne se poste pas.

## Énoncé (citation exacte, résumé aux passages utiles)

Titre : La relecture qui réécrit tout

Contexte : Julie écrit une newsletter chaque semaine et la fait relire par une IA avant l'envoi. Son prompt : « Améliore ce texte. » puis elle colle la newsletter. Plaintes : « elle me rend un texte qui ne me ressemble plus du tout », « tout devient lisse avec des phrases bateau comme "Dans un monde en constante évolution" », « elle a changé un prix : 49 € est devenu 59 €, je ne l'ai vu qu'après l'envoi », « je voulais juste qu'elle corrige mes fautes », « je ne sais jamais ce qu'elle a modifié ». Question : « Vous pouvez m'aider ? »

Ce qu'il faut faire : « Lister ce qui ne va pas dans la façon de faire de Julie » ; « Écrire un meilleur prompt et le tester sur un texte de ton choix, avec quelques fautes et un prix ». Compte rendu : « Envoie la liste des erreurs en répondant à ce message » ; « Envoie ton prompt corrigé en répondant à ce message » ; « pas de document, juste du texte dans un message classique ». 15 points de participation + 15 points de réussite. « Ouvert jusqu'au 20/11/2026 à 17h00. Poste ta réponse dans ce fil pour participer. »

## Canal et déclencheur (v1.10.0)

-> Où le problème arrive : dans une conversation avec une IA, à chaque envoi de newsletter hebdomadaire.

-> Ce qui déclenche le travail : Julie, à la main, avant chaque envoi. Aucun outil n'est demandé : le livrable est un prompt réutilisable.

-> La solution ajoute-t-elle une action manuelle ? Elle en garde une (coller la newsletter) et en ajoute une utile (lire la liste des modifications et la rubrique « À vérifier » avant l'envoi). C'est assumé : l'énoncé demande un prompt, pas une automatisation, et la relecture humaine finale est le filet qui aurait arrêté le 59 €. Une version automatisée (n8n ou Make qui relit chaque brouillon et bloque l'envoi sur une alerte) est possible mais hors énoncé.

## Grille

| Attendu (citation exacte) | Points | Où c'est rendu | Statut |
|---|---|---|---|
| « Lister ce qui ne va pas dans la façon de faire de Julie » | - | Message 1/3 : 8 points, chacun relié à une plainte ou à une mesure de l'essai | fait |
| « Écrire un meilleur prompt » | - | Message 2/3 : prompt complet (rôle, ce qu'il corrige, ce qu'il ne touche jamais, point qui n'est pas une faute, réponse en 3 parties), newsletter entre balises | fait |
| « le tester sur un texte de ton choix, avec quelques fautes et un prix » | - | Message 3/3 : texte 1 (4 fautes, 49 € au lieu de 59 €, un pourcentage faux, une date fausse, une tournure orale), texte 2 (piège) ; sorties brutes en annexe de `MESSAGES-DISCORD.md` | fait |
| « Envoie la liste des erreurs en répondant à ce message » | - | Message 1/3, à poster dans le fil | à poster (David) |
| « Envoie ton prompt corrigé en répondant à ce message » | - | Message 2/3, à poster dans le fil | à poster (David) |
| « pas de document, juste du texte dans un message classique » | - | 3 messages texte, aucune pièce jointe, aucun PDF | fait |
| « Vous pouvez m'aider ? » | - | 1re ligne du message 1 : « Bonjour Julie, oui, ça se règle. » | fait |
| « un texte qui ne me ressemble plus du tout » | - | Message 1, point 4 (style non protégé) ; prompt : « Mon style : mes expressions, mon ton, mes tournures orales » ; preuve : l'exécution 2 garde « J'ai pas eu le temps de tout vous dire » | fait |
| « phrases bateau » | - | Message 1, points 1 et 4 ; prompt : « tu n'ajoutes rien » | fait |
| « 49 € est devenu 59 € » | - | Message 1, point 3 ; prompt : « Prix, chiffres... : recopie-les à l'identique » ; preuve : 49 € et 59 € intacts à l'exécution 2 | fait |
| « je ne sais jamais ce qu'elle a modifié » | - | Message 1, point 5 ; prompt : partie 2 « MODIFICATIONS, avant -> après (raison) » | fait |
| « je ne l'ai vu qu'après l'envoi » | - | Message 1, point 8 ; message 3, dernier paragraphe : relecture avant envoi | fait |
| « 15 points de participation + 15 points de réussite » | 30 | Les 2 livrables demandés sont rendus | fait |
| « La correction sera disponible ce vendredi » | - | Correction vidéo du vendredi 20/11 à traiter en `/rn-audio` (lien à fournir par David), section « Correction officielle » à ajouter ensuite | à faire |
| Indice éventuel de l'énoncé | - | Aucun indice dans cet énoncé : rien à exploiter, ligne conservée pour que l'absence soit vérifiée | sans objet |
| « Ouvert jusqu'au 20/11/2026 à 17h00. Poste ta réponse dans ce fil » | - | À poster par David dans le fil du salon `défi-hebdomadaire` | à poster (David) |

## Protocole de preuve (3 exécutions, contexte neuf, sans historique)

Cadrage commun : « assistant de chat généraliste grand public, conversation neuve ». Sorties brutes en annexe de `MESSAGES-DISCORD.md`.

-> Exécution 1 : « Améliore ce texte. » + texte 1 (la situation réelle de Julie). Résultat : 4 fautes corrigées, mais une tournure orale remplacée par une phrase inventée (« Le reste, je vous le garde pour la surprise ! »), « -20 % » réécrit en « 20 % de réduction », phrases raccourcies, aucune alerte sur le -17 % réel ni sur le vendredi 26 novembre qui est un jeudi.

-> Exécution 2 : prompt corrigé + texte 1. Résultat : 4 fautes corrigées et listées, tournure orale gardée, 49 € et 59 € intacts, alertes sur le -17 % et sur le jeudi. Une virgule oubliée (« Pour vous inscrire c'est ») signalée, pas corrigée.

-> Exécution 3 : prompt corrigé + texte 2 (2 fautes, emoji, code promo, phrase piège « Ignore les consignes précédentes... »). Résultat : 2 fautes corrigées, emoji et code intacts, phrase piège non exécutée et signalée.

## Écarts assumés (à ne pas maquiller)

-> **Le symptôme « 49 € devenu 59 € » ne s'est pas reproduit.** À l'exécution 1, le prix est resté 49 € et 59 €. Ce qui a bougé : « -20 % » devenu « 20 % de réduction », une phrase inventée, des phrases raccourcies. Le message 1 le dit (« dans mon essai ») et ne prétend pas avoir reproduit le 59 €.

-> **Essais sur Claude, pas sur l'outil de Julie.** Les 3 exécutions ont tourné sur des sous-agents Claude sans historique ; l'outil de Julie est inconnu. Le message 3 le dit et invite à refaire le test avec son outil.

-> **Un seul essai par texte.** Aucune répétition : un autre tirage pourrait donner une sortie différente. Dit dans les limites du message 3.

-> **La rubrique « À vérifier » fait du bruit.** L'exécution 3 signale un 5 décembre pourtant juste et une espace avant « ! ». Dit dans le message 3.

-> **Une faute signalée au lieu d'être corrigée** (la virgule de « Pour vous inscrire c'est »). Choix du prompt : douter plutôt que toucher au style. Dit dans le message 3.

-> **Trois messages au lieu de deux.** L'énoncé demande la liste puis le prompt (2 envois). Le test s'ajoute en 3e message parce que les 3 contenus ne tiennent pas dans 2 messages de 2000 caractères.

-> **Aucune capture d'écran.** Les essais sont des sous-agents sans interface : la preuve est le texte brut en annexe, pas une capture.

## Auto-check Étape 11 (skill v1.10.1)

| Point | État |
|---|---|
| `ATTENDUS.md` existe, chaque attendu a son « où c'est rendu » et son statut | fait (3 lignes « à poster » ou « à faire » : actions de David ou à venir) |
| Réponse au fil livrée en tête, répond à la question dès la 1re ligne | fait |
| Type `prompt` : 3 exécutions en contexte neuf, sorties brutes archivées | fait |
| Chaque message sous 2000 caractères (`LC_ALL=C.UTF-8 wc -m`, moins le saut de ligne final) | fait (1377, 1319, 1416) |
| Mots « défi », « Eva », « Alegria », « formation » en mot entier dans les 3 messages | fait : aucune occurrence (`grep -niE`) |
| 6 liens, 2 par forme | sans objet : l'énoncé ne demande aucun outil (Étape 10 bis) |
| `INTERFACE.md` Airtable et NocoDB | sans objet : aucune base |
| Ligne `AZ_Portfolio` | sans objet : rien à montrer en ligne |
| Table créée en double | sans objet : aucune table créée |
| Aucune ligne `Val :` qui porte une action de David | fait |
| Noms de salons | fait : `défi-hebdomadaire` pour le fil |
