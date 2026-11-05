# Consignes de l'assistant mail -- formation couture en ligne

Artefact réutilisable pour le portfolio (anonymisé : pas de nom de produit réel, pas de nom de cliente réelle -- "Nadia" est une cliente fictive d'exercice).

## Outil choisi

**Chatbase.** Rationale de David (Cor David 02/11, reprise ici) : "le meilleur outil à utiliser c'est Chatbase parce que c'est un agent conversationnel et non un agent de création, il n'inventera donc pas ses réponses. Mais avant il faut lui fournir toutes les informations nécessaires ainsi que des instructions précises sur ce qu'il doit et ne doit pas faire."

-> **Agent conversationnel, pas agent de création.** Chatbase répond depuis une base de connaissance qu'on lui donne (les 6 informations de l'énoncé) plutôt que de générer librement -- plus proche d'une recherche dans un document que d'une improvisation, ce qui réduit le risque d'invention à la racine. C'est l'outil étudié dans la communauté pour ce type de cas, donc déjà familier pour qui le reconnaît.

-> **Pas de code, 10-15 minutes de configuration.** Compte Chatbase, base de connaissance = les 6 infos, instructions = le texte ci-dessous, lien de chat généré automatiquement.

-> **Aucune automatisation d'envoi d'office.** Nadia peut soit garder le lien du chat pour elle (copier-coller des réponses), soit le partager directement aux élèves -- choix qu'elle fait elle-même selon sa confiance dans l'outil après le test.

-> **Volume compatible.** Une trentaine de mails par semaine passe largement dans ce format.

**Alternative DIY équivalente**, pour qui préfère rester chez Anthropic ou OpenAI sans ouvrir de nouveau compte : un **Projet Claude** (claude.ai) ou un **GPT personnalisé** (ChatGPT) avec les mêmes instructions -- fonctionnellement identique (instructions permanentes + fichier de connaissance), mais Nadia colle alors elle-même le mail de l'élève dans le chat au lieu d'un lien de bot autonome.

## Instructions à coller dans Chatbase (champ "Instructions" du bot, prêtes à copier)

```
Tu es l'assistant mail d'une formatrice en couture en ligne pour débutantes.

Ton rôle : rédiger une réponse à l'élève qui écrit, en t'appuyant UNIQUEMENT sur les
informations listées ci-dessous.

Règle absolue, non négociable : si une information demandée n'est PAS dans cette liste,
tu ne l'inventes JAMAIS, tu ne devines JAMAIS, tu ne promets JAMAIS une exception, un
geste commercial ou un remboursement qui n'est pas écrit ici. Tu dis à l'élève que tu
transmets sa question directement à la formatrice, qui lui répondra personnellement.

Informations connues :
- 12 modules vidéo, accessibles à vie.
- Remboursement possible dans les 14 jours après l'achat, UNIQUEMENT si moins de 3
  modules ont été regardés.
- Une machine à coudre d'entrée de gamme suffit, pas besoin de surjeteuse.
- Live de questions-réponses le premier mardi de chaque mois à 19h.
- Les patrons sont fournis en PDF, à imprimer en taille réelle.
- Pour tout le reste (sujet absent de cette liste), tu transmets à la formatrice au lieu
  de répondre toi-même.

Ton : chaleureux, simple, jamais de jargon technique. Tutoiement si l'élève tutoie,
vouvoiement sinon. Tu signes "L'équipe de la formation" (jamais "une IA" ni un nom de
modèle).
```

## Garde-fous (champ "Guardrails" de l'éditeur Chatbase, distinct du champ "General")

Vérifié dans la doc Chatbase (02/11) : l'éditeur `Build > Instructions` a deux sections -- `General` (contexte, rôle, ton) et `Guardrails` (règles non négociables qui s'appliquent à CHAQUE conversation, peu importe ce que dit le visiteur). Attention, ce n'est pas le même objet que l'onglet "Guardrails" des réglages de l'agent, qui lui ne gère que l'anti-abus (limite de débit, anti-spam, blocage pays).

Le bot "AZen Guide" porte désormais deux rôles (qualification B2B LIA'M + réponse couture du défi, décision David 02/11 ci-dessous). Texte à coller dans `Guardrails` pour garder les deux rôles séparés et tenir la règle anti-invention :

```
- Ne jamais inventer une information absente de ce qui t'a ete fourni (modules, remboursement,
  materiel, dates, tarifs, delais). Si l'information manque, le dire et transmettre a l'humain
  responsable -- jamais deviner, jamais promettre une exception ou un geste commercial non ecrit.
- Ne jamais promettre un resultat, un delai ou un remboursement qui n'est pas explicitement
  ecrit dans tes informations.
- Ne jamais mettre de pression commerciale ni insister si la personne decline.
- Determiner le registre de la conversation des le premier message et t'y tenir jusqu'a la fin :
  question sur une formation couture = reponse FAQ couture uniquement ; question B2B/LIA'M/vente
  = pre-qualification B2B uniquement. Ne jamais melanger les deux registres dans une meme reponse.
- Pas plus de 3 questions a la fois.
- Si la situation est complexe, sensible ou strategique, orienter vers un echange humain direct,
  jamais decider a la place de la personne responsable.
```

Réglage complémentaire : le curseur de température du modèle (panneau "Model configuration") gagne à rester côté "Reserved" plutôt que "Creative" -- moins de créativité veut dire moins d'invention, exactement l'objectif de l'énoncé.

## Test -- 3 questions, dont une sans réponse dans les infos

Joué directement avec ces instructions (contexte neuf), transcription brute en annexe de `MESSAGES-DISCORD.md`. Résumé :

-> 1- "Faut-il une surjeteuse ?" -> répond avec l'info donnée (non, machine d'entrée de gamme).

-> 2- "J'ai regardé 4 modules, je peux être remboursée ?" -> cas limite volontaire : la règle exige moins de 3 modules vus, l'élève en a vu 4. L'assistant refuse et ne cède pas à la tentation de faire une exception -- c'est le test qui répond exactement à la crainte du client ("elle a promis un remboursement alors que ce n'était pas possible").

-> 3- "Paiement en plusieurs fois possible ?" -> absent de la liste. L'assistant dit qu'il ne sait pas et transmet, au lieu de deviner.

## Étape 2 (non demandée, notée pour plus tard)

Si le volume dépasse ce qu'un copier-coller manuel absorbe, les mêmes instructions se branchent sur un webhook Gmail + n8n (watch Gmail -> appel du même prompt -> brouillon déposé, jamais d'envoi automatique direct) pour gagner le temps du copier-coller. Repris dans `AZ_Portfolio.Notes` si ce projet est ajouté au portfolio.
