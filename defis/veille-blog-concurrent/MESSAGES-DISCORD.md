# Messages Discord -- défi "veille blog concurrent" (défi 8)

## Message 1 : fil du projet (livrable noté, à poster avant le 13/11/2026 17h00, avec les 3 captures)

Statut : à poster. Captures à joindre, dans l'ordre : `captures/261113_AZNcDf_01-Make-Scenario-Actif.png`, `..._02-Make-Mapping-Airtable.png`, `..._03-Airtable-Tableau-Rempli.png`.

++++++

Oui Claire, c'est en place : chaque nouvel article de ta concurrente arrive tout seul dans ton tableau, avec son titre, sa date et son lien, et tu n'as plus rien à faire.

Le principe : presque tous les blogs publient un flux RSS, une sorte de liste officielle de leurs derniers articles, mise à jour à chaque publication. Il suffit qu'un outil aille la lire à ta place, régulièrement, et recopie chaque nouveauté dans ton tableau.

Meilleur outil : Make (pour l'automatisation) + Airtable (pour le tableau).
- Make sait lire un flux RSS et ne garde que les articles qu'il n'a pas encore vus : pas de doublon.
- Airtable te donne un vrai tableau, lisible sur ordinateur et téléphone, que tu peux trier par date ou filtrer.
- Aucun code, gratuit pour démarrer.

Ce que j'ai construit (blog choisi pour la démo : lanutrition.fr) :
1- Le tableau : Titre, Date de publication, Lien, Source (le nom du blog) et la date d'ajout automatique.
2- Le scénario Make : "surveiller le flux RSS du blog" puis "créer une ligne dans Airtable".
3- La planification : il tourne tout seul toutes les heures, scénario activé. C'est ce qui répond à ton "sans que j'aie à y penser" : tu ne lances jamais rien.

Captures jointes : le scénario actif (toutes les heures), la correspondance des champs, et le tableau rempli avec les 10 derniers articles du blog.

Et pour ajouter d'autres sites plus tard : la colonne Source est déjà prévue. Pour chaque nouveau blog, on ajoute son flux RSS dans Make, et ses articles arrivent dans le même tableau, rangés par source. Rien à refaire.

++++++
