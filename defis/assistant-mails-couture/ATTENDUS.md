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

-> **Étape 10 bis appliquée par analogie.** Ce défi n'est ni un `MVP` multi-tables, ni un `diagnostic`, ni un `prompt` à corriger -- c'est un nouveau cas : écrire un prompt/assistant neuf. Un seul outil choisi (pas de 3 stacks), donc la règle des 6 liens est structurellement impossible ici aussi. Les 3 options habituelles (pas de message de salon / message de méthode sans lien / construire un outil partageable après coup) sont exposées à David, pas tranchées seules.
