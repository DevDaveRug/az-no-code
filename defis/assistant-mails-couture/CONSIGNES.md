# Consignes de l'assistant mail -- formation couture en ligne

Artefact réutilisable pour le portfolio (anonymisé : pas de nom de produit réel, pas de nom de cliente réelle -- "Nadia" est une cliente fictive d'exercice).

## Outil choisi

**Projet Claude** (claude.ai, fonction "Projects") ou, équivalent côté ChatGPT, un **GPT personnalisé**. Les deux font exactement la même chose : un espace avec des instructions permanentes + un fichier de connaissance, qu'on rouvre pour chaque question. Choix du Projet Claude ici, pour 4 raisons :

-> **Pas de code, rien à déployer.** Nadia colle le mail de l'élève dans le chat, elle récupère la réponse déjà rédigée, elle relit, elle envoie. 10 minutes de mise en place, zéro maintenance.

-> **Aucune automatisation d'envoi.** Le risque de l'énoncé (une IA qui promet un remboursement à tort) vient d'un système qui répond et qui envoie tout seul. Ici, Nadia garde la main sur chaque mail avant qu'il parte -- la pire IA au monde ne peut pas faire de dégât si un humain relit avant d'envoyer.

-> **Volume compatible.** Une trentaine de mails par semaine, en copier-coller manuel, reste largement gérable et ne justifie pas un bot branché sur la boîte mail (ce qui serait l'étape 2, mentionnée en fin de fichier, si le volume augmente).

-> **Coût nul à faible.** Gratuit avec un compte Claude standard tant que l'usage reste modéré ; un abonnement payant seulement si le volume explose.

## Instructions à coller dans le Projet (prêtes à copier)

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
