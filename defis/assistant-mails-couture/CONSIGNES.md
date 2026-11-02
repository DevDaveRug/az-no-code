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

## Test -- 3 questions, dont une sans réponse dans les infos

Joué directement avec ces instructions (contexte neuf), transcription brute en annexe de `MESSAGES-DISCORD.md`. Résumé :

-> 1- "Faut-il une surjeteuse ?" -> répond avec l'info donnée (non, machine d'entrée de gamme).

-> 2- "J'ai regardé 4 modules, je peux être remboursée ?" -> cas limite volontaire : la règle exige moins de 3 modules vus, l'élève en a vu 4. L'assistant refuse et ne cède pas à la tentation de faire une exception -- c'est le test qui répond exactement à la crainte du client ("elle a promis un remboursement alors que ce n'était pas possible").

-> 3- "Paiement en plusieurs fois possible ?" -> absent de la liste. L'assistant dit qu'il ne sait pas et transmet, au lieu de deviner.

## Étape 2 (non demandée, notée pour plus tard)

Si le volume dépasse ce qu'un copier-coller manuel absorbe, les mêmes instructions se branchent sur un webhook Gmail + n8n (watch Gmail -> appel du même prompt -> brouillon déposé, jamais d'envoi automatique direct) pour gagner le temps du copier-coller. Repris dans `AZ_Portfolio.Notes` si ce projet est ajouté au portfolio.
