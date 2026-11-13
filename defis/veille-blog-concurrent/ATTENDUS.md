# ATTENDUS -- défi "veille blog concurrent" (défi 8)

Énoncé reçu le 09/11/2026 (cliente fictive Claire, coach en nutrition). Échéance : 13/11/2026 17h00, réponse dans le fil du projet, **captures d'écran uniquement** (« pas de document, pas de lien »).

## Canal et déclencheur (fondamental v1.10.0, leçon du défi 7)

-> **Où le problème arrive aujourd'hui** : sur le blog de la concurrente. Claire va le consulter à la main, « je perds un temps fou ».

-> **Ce qui doit déclencher le travail** : la publication d'un nouvel article, détectée par le flux RSS du blog et vérifiée automatiquement à intervalle régulier. Claire ne fait rien (« sans que j'aie à y penser »).

-> **La solution supprime-t-elle l'action manuelle ?** Oui, à condition que le scénario soit **planifié et actif**, et pas lancé à la main. Une capture doit le prouver (planification visible, scénario activé).

-> **Où le résultat arrive** : dans un tableau que Claire ouvre quand elle veut, une ligne par article : titre, date, lien.

## Grille des attendus

| Attendu (citation exacte de l'énoncé) | Points | Où c'est rendu | Statut |
|---|---|---|---|
| "Trouver le meilleur outil pour répondre au besoin de Claire" | réussite | Message 1, « Meilleur outil : Make + Airtable » | fait |
| "Créer le tableau qui accueille les articles" (titre, date, lien) | réussite | table Airtable `AZ_VeilleArticles` (créée par API le 10/11) + capture 03 | fait |
| "Construire l'automatisation qui ajoute une ligne pour chaque nouvel article d'un blog de ton choix" | réussite | scénario Make « Integration RSS, Airtable » (RSS Watch feed items -> Airtable Create a record), captures 01 à 03, 10 lignes importées | fait |
| "ses nouveaux articles arrivent tout seuls [...] sans que j'aie à y penser" (implicite : automatique et planifié) | réussite | capture 01 : « Every 1 hour » + « Active » | fait |
| "Si ça marche bien, j'ajouterai d'autres sites plus tard" (implicite : ajouter un site sans tout refaire) | réussite | colonne Source + dernier paragraphe du Message 1 | fait |
| "Envoie des captures d'écran [...] Pas de document, pas de lien" | format | Message 1 (1558 caractères) + 3 captures, aucun lien de partage | fait |
| "Ouvert jusqu'au 13/11/2026 à 17h00. Poste ta réponse dans ce fil" | délai, lieu | fil du projet | à poster (David) |

## Blog choisi

-> `https://www.lanutrition.fr/rss.xml` : flux RSS valide lu le 10/11/2026 (HTTP 200, 10 articles, champs `title`, `pubDate`, `link` présents). Blog de nutrition grand public, cohérent avec le métier de Claire.

-> Limite constatée : rythme de publication faible (dernier article le 02/11/2026). Pour montrer une ligne ajoutée « en vrai », le premier passage importe les derniers articles existants.

## Écarts assumés

-> **Mapping de la date** : Make ne propose pas de champ « Date published » pour ce flux mais `date`, `Date created` ou `pubdate`. Les deux premiers sont au format ISO (2026-11-02T09:24:06Z) et Airtable les lit directement ; c'est ce qui a été retenu, les dates du tableau sont justes (lues par l'API Airtable le 13/11).

-> **Capture 02** montre le panneau de correspondance pendant le choix de la date : la preuve que la date est bien reliée est la capture 03, où la colonne est remplie.

-> **Un lien dans le Message 1 ?** Non : l'énoncé dit « pas de lien », la seule URL citée est le nom du blog, pas un lien de partage.

-> **Versions n8n + NocoDB et code** : construites après le rendu, pour la veille de David (session d), conformément à sa décision du 10/11. Elles ne changent pas la réponse notée.
