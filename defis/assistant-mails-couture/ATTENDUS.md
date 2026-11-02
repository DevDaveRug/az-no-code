# ATTENDUS -- défi "assistant mails formation couture" (défi 7)

Énoncé recopié tel que reçu (client fictif Nadia, formation couture en ligne débutantes).

## Grille des attendus

| Attendu (citation exacte de l'énoncé) | Points | Où c'est rendu | Statut |
|---|---|---|---|
| "Trouver le meilleur outil pour créer cet assistant" | barème | Message 1, section "Outil" | fait |
| "Écrire les consignes de l'assistant" | barème | Message 1, section "Consignes" + `CONSIGNES.md` | fait |
| "Le tester avec au moins 3 questions d'élèves, dont une à laquelle les infos ne permettent pas de répondre" | barème | Message 1, section "Test" + annexe `MESSAGES-DISCORD.md` | fait |
| "Il ne doit dire que ce qui est vrai" (crainte du client sur le remboursement inventé) | barème implicite | 1re ligne du Message 1 + question-test 2 (cas limite 4 modules vus) | fait |
| "Envoie des captures d'écran de ce que tu as fait" / "Aucun document .docx ou présentation" | format | voir Écarts assumés -- captures réelles à prendre par David | sans objet (raison ci-dessous) |
| "La correction sera disponible ce vendredi" + "Ouvert jusqu'au 06/11/2026 à 17h00. Poste ta réponse dans ce fil" | délai, lieu | à poster par David dans le fil | à poster (David) |

## Écarts assumés

-> **Pas de captures d'écran réelles produites par CC.** L'énoncé demande des captures d'un outil SaaS (Projet Claude, ou GPT personnalisé) configuré avec les consignes. CC n'a pas d'identifiants pour se connecter à un compte claude.ai ou ChatGPT au nom de David ou de Nadia depuis cette session -- impossible de produire une vraie capture d'écran d'une interface qu'il n'a pas ouverte. À la place : les 3 échanges de test sont joués ici même, texte brut, en appliquant exactement les consignes écrites (même rigueur que le protocole "3 exécutions" du défi 6). David reproduit la configuration en 5 minutes (instructions prêtes à coller) et prend les vraies captures avant de poster.

-> **Aucune des deux stacks no-code/NocoDB habituelles, aucun code souverain Next.js.** Le fondamental "deux stacks toujours en miroir" (Airtable/NocoDB) vise les défis CRM/base de données. Cet énoncé ne demande ni table, ni formulaire, ni base : c'est un assistant conversationnel, pas une application de gestion. Construire une base pour cet énoncé serait une page de plus que ce qui est demandé, exactement l'erreur citée dans "Erreurs à éviter" (vouloir tout livrer, ou livrer ce qui n'est pas demandé).

-> **Occurrences légitimes du mot "formation" gardées** (auto-check Étape 11) : `CONSIGNES.md`, `MESSAGES-DISCORD.md` et `README.md` emploient "formation" pour désigner le produit vendu par la cliente (une formation couture en ligne), pas la formation Alegria elle-même -- exactement le cas d'usage légitime que l'auto-check demande de garder plutôt que de contourner par une périphrase artificielle. La référence interne à "Alegria" reste uniquement dans `ATTENDUS.md` et `README.md` (fichiers internes), retirée de `CONSIGNES.md` qui est l'artefact public du portfolio.

-> **Étape 10 bis appliquée par analogie.** Ce défi n'est ni un `MVP` multi-tables, ni un `diagnostic`, ni un `prompt` à corriger -- c'est un nouveau cas : écrire un prompt/assistant neuf. Un seul outil choisi (pas de 3 stacks Airtable/NocoDB/code), donc la règle des 6 liens est structurellement impossible ici aussi. Les 3 options habituelles ont été exposées à David (Cor David 02/11) : il tranche pour **construire réellement** -- Chatbase (l'outil répondant au critère "meilleur outil") et un volet commercial LIA'M Libre, avec les 2 messages de salon écrits une fois les vrais liens disponibles.

-> **Tool changé en cours de route (Cor David 02/11) : Chatbase plutôt que Projet Claude.** David a tranché avec sa propre justification ("agent conversationnel, pas agent de création, il n'invente pas") -- `CONSIGNES.md` et `MESSAGES-DISCORD.md` mis à jour en conséquence. Le Projet Claude / GPT reste mentionné comme alternative DIY équivalente.

-> **Un bot Chatbase dédié, pas le bot "AZen Guide" existant.** David a collé les consignes de l'énoncé dans son bot Chatbase déjà en place pour Sales Closer (AZen Guide, qualification B2B de prospects LIA'M). Recommandation : ne pas fusionner les deux rôles dans le même bot -- AZen Guide sert déjà en production à qualifier de vrais prospects, et son ton/sa mission (consultatif B2B, jamais vendeur direct) est incompatible avec le ton attendu pour répondre à une élève de couture. Un bot séparé, créé pour ce défi, évite tout risque sur le fonctionnement réel de AZen Guide et reste rapide à construire (mêmes instructions, nouveau bot).

-> **"Tester en vrai" plutôt que des captures, idée de David -- bonus, pas un remplacement.** L'énoncé demande explicitement des captures d'écran ("Envoie des captures d'écran de ce que tu as fait [...] Aucun document .docx ou présentation"). Le lien public du bot Chatbase (une fois créé) permet en plus à Eva et aux membres Alegria de tester l'assistant eux-mêmes -- ajouté en bonus dans la réponse, les captures d'écran restent produites pour respecter l'énoncé à la lettre.
