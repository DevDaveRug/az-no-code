# ATTENDUS -- défi "assistant mails formation couture" (défi 7)

Énoncé recopié tel que reçu (client fictif Nadia, formation couture en ligne débutantes).

## Grille des attendus

| Attendu (citation exacte de l'énoncé) | Points | Où c'est rendu | Statut |
|---|---|---|---|
| "Trouver le meilleur outil pour créer cet assistant" | barème | Message 1, section "Outil" | fait |
| "Écrire les consignes de l'assistant" | barème | Message 1, section "Consignes" + `CONSIGNES.md` | fait |
| "Le tester avec au moins 3 questions d'élèves, dont une à laquelle les infos ne permettent pas de répondre" | barème | Message 1, section "Test" (réponses réelles du bot, 05/11) + annexe `MESSAGES-DISCORD.md` + 3 captures | fait |
| "Il ne doit dire que ce qui est vrai" (crainte du client sur le remboursement inventé) | barème implicite | 1re ligne du Message 1 + question-test 2 (cas limite 4 modules vus) | fait |
| "Envoie des captures d'écran de ce que tu as fait" / "Aucun document .docx ou présentation" | format | `captures/` : 3 captures réelles du bot Chatbase (05/11), jointes au Message 1 ; aucun .docx | fait |
| "La correction sera disponible ce vendredi" + "Ouvert jusqu'au 06/11/2026 à 17h00. Poste ta réponse dans ce fil" | délai, lieu | Message 1 posté par David dans le fil du projet le 05/11, avant l'échéance du 06/11 17h00, 3 captures jointes | fait |

## Écarts assumés

-> **Captures prises par David, pas par CC (05/11).** CC n'a pas d'accès au compte Chatbase de David : les 3 tests ont été joués par David dans le Playground (conversation neuve à chaque fois), réponses collées dans la session et captures jointes. Écarts constatés entre la simulation du 02/11 et le bot réel : le bot vouvoie (l'élève ne tutoie pas, conforme à la consigne), signe "L'équipe de la formation", et au test 3 ne promet aucun délai ("très vite" de la simulation supprimé). Le bot réel est plus fidèle à la règle anti-invention que la simulation : Message 1 réécrit sur ses vraies réponses, détail en annexe de `MESSAGES-DISCORD.md`.

-> **Messages 2 et 3 corrigés sur le fond avant publication (05/11).** Les brouillons présentaient LIA'M Libre comme "chez toi, ton propre moteur" : faux selon la fiche LIA'M Libre v1.0.3 §V (service hébergé, pas d'infrastructure dédiée), qui est aussi une source du bot. Remplacé par le périmètre réel de la fiche. Méthode Chatbase écrite dans le Message 2 (Val David 05/11) plutôt qu'un lien vers le bot fusionné, qui aurait ouvert aussi la qualification LIA'M au public. Règle des 6 liens de l'Étape 11 : sans objet, l'énoncé ne demande aucune base ni formulaire (Étape 10 bis, voir plus bas).

-> **En-tête des captures : "David Ruggieri IA"**, nom public du widget du bot fusionné (décision David du 02/11), pas un nom de cliente. Rien à flouter : aucune donnée personnelle d'élève dans les tests.

-> **Aucune des deux stacks no-code/NocoDB habituelles, aucun code souverain Next.js.** Le fondamental "deux stacks toujours en miroir" (Airtable/NocoDB) vise les défis CRM/base de données. Cet énoncé ne demande ni table, ni formulaire, ni base : c'est un assistant conversationnel, pas une application de gestion. Construire une base pour cet énoncé serait une page de plus que ce qui est demandé, exactement l'erreur citée dans "Erreurs à éviter" (vouloir tout livrer, ou livrer ce qui n'est pas demandé).

-> **Occurrences légitimes du mot "formation" gardées** (auto-check Étape 11) : `CONSIGNES.md`, `MESSAGES-DISCORD.md` et `README.md` emploient "formation" pour désigner le produit vendu par la cliente (une formation couture en ligne), pas la formation Alegria elle-même -- exactement le cas d'usage légitime que l'auto-check demande de garder plutôt que de contourner par une périphrase artificielle. La référence interne à "Alegria" reste uniquement dans `ATTENDUS.md` et `README.md` (fichiers internes), retirée de `CONSIGNES.md` qui est l'artefact public du portfolio.

-> **Étape 10 bis appliquée par analogie.** Ce défi n'est ni un `MVP` multi-tables, ni un `diagnostic`, ni un `prompt` à corriger -- c'est un nouveau cas : écrire un prompt/assistant neuf. Un seul outil choisi (pas de 3 stacks Airtable/NocoDB/code), donc la règle des 6 liens est structurellement impossible ici aussi. Les 3 options habituelles ont été exposées à David (Cor David 02/11) : il tranche pour **construire réellement** -- Chatbase (l'outil répondant au critère "meilleur outil") et un volet commercial LIA'M Libre, avec les 2 messages de salon écrits une fois les vrais liens disponibles.

-> **Tool changé en cours de route (Cor David 02/11) : Chatbase plutôt que Projet Claude.** David a tranché avec sa propre justification ("agent conversationnel, pas agent de création, il n'invente pas") -- `CONSIGNES.md` et `MESSAGES-DISCORD.md` mis à jour en conséquence. Le Projet Claude / GPT reste mentionné comme alternative DIY équivalente.

-> **Décision finale de David (02/11) : bot fusionné, pas séparé.** CC avait recommandé un bot Chatbase dédié plutôt que de toucher "AZen Guide" (qualification B2B en production). David a tranché l'inverse : AZen Guide porte désormais les deux rôles (qualification LIA'M + réponse couture du défi). Pour limiter le risque de ce choix, une règle de garde-fou explicite ("ne jamais mélanger les deux registres dans une même réponse") a été ajoutée dans la section Guardrails de l'agent -- voir `CONSIGNES.md`.

-> **"Tester en vrai" plutôt que des captures, idée de David -- bonus, pas un remplacement.** L'énoncé demande explicitement des captures d'écran ("Envoie des captures d'écran de ce que tu as fait [...] Aucun document .docx ou présentation"). Le lien public du bot Chatbase (une fois créé) permet en plus à Eva et aux membres Alegria de tester l'assistant eux-mêmes -- ajouté en bonus dans la réponse, les captures d'écran restent produites pour respecter l'énoncé à la lettre.
