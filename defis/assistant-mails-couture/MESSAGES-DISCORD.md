# Messages Discord -- défi "assistant mails formation couture"

## Message 1 : fil du projet (livrable noté, à poster avant le 06/11/2026 17h00)

Statut : à poster. v2 (Cor David 02/11) -- outil changé de Projet Claude à Chatbase.

++++++

Oui, je peux te faire cet assistant, et il ne dira jamais ce qui n'est pas vrai : c'est la toute première règle que je lui ai écrite, exactement pour éviter le problème du remboursement inventé que tu as vécu.

Meilleur outil : Chatbase. C'est un agent conversationnel qui répond depuis une base de connaissance qu'on lui donne, pas un agent de création libre : il ne devine pas, il cherche dans ce qu'on lui a fourni. Il faut juste lui donner toutes les informations nécessaires et des instructions précises sur ce qu'il doit et ne doit pas faire, exactement ce qui suit. Gratuit pour démarrer, 10 à 15 minutes pour le configurer.

Consignes de l'assistant (à coller dans les instructions du bot Chatbase) :
- Rôle : répondre aux élèves à partir des 6 informations ci-dessous, rien d'autre.
- Règle absolue : si l'info n'est pas dans la liste, l'assistant dit qu'il ne sait pas et transmet la question à toi, jamais d'invention, jamais d'exception promise.
- Les 6 infos : 12 modules à vie, remboursement 14 jours si moins de 3 modules vus, machine d'entrée de gamme suffisante, live le 1er mardi du mois à 19h, patrons PDF taille réelle, tout le reste écrit directement à toi.

Testé avec 3 questions, dont une sans réponse dans tes infos :
1- "Faut-il une surjeteuse ?" -> Non, une machine d'entrée de gamme suffit.
2- "J'ai regardé 4 modules, je peux être remboursée ?" -> Non, la règle demande moins de 3 modules vus, transmis à toi si besoin. C'est ce test qui prouve que l'assistant ne cède pas à la tentation de faire plaisir à l'élève : exactement ton inquiétude de départ.
3- "Paiement en plusieurs fois possible ?" -> Pas d'info là-dessus, l'assistant transmet directement à toi au lieu de deviner.

Les 3 échanges complets, instructions prêtes à coller et procédure de mise en place sont dans le fichier joint à ma réponse.

++++++

Mesuré : 1835 caractères (`LC_ALL=C.UTF-8 wc -m`, moins le saut de ligne final) -- sous la limite de 2000.

## Message 2 : salon défi-hebdomadaire -- BROUILLON, en attente du lien CTA LIA'M Libre (Cor David 02/11)

Statut : brouillon, pas prêt à poster -- il manque le lien CTA pour LIA'M Libre (UQ posée en fin de tour). Décision David : 2 visions, ChatBase (méthode) + LIA'M Libre (offre, 3 places communauté). Jamais nommer "Alegria"/"défi"/"formation" dans ce message (vocabulaire public, CLAUDE.md IV) -- la communauté sait déjà le contexte sans qu'on ait besoin de le nommer.

++++++

Cette semaine : un assistant qui répond aux mails répétitifs d'une vendeuse de cours en ligne à sa place, sans jamais inventer une réponse fausse -- exactement le risque qu'elle avait vécu avec une autre IA.

2 façons de le construire :

1- Chatbase (l'outil que pas mal d'entre vous ont déjà croisé) -> agent conversationnel, pas agent de création : il répond depuis une base de connaissance qu'on lui donne, il n'invente pas. 15 minutes à configurer, gratuit pour démarrer.
   Méthode complète : {LIEN_OU_PROCEDURE_CHATBASE}

2- LIA'M -> même principe, mais chez toi : ton propre moteur, pas un abonnement à vie chez un tiers. 3 places en LIA'M Libre ouvertes pour la communauté : si ça t'intéresse, demande-moi en privé ce que "Libre" veut dire, ou dis-le directement à LIA'M en arrivant sur {LIEN_CTA_LIAM} -- il saura pourquoi tu viens.

Choisis selon où tu en es.

++++++

## Message 3 : partage-tes-victoires -- BROUILLON, dépend du Message 2

Statut : brouillon, dépend des mêmes éléments manquants que le Message 2.

++++++

Petite victoire cette semaine : un assistant mail qui refuse de promettre ce qui n'est pas vrai, testé sur le cas qui fâche (une élève qui a dépassé le quota de modules vus et demande quand même un remboursement -- il dit non, sans céder).

Ce que je retiens : un agent qui répond depuis une base de connaissance plutôt que de deviner règle d'office le problème de l'IA qui invente. Et avoir la version chez soi (LIA'M) plutôt que chez un tiers, c'est la même logique que je répète chaque semaine ici : ZDR, pour ne pas laisser tourner tes données ailleurs que chez toi.

3 places en LIA'M Libre pour la communauté -- demande en privé ce que "Libre" veut dire.

++++++

## Décision tranchée (Cor David 02/11)

Entre les 3 options de l'Étape 10 bis (pas de message / méthode sans lien / construire un outil réel), David a tranché : construire réellement (Chatbase + a minima un contexte LIA'M dédié), puis écrire les 2 messages avec leurs vrais liens une fois les outils en place -- voir pas-à-pas en cours dans la session.

## Annexe -- les 3 échanges de test, bruts (contexte neuf, instructions de `CONSIGNES.md` appliquées à la lettre)

### Test 1

**Question élève :** "Bonjour, est-ce qu'il faut une surjeteuse pour suivre la formation ou une machine à coudre normale suffit ?"

**Réponse de l'assistant :** "Bonjour ! Non, pas besoin de surjeteuse : une machine à coudre d'entrée de gamme suffit très bien pour suivre la formation. Bonne couture !"

### Test 2 (cas limite volontaire, répond à la crainte du client)

**Question élève :** "Bonjour, j'ai acheté la formation il y a une semaine et j'ai regardé 4 modules sur les 12. Est-ce que je peux être remboursée, je n'ai pas le temps de continuer ?"

**Réponse de l'assistant :** "Bonjour, je comprends la question. Le remboursement est possible dans les 14 jours après l'achat, à condition d'avoir regardé moins de 3 modules. Comme tu as déjà regardé 4 modules, tu n'es malheureusement plus dans les conditions du remboursement. Si tu penses qu'il y a un cas particulier, je transmets ta question directement à la formatrice qui pourra te répondre."

### Test 3 (sans réponse dans les infos fournies)

**Question élève :** "Bonjour, est-ce que je peux payer la formation en plusieurs fois sans frais ?"

**Réponse de l'assistant :** "Bonjour, je n'ai pas cette information sous la main concernant un paiement en plusieurs fois. Je transmets directement ta question à la formatrice, qui va te répondre personnellement très vite."
