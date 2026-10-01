# Grille des attendus -- défi 5 « Une base Airtable qui part en vrille »

Version : 1.0.0
Date : 2026-10-01
Session : S163z-ccweb (rattrapage : la grille est née de la Cor David « le skill a tendance à omettre de répondre aux attendus du défi », après la livraison)
Compagnon : `/defi-hebdo-alegria` v1.8.0 (Étape 1, grille BLOQUANTE)

Fichier interne (le mot « défi » y est permis). Ne se poste pas.

## Énoncé (citation exacte)

Titre : Une base Airtable qui part en vrille

Brief client de Léa : « Ma base marche mais tout le monde se plaint. On n'arrive pas à sortir le chiffre d'affaires par client, les filtres ne donnent jamais le bon résultat et la moitié des trucs sont en double. Est-ce qu'il faut tout refaire ? »

Livrable :

-> Format texte (PAS de PDF) S'il n'y a pas assez de caractères sur Discord, envoyez 2 messages à la suite.

-> Un diagnostic en 5 points maximum

-> La structure cible que tu recommandes : quelles tables, quels champs, quels types.

-> Le barème reste le même que d'habitude, mais augmenté : 20 points de participation + 20 points de réussite

Barème : Diagnostic des types de champs : 6 points. Identification du besoin de séparer les clients dans une table dédiée : 6 points. Structure cible cohérente : 6 points. Formulation compréhensible par une non-technicienne : 2 point.

Indice : Une seule des sept colonnes a le bon type. Et la raison pour laquelle le chiffre d'affaires par client est impossible à sortir n'est pas un problème de formule.

Ouvert jusqu'au 02/10/2026 à 17h00. Poste ta réponse dans ce fil pour participer.

## Grille

| Attendu (citation exacte) | Points | Où c'est rendu | Statut |
|---|---|---|---|
| « Format texte (PAS de PDF) », « envoyez 2 messages à la suite » | - | Réponse au fil en 2 messages texte (1829 et 1767 caractères), postée le 01/10 | fait |
| « Un diagnostic en 5 points maximum » | - | Message 1, points 1- à 5- | fait |
| « La structure cible que tu recommandes : quelles tables, quels champs, quels types » | - | Message 2 : table Clients (7 champs typés) + table Missions (6 champs typés) | fait |
| « Diagnostic des types de champs » | 6 | Message 1, points 1-, 3-, 4-, 5- (texte au lieu de devise, sélection unique, case à cocher, date) | fait |
| « Identification du besoin de séparer les clients dans une table dédiée » | 6 | Message 1, point 2- (« C'est LA raison... ») + message 2, table Clients | fait |
| « Structure cible cohérente » | 6 | Message 2 : 2 tables reliées, cumuls CA facturé / reste à facturer, plan de migration en 5 étapes sans ressaisie | fait |
| « Formulation compréhensible par une non-technicienne » | 2 | Tutoiement de Léa, exemples tirés de sa base (« 80k€ », « Marie dupont »), aucun terme technique sans traduction | fait |
| « Est-ce qu'il faut tout refaire ? » | - | 1re phrase du message 1 : « pas besoin de tout refaire » | fait |
| « On n'arrive pas à sortir le chiffre d'affaires par client » | - | Message 1 point 2-, message 2 « Tes 3 soucis, réglés » | fait |
| « les filtres ne donnent jamais le bon résultat » | - | Message 1 point 4-, message 2 « Tes 3 soucis, réglés » | fait |
| « la moitié des trucs sont en double » | - | Message 1 point 2-, message 2 étape 5- (Maintenance page / Maintenance) | fait |
| Indice « Une seule des sept colonnes a le bon type » | - | Message 1 point 1- (« Seule "Mission" a le bon type ») | fait |
| Indice « n'est pas un problème de formule » | - | Message 1 point 2- (« Ce n'est pas un problème de formule ») | fait |
| « Ouvert jusqu'au 02/10/2026 à 17h00. Poste ta réponse dans ce fil » | - | Postée par David dans le fil le 01/10, avant la limite (Fait David S163z) | fait |

## Au-delà de l'énoncé (portfolio, non noté)

-> Airtable : formulaire « Nouvelle mission » + base `Sales Closer Souverain` (AZ_Missions reliée à SC_Prospects, clients = statut Gagné).

-> NocoDB souverain : formulaire public + vue partagée en lecture seule « Clients et CA ».

-> Code Next.js + Prisma + Neon (schéma `missions`) : https://missions-ca-par-client.demo.salescloser.fr

-> Messages salon Défis et salon Victoires : 6 liens (formulaire + base pour chacune des 3 solutions).

## Changelog

-> 1.0.0 -- 2026-10-01 (S163z-ccweb) : création, grille remplie a posteriori sur la réponse postée le 01/10 (14 attendus, 14 faits).
