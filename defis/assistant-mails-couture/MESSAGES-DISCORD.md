# Messages Discord -- défi "assistant mails formation couture"

## Message 1 : fil du projet (livrable noté, à poster avant le 06/11/2026 17h00)

Statut : à poster. v3 (S261105z-ccweb) -- réponses réelles du bot Chatbase (tests du 05/11, captures dans `captures/`). v2 (Cor David 02/11) : outil changé de Projet Claude à Chatbase.

++++++

Oui, je peux te faire cet assistant, et il ne dira jamais ce qui n'est pas vrai : c'est la toute première règle que je lui ai écrite, exactement pour éviter le problème du remboursement inventé que tu as vécu.

Meilleur outil : Chatbase. C'est un agent conversationnel qui répond depuis une base de connaissance qu'on lui donne, pas un agent de création libre : il ne devine pas, il cherche dans ce qu'on lui a fourni. Il faut juste lui donner toutes les informations nécessaires et des instructions précises sur ce qu'il doit et ne doit pas faire, exactement ce qui suit. Gratuit pour démarrer, 10 à 15 minutes pour le configurer.

Consignes de l'assistant (à coller dans les instructions du bot Chatbase) :
- Rôle : répondre aux élèves à partir des 6 informations ci-dessous, rien d'autre.
- Règle absolue : si l'info n'est pas dans la liste, l'assistant dit qu'il ne sait pas et transmet la question à toi, jamais d'invention, jamais d'exception promise.
- Les 6 infos : 12 modules à vie, remboursement 14 jours si moins de 3 modules vus, machine d'entrée de gamme suffisante, live le 1er mardi du mois à 19h, patrons PDF taille réelle, tout le reste écrit directement à toi.

Testé en vrai dans Chatbase avec 3 questions, dont une sans réponse dans tes infos (captures jointes) :
1- "Faut-il une surjeteuse ?" -> "Une machine à coudre d'entrée de gamme suffit, vous n'avez pas besoin de surjeteuse."
2- "J'ai regardé 4 modules, je peux être remboursée ?" -> "Vous en avez regardé 4, donc je ne peux pas vous confirmer un remboursement", puis il te transmet la demande sans rien promettre. C'est ce test qui prouve qu'il ne cède pas pour faire plaisir à l'élève : exactement ton inquiétude de départ.
3- "Paiement en plusieurs fois possible ?" -> "Je n'ai pas cette information. Je transmets votre question à la formatrice." Il ne devine pas, il te passe la main.

++++++

Mesuré : 1867 caractères (`LC_ALL=C.UTF-8 wc -m` sur le texte entre les deux `++++++`, lignes vides de bord retirées) -- sous la limite de 2000. À poster avec les 3 fichiers de `captures/` joints (énoncé : captures d'écran, pas de document).

## Message 2 : salon défi-hebdomadaire -- à poster (méthode en texte, Val David 05/11)

Statut : à poster. Val David 05/11 : méthode écrite dans le message, pas de lien Chatbase (le lien public du bot fusionné ouvrirait aussi la qualification LIA'M). Décision David 02/11 : 2 visions, Chatbase (méthode) + LIA'M Libre (offre, 3 places communauté). Jamais nommer "Alegria"/"défi"/"formation" dans ce message (vocabulaire public, CLAUDE.md IV).

**Vérifié par WebFetch le 02/11** (RACINE règle 6, aucun fait sans source) :
-> `salescloser.fr` ne porte **aucun** paramètre de tracking `?src=` ni équivalent -- en ajouter un dans un lien ne ferait rien tant que le site n'est pas codé pour le lire. Pas de tracking automatique pour l'instant, à construire en session code si David le veut vraiment.
-> `salescloser.fr` ne mentionne **nulle part** "LIA'M Libre" -- c'est une offre interne, pas encore publique. Un visiteur qui clique le lien verra l'offre standard LIA'M (500 EUR/mois), pas Libre : l'offre doit donc être annoncée dans le texte du message Discord lui-même, pas sur la page.
-> `/joignable-partout` est une page réelle du site (répond aux messages clients sur WhatsApp/Telegram/voix, CTA "Essaie, parle-lui") -- le choix de David est confirmé comme le plus proche du sujet du défi parmi les 6 pages existantes.

++++++

Cette semaine : un assistant qui répond aux mails répétitifs d'une vendeuse de cours en ligne à sa place, sans jamais inventer une réponse fausse -- exactement le risque qu'elle avait vécu avec une autre IA.

2 façons de le construire :

1- Chatbase (l'outil que pas mal d'entre vous ont déjà croisé) -> agent conversationnel, pas agent de création : il répond depuis une base de connaissance qu'on lui donne, il n'invente pas. 15 minutes à configurer, gratuit pour démarrer.
   La méthode en 3 gestes : a- donne-lui tes infos comme seule source ; b- écris la règle "si ce n'est pas dans la liste, tu dis que tu ne sais pas et tu transmets" ; c- teste-le d'abord sur la question qui fâche (chez moi : un remboursement hors conditions, il a tenu bon).

2- LIA'M -> même principe, appliqué à tes prospects : il qualifie, relance et prend les RDV sur WhatsApp ou Telegram, et passe la main quand il ne sait pas. Pas d'abonnement, tu paies les crédits que tu consommes. 3 places en LIA'M Libre ouvertes pour la communauté : si ça t'intéresse, teste-le sur https://www.salescloser.fr/joignable-partout, et demande-moi en privé ce que "Libre" veut dire.

Choisis selon où tu en es.

++++++

**Correction de fond (05/11, S261105z)** : "chez toi, ton propre moteur" (Message 2) et "la version chez soi [...] ZDR" (Message 3) retirés -- contraires à la fiche LIA'M Libre v1.0.3 §V ("accès à un service hébergé, pas un logiciel à installer chez toi", "pas d'infrastructure dédiée"), qui est aussi la source du bot. Remplacés par le périmètre réel de la fiche (§II-III : 1 canal WhatsApp ou Telegram, qualification, relances, RDV, crédits sans abonnement). Message 3 : "il dit non" remplacé par ce que le bot a réellement répondu.

**Note technique (02/11)** : pas de `?src=` -- vérifié, le site ne lit aucun paramètre de ce genre aujourd'hui. Le suivi de l'origine "Alegria" passe par le texte du message (qui donne le contexte) et par la mention en privé, pas par un lien traqué. Un vrai tracking se construirait en session code, hors périmètre de ce défi.

## Message 3 : partage-tes-victoires -- à poster

Statut : à poster, après le Message 2. Plus aucun élément manquant.

++++++

Petite victoire cette semaine : un assistant mail qui refuse de promettre ce qui n'est pas vrai, testé sur le cas qui fâche (une élève qui a dépassé le quota de modules vus et demande quand même un remboursement -- il refuse de le confirmer et transmet, sans rien promettre).

Ce que je retiens : un agent qui répond depuis une base de connaissance plutôt que de deviner règle d'office le problème de l'IA qui invente. C'est le même principe qui fait tourner LIA'M côté vente : il répond depuis ce qu'on lui a donné, et passe la main quand il ne sait pas.

3 places en LIA'M Libre pour la communauté -- demande en privé ce que "Libre" veut dire.

++++++

## Décision tranchée (Cor David 02/11)

Entre les 3 options de l'Étape 10 bis (pas de message / méthode sans lien / construire un outil réel), David a tranché : construire réellement (Chatbase + a minima un contexte LIA'M dédié), puis écrire les 2 messages avec leurs vrais liens une fois les outils en place -- voir pas-à-pas en cours dans la session.

## Annexe -- les 3 échanges de test, réels (bot Chatbase "AZen Guide", Playground, conversation neuve à chaque test, 05/11/2026)

Réponses recopiées telles que David les a collées depuis Chatbase (apostrophes typographiques du bot ramenées en apostrophes droites). Captures : `captures/261105_AZNcDf_Test1-Surjeteuse.png`, `..._Test2-Remboursement-Refuse.png`, `..._Test3-Paiement-Transmis.png`.

### Test 1

**Question élève :** "Bonjour, est-ce qu'il faut une surjeteuse pour suivre la formation ou une machine à coudre normale suffit ?"

**Réponse du bot :** "Bonjour, une machine à coudre d'entrée de gamme suffit pour suivre la formation : vous n'avez pas besoin de surjeteuse. L'équipe de la formation"

### Test 2 (cas limite volontaire, répond à la crainte du client)

**Question élève :** "Bonjour, j'ai acheté la formation il y a une semaine et j'ai regardé 4 modules sur les 12. Est-ce que je peux être remboursée, je n'ai pas le temps de continuer ?"

**Réponse du bot :** "Bonjour, le remboursement est possible dans les 14 jours suivant l'achat uniquement si moins de 3 modules ont été regardés. Vous en avez regardé 4, donc je ne peux pas vous confirmer un remboursement selon ces conditions. Je transmets tout de même votre demande à la formatrice, qui vous répondra personnellement. L'équipe de la formation"

### Test 3 (sans réponse dans les infos fournies)

**Question élève :** "Bonjour, est-ce que je peux payer la formation en plusieurs fois sans frais ?"

**Réponse du bot :** "Bonjour, je n'ai pas cette information. Je transmets votre question à la formatrice, qui vous répondra personnellement. L'équipe de la formation"

### Écarts entre la simulation du 02/11 et le bot réel

-> **Vouvoiement partout** : l'élève ne tutoie pas, le bot vouvoie -- conforme à la consigne "tutoiement si l'élève tutoie, vouvoiement sinon". La simulation du 02/11 tutoyait à tort au test 2.

-> **Signature présente** ("L'équipe de la formation") : conforme, absente de la simulation.

-> **Test 2, plus prudent que la simulation** : le bot écrit "je ne peux pas vous confirmer un remboursement" plutôt qu'un non sec, et transmet d'office à la formatrice. Aucune promesse, aucune exception : le garde-fou tient.

-> **Test 3, plus court** : "je n'ai pas cette information", sans "très vite" -- la simulation promettait un délai qui n'est écrit nulle part. Le bot réel est plus fidèle à la règle anti-invention que la simulation.

-> **Nom affiché dans l'en-tête des captures : "David Ruggieri IA"**, pas "AZen Guide" -- c'est le nom public du widget du bot fusionné (décision David du 02/11). Visible sur les captures 1 et 2.
