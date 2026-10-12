# Messages Discord -- défi 7 « Mon automatisation déconne »

Version : 1.0.0
Date : 2026-10-13
Session : S261013z-ccdd
Compagnon : `ATTENDUS.md`, `DIAGNOSTIC.md`, `/defi-hebdo-alegria` v1.9.0

Archive des messages tels qu'ils sont à poster. Défi de type texte : aucun lien, aucune base, aucun code ; la règle des 6 liens ne s'applique pas (Étape 10 bis).

Comptage : `LC_ALL=C.UTF-8 wc -m` moins le saut de ligne final. Les `**` sont le gras Discord.

## Message 1 : fil du projet (livrable noté)

Statut : **à poster** dans le fil « Mon automatisation déconne » du salon `défi-hebdomadaire`, avant le 16/10/2026 17h00. Deux messages à la suite.

### 1/2 (1548 caractères)

Bonjour Camille, rassure-toi : ton scénario n'est pas à refaire, trois réglages suffisent. Voici les 3 problèmes, chacun relié à ce que tu vois.

**Problème 1 : le même prospect 4 ou 5 fois par jour**
Cause : le module 1 surveille la "Date de dernière modification" (Last modified time). Il réagit donc à toute fiche qui bouge, nouvelle ou ancienne. Et ton scénario fait lui-même bouger la fiche à la fin : le module 5 coche "Notifié". Au passage suivant, Make voit une fiche modifiée, le statut est toujours "Nouveau", le filtre laisse passer, le mail repart, la case est recochée, et ça recommence à chaque passage. D'où tes 4 ou 5 envois par jour.
La case "Notifié" est bien remplie, mais personne ne la lit : le filtre ne la regarde jamais.

**Problème 2 : 3 centres d'intérêt = 3 mails**
Cause : le module 2, l'Iterator, découpe la liste des centres d'intérêt en morceaux et fait passer chaque morceau séparément dans la suite du scénario. Trois centres cochés, trois passages, trois mails. Pour Make, ton prospect est devenu trois prospects.

**Problème 3 : les prospects du site ne remontent jamais**
Cause : le filtre (module 3) ne laisse passer que Statut = "Nouveau". Ton formulaire crée les fiches avec un Statut vide. Vide n'est pas égal à "Nouveau", donc ces prospects s'arrêtent au filtre, sans aucune alerte : pour Make, tout s'est bien passé.
Bonne nouvelle : ils ne sont pas perdus. Ta dizaine de prospects est toujours dans Airtable, avec un Statut vide et "Notifié" non coché.

La correction, module par module : message suivant.

### 2/2 (1736 caractères)

**Module 1, Airtable > Watch Records**
Change le champ déclencheur : "Date de création" au lieu de "Date de dernière modification". Si ta table n'a pas ce champ, crée-le (type "Date de création", Created time). Le scénario ne réagit plus qu'aux nouvelles fiches, et cocher "Notifié" ne le relance plus.

**Module 2, Iterator**
Supprime-le. Un prospect = un mail.

**Module 3, Filtre**
Deux conditions :
-> "Notifié" n'est pas coché
-> ET "Statut" est égal à "Nouveau" OU "Statut" est vide
Astuce Make : Airtable n'envoie ni une case décochée ni un champ vide. Pour ces deux cas, choisis l'opérateur "n'existe pas" (Does not exist).

**Module 4, Gmail > Send an email**
Un seul mail avec tous les centres d'intérêt sur une ligne : join(Centres d'intérêt; ", ") donne "Stress, Sommeil, Organisation".

**Module 5, Airtable > Update a record**
Garde-le : il coche "Notifié". Avec le nouveau déclencheur, cette coche ne relance plus rien, et le filtre s'en sert comme garde-fou.

**À la source, dans Airtable**
Donne au champ Statut la valeur par défaut "Nouveau", puis envoie un faux prospect depuis ton site pour vérifier qu'il arrive bien en "Nouveau". Si ce n'est pas le cas, pas de panique : le filtre accepte déjà le Statut vide.

**Ta dizaine de prospects perdus**
Le nouveau déclencheur ne regarde que les fiches créées à partir de maintenant : il ne les rattrapera pas. Crée une vue "À rattraper" (Statut vide et "Notifié" non coché), relis-les, puis transmets-les à tes coachs en une fois et coche "Notifié".

**Une question pour finir**
Tes deux coachs doivent-ils recevoir tous les prospects, ou chacun ceux de ses centres d'intérêt ? Dans le second cas, on ajoute un aiguillage (Router) qui envoie chaque prospect au bon coach.

## Messages 2 et 3 (salons)

Sans objet par défaut : l'énoncé ne demande aucun outil. Option ouverte à David : un message de méthode dans `causons-ici`, appuyé sur `DIAGNOSTIC.md`.
