# Messages Discord -- défi "assistant mails formation couture"

## Message 1 : fil du projet (livrable noté, à poster avant le 06/11/2026 17h00)

Statut : à poster.

++++++

Oui, je peux te faire cet assistant, et il ne dira jamais ce qui n'est pas vrai : c'est la toute première règle que je lui ai écrite, exactement pour éviter le problème du remboursement inventé que tu as vécu.

Meilleur outil : un Projet Claude (ou un GPT personnalisé, strictement équivalent côté ChatGPT). Pas de code, gratuit ou quelques euros par mois, 10 minutes pour le configurer. Tu colles le mail de l'élève dans le chat, l'assistant te propose la réponse déjà prête, tu la relis et tu l'envoies toi-même. Aucune connexion à ta boîte mail, aucun envoi automatique : tu gardes la main sur chaque mail avant qu'il parte, ce qui règle d'office le risque qu'une mauvaise réponse parte seule.

Consignes de l'assistant (à coller dans les instructions du Projet) :
- Rôle : répondre aux élèves à partir des 6 informations ci-dessous, rien d'autre.
- Règle absolue : si l'info n'est pas dans la liste, l'assistant dit qu'il ne sait pas et transmet la question à toi, jamais d'invention, jamais d'exception promise.
- Les 6 infos : 12 modules à vie, remboursement 14 jours si moins de 3 modules vus, machine d'entrée de gamme suffisante, live le 1er mardi du mois à 19h, patrons PDF taille réelle, tout le reste écrit directement à toi.

Testé avec 3 questions, dont une sans réponse dans tes infos :
1- "Faut-il une surjeteuse ?" -> Non, une machine d'entrée de gamme suffit.
2- "J'ai regardé 4 modules, je peux être remboursée ?" -> Non, la règle demande moins de 3 modules vus, transmis à toi si besoin. C'est ce test qui prouve que l'assistant ne cède pas à la tentation de faire plaisir à l'élève : exactement ton inquiétude de départ.
3- "Paiement en plusieurs fois possible ?" -> Pas d'info là-dessus, l'assistant transmet directement à toi au lieu de deviner.

Les 3 échanges complets, instructions prêtes à coller et procédure de mise en place sont dans le fichier joint à ma réponse.

++++++

Mesuré : 1894 caractères (`LC_ALL=C.UTF-8 wc -m`, moins le saut de ligne final) -- sous la limite de 2000.

## Messages 2 et 3 (salons) -- décision en attente de David

Un seul outil utilisé ici (pas de 3 stacks Airtable/NocoDB/code), donc la règle des 6 liens est structurellement impossible, comme au défi 6. Par analogie avec l'Étape 10 bis, 3 options exposées à David (voir UQ de la session) :

-> pas de message de salon

-> un message de méthode sans lien, dans `causons-ici`

-> construire un Projet Claude réel et partageable après coup, puis écrire les 2 messages avec son lien

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
