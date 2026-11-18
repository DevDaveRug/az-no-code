# Grille d'audit réutilisable : un prompt de relecture qui réécrit tout

Version : 1.0.0
Date : 2026-11-17

Cas : une personne demande à une IA d'« améliorer » un texte alors qu'elle veut seulement qu'on corrige ses fautes. Le texte revient lissé, un chiffre a changé, et personne ne sait ce qui a bougé.

## Les 8 symptômes et leur cause

1- Le texte ne ressemble plus à son auteur : la consigne dit « améliore », donc l'IA décide seule de ce qui est mieux.

2- Phrases génériques ajoutées : rien n'interdit d'ajouter.

3- Un prix ou une date change : rien ne protège les chiffres.

4- Aucune trace des changements : aucun format de réponse n'est demandé.

5- Incohérences non vues (pourcentage faux, jour de la semaine faux) : l'IA n'a pas l'ordre de signaler.

6- Une consigne cachée dans le texte peut être exécutée : le texte n'est pas séparé de la consigne.

7- Le besoin réel n'est pas formulé : corriger n'est pas améliorer.

8- Aucune relecture humaine après l'IA : rien n'est contrôlé avant l'envoi.

## Le prompt qui règle les 8

Rôle de correcteur, liste de ce qui se corrige, liste de ce qui ne se touche jamais (chiffres, dates, liens, style, tournures orales), règle du doute (signaler au lieu de modifier), règle des points qui ne sont pas des fautes, séparation du texte par balises avec l'ordre de ne rien exécuter, réponse en 3 parties : texte corrigé, liste « avant -> après (raison) », rubrique « à vérifier ».

## Limites mesurées

-> Un prompt réduit fortement le risque, il ne le supprime pas : relire la liste des modifications avant l'envoi reste le vrai garde-fou.

-> La rubrique « à vérifier » fait du bruit : elle se lit, elle ne se suit pas aveuglément.

-> Une faute peut être signalée au lieu d'être corrigée (le prompt préfère douter que toucher au style).

-> Tester sur son propre outil : le comportement varie d'un outil à l'autre.
