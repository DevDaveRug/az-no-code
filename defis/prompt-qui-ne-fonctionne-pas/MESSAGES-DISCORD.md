# Messages Discord -- défi « Le prompt qui ne fonctionne pas »

Version : 1.0.0
Date : 2026-10-05
Session : S170z-ccweb
Compagnon : `ATTENDUS.md` (grille des attendus), `DIAGNOSTIC.md` (version publique anonymisée), `/defi-hebdo-alegria` v1.8.1 (Étape 10.0, réponse au fil = livrable noté ; Étape 10 bis, défi `diagnostic` ; Étape 10, archive des messages)

Archive des messages tels qu'ils sont à poster. Défi de type texte : aucun lien, aucune capture, aucune base, aucun code. La règle des 6 liens minimum ne s'applique pas.

Comptage : `LC_ALL=C.UTF-8 wc -m` moins le saut de ligne final, soit le nombre de caractères que Discord compte. Sans locale UTF-8, `wc -m` compte les octets et surévalue un texte français d'environ 2 % (1998 octets au lieu de 1966 caractères pour le message 1), ce qui déclenche une fausse alerte de dépassement.

Les messages déjà postés se corrigent en éditant le message dans Discord, pas en le repostant.

## Message 1 : fil du projet (livrable noté)

Statut : à poster par David dans le fil avant le 09/10/2026 17h00. Deux messages à la suite, 1966 et 1978 caractères (limite 2000). À copier tel quel, les `**` sont le gras Discord.

Val : poster les messages 1/2 puis 2/2 dans le fil du projet avant le 09/10/2026 17h00.

### 1/2

Bonjour Sonia, oui, ça se répare. Mais ton prompt n'est pas cassé : il est vide. Tes trois plaintes sortent toutes de ce vide.

**Les posts qui se ressemblent tous.** Ton prompt dit "sur mon sujet" : le sujet n'y est pas, ta cible non plus. Je l'ai fait tourner tel quel : l'outil n'a écrit aucun post, il m'a posé 5 questions. Avec seulement ta ligne de présentation, il a écrit sur "le stress des dirigeants" en général. Rien ne change d'une fois sur l'autre, donc rien ne change dans les posts.

**Les phrases creuses.** Tu commandes quatre effets : engageant, percutant, impactant, viral. Aucun ne décrit le texte, ils décrivent ce que tu espères du lecteur. L'outil ne sait pas les mesurer, alors il imite ce qui leur ressemble : accroche choc, lignes courtes, formules à citer. Mon essai a sorti "Le stress n'est pas la preuve que vous travaillez dur. C'est une information." Ça sonne, ça ne dit rien. Et "viral" ne se commande pas : ça se passe chez le lecteur, pas dans le texte.

**Les emojis malgré la consigne.** Trois causes, dans cet ordre.

1- Tout le reste du prompt commande le style LinkedIn viral : "renommée mondiale", "percutant", "viral". Dans ce style, emojis, flèches et hashtags font partie du format. Quatre mots d'interdiction contre quatre mots qui appellent l'inverse : c'est le style qui gagne, et de plus en plus à mesure que le texte s'allonge.

2- Ta consigne est négative et seule, en fin de prompt. "Pas d'emojis" demande de retenir quelque chose pendant 1 500 caractères. "Texte brut uniquement, aucun caractère décoratif" donne quelque chose à faire : ça, c'est tenu.

3- Ton historique. Si ta conversation ou la mémoire de ton outil garde d'anciens posts avec emojis, ces exemples pèsent plus que ta ligne d'interdiction. Ouvre une conversation neuve.

Mesuré sur mon essai : pas d'emoji, mais 3 flèches, 5 hashtags, 4 questions. La consigne n'a tenu que sur le mot exact.

Ton prompt corrigé et les deux posts : message suivant.

### 2/2

Ton prompt corrigé. Seuls les crochets changent ; le cas réel est le tien, ici un exemple :

CONTEXTE
Formatrice en gestion du stress pour dirigeants de PME.
Lecteur : [QUI, une ligne]
Une seule idée : [SUJET]
Mon cas réel : [À REMPLACER : qui, quand, la phrase entendue, ce qui a changé]
Ce qu'il doit faire après : [ACTION]

LE POST
900 à 1300 caractères, en français, à la première personne.
Mon cas réel en 3 à 5 phrases concrètes, puis l'idée à retenir, puis [ACTION] en une phrase affirmative.

FORMAT
Texte brut. Aucun emoji, pictogramme, flèche, puce, astérisque, hashtag, ni question. Paragraphes de 1 à 3 lignes.

STYLE
Phrases courtes, mots de tous les jours, ton calme.
Bannis : incroyable, révolutionner, game changer, ça change tout, secret, "et toi ?".
N'invente rien : aucun chiffre, citation ou exemple que je ne t'ai pas donné. S'il te manque un détail, écris [À COMPLÉTER].

Relis FORMAT, corrige, rends le post seul.

Le post obtenu, 964 caractères, 0 emoji, 0 flèche, 0 hashtag, 0 question :

À la fin de la deuxième journée d'atelier, un dirigeant de 34 salariés m'a dit « Je ne suis pas fatigué de travailler, je suis fatigué de décider pour tout le monde. »

Nous avons écrit ses décisions de la semaine sur une feuille. Il y en avait onze. Sept ne lui appartenaient pas.

Trois semaines plus tard, il en avait rendu cinq à ses responsables.

Si vous réglez vos dossiers le dimanche soir sans en parler à personne, ce n'est peut-être pas le travail qui pèse.

La fatigue d'un dirigeant vient moins de son travail que des décisions qu'il prend à la place des autres.

Chaque décision qui n'est pas la vôtre reste pourtant sur vos épaules. Ce n'est pas une question de courage ni de temps, mais de savoir à qui appartient chaque décision.

Écrire les décisions suffit à les voir autrement. Sur une feuille, la réponse se voit.

Lundi matin, posez la même feuille devant vous, écrivez vos décisions de la semaine et cochez celles qui ne sont pas les vôtres.

## Annexe : les 3 exécutions (preuve, ne se poste pas)

Contextes neufs, sans historique, même cadrage pour les trois : « tu simules un assistant de chat généraliste grand public qui reçoit ce message dans une conversation neuve ».

### Exécution 1 -- prompt d'origine, mot pour mot, sans aucun autre message

Aucun post produit. L'outil pose 5 questions, puis livre une trame générique. Sortie (abrégée sur la trame) :

    Avec plaisir, mais il me manque l'essentiel : vous n'avez pas précisé votre sujet. Un post viral ne s'écrit pas dans le vide. Il repose sur un angle précis, une audience identifiée et une histoire vraie.

    Pour vous livrer un post vraiment percutant, répondez simplement à ces questions (même en quelques mots) :

    1. Le sujet : de quoi voulez-vous parler ?
    2. La cible : qui doit se sentir concerné ?
    3. L'objectif : que doit faire le lecteur à la fin ?
    4. Votre histoire : un fait concret, un chiffre, une anecdote ou une conviction forte qui vous est propre.
    5. Le ton : provocateur, inspirant, pédagogique, sobre, personnel ?

    En attendant, voici la structure que j'utilise pour les posts qui performent sur LinkedIn. [...suit une trame générique : accroche, contexte, révélation, phrase à retenir, question ouverte, puis 5 règles de portée.]

    Envoyez-moi votre sujet et ces éléments, et je vous rédige un post prêt à publier, avec une accroche et, si vous le souhaitez, deux ou trois variantes à tester.

### Exécution 2 -- prompt d'origine, précédé de « Je suis Sonia, formatrice en gestion du stress pour dirigeants de PME »

Post de 1884 caractères. Mesuré : 3 flèches U+2192, 5 hashtags, 4 questions, aucun fait concret, aucun cas vécu. Aucun emoji littéral. Sortie complète :

    Votre entreprise ne tombera pas à cause d'un concurrent.

    Elle tombera le jour où vous ne tiendrez plus.

    Je forme des dirigeants de PME à la gestion du stress. Et il y a une phrase que j'entends à chaque fois, presque mot pour mot :

    "Moi, ça va. Je gère."

    Ils gèrent la trésorerie. Les clients. Les équipes. Les imprévus du lundi matin.
    Mais personne ne leur a jamais appris à gérer ce qui les maintient debout : eux-mêmes.

    Voici ce que je constate sur le terrain :

    → Le dirigeant épuisé ne le dit jamais. Il devient irritable, il tranche trop vite, il ne délègue plus.
    → Son stress ne reste pas dans son bureau. Il se propage dans toute l'entreprise.
    → Les signaux arrivent bien avant la rupture. On les ignore parce qu'on les prend pour de la motivation.

    Le stress n'est pas la preuve que vous travaillez dur.
    C'est une information. Et quand on l'ignore trop longtemps, elle finit par coûter très cher : à votre santé, à vos décisions, à vos équipes.

    Trois questions à vous poser dès ce soir :

    1. Quand ai-je vraiment déconnecté pour la dernière fois, sans téléphone, sans culpabilité ?
    2. Mes décisions importantes sont-elles prises à froid, ou sous pression ?
    3. Si je m'arrêtais deux semaines, qu'est-ce qui s'effondrerait, et pourquoi ?

    Si la troisième réponse vous met mal à l'aise, ce n'est pas un problème de planning.
    C'est un problème de système. Et un système, ça se reconstruit.

    Un dirigeant qui maîtrise son stress ne devient pas plus mou.
    Il devient plus lucide, plus stable, plus difficile à déstabiliser.
    C'est exactement ce dont une PME a besoin à sa tête.

    Alors posez-vous honnêtement la question :
    combien de temps pensez-vous encore tenir comme ça ?

    Si ce post vous parle, partagez-le à un dirigeant qui "gère" un peu trop bien. Parfois, il suffit qu'on lui tende le miroir.

    #Leadership #GestionDuStress #DirigeantsPME #BienEtreAuTravail #Management

### Exécution 3 -- prompt corrigé, crochets remplis avec un cas d'exemple

Post de 964 caractères. Mesuré : 0 emoji, 0 pictogramme hors ponctuation, 0 hashtag, 0 question, 0 astérisque, 0 puce. Dans la fourchette 900-1300 demandée. Sortie complète : c'est le post du message 2/2 ci-dessus.

Cette exécution a été relancée une fois : le cas d'exemple disait « deuxième journée de formation », et l'auto-check de l'Étape 11 interdit le mot « formation » dans un message Discord. Le crochet « cas réel » étant une variable d'entrée, « formation » a été remplacé par « atelier » dans le cas d'exemple et le prompt relancé à l'identique. Le post montré est bien celui que le prompt corrigé a produit, aucune retouche à la main.

Crochets remplis pour cette exécution (cas d'exemple, ni un témoignage de David ni un témoignage de Sonia) :

    Lecteur : dirigeant d'une PME de 20 à 60 salariés, qui règle ses dossiers le dimanche soir et n'en parle à personne.
    Une seule idée : la fatigue d'un dirigeant vient moins de son travail que des décisions qu'il prend à la place des autres.
    Mon cas réel : en fin de deuxième journée d'atelier, un dirigeant de 34 salariés m'a dit "je ne suis pas fatigué de travailler, je suis fatigué de décider pour tout le monde". On a écrit ses décisions de la semaine sur une feuille : sur onze, sept ne lui appartenaient pas. Trois semaines plus tard il en avait rendu cinq à ses responsables.
    Ce qu'il doit faire après : poser la même feuille lundi matin et cocher les décisions qui ne sont pas les siennes.

## Salons Défis et Victoires -- à trancher par David

Le skill v1.8.1 attend 3 messages (fil, salon Défis, salon Victoires), les deux derniers avec au minimum 6 liens, au minimum 2 par forme (formulaire + table) pour Airtable, NocoDB et le code. L'Étape 10 bis lève cette règle pour la réponse au fil, pas pour les messages de salon.

Ici les 6 liens sont impossibles : l'énoncé ne demande aucun outil, il n'y a donc ni base, ni formulaire, ni déploiement, donc rien à lier. Trois issues, à choisir par David :

-> 1- ne rien poster en salon cette semaine (le livrable noté est complet sans cela)

-> 2- poster un message de salon sans lien, sur la méthode : un prompt se répare en le remplissant, pas en l'enjolivant, avec la grille d'audit en 7 points de `DIAGNOSTIC.md`

-> 3- construire après coup un petit outil à montrer (un formulaire qui assemble le prompt depuis les 5 crochets, dans les 3 stacks) pour retrouver les 6 liens, en sachant que l'énoncé ne le demande pas

Le skill ne prévoit pas le cas d'un défi `diagnostic` livré sans aucune stack : signal remonté à la session qui édite le skill.

## Changelog

-> 1.1.0 -- 2026-10-05 (S170z-ccweb) : relecture contre le skill v1.8.1 une fois `dr-context` cloné. Étiquettes alignées sur l'Étape 10.0 (`Message 1 : fil du projet (livrable noté)`), ligne `Val` ajoutée, section Salons réécrite avec les 3 issues possibles et le signal sur la règle des 6 liens. Exécution 3 relancée avec « atelier » au lieu de « formation » dans le cas d'exemple (auto-check Étape 11) : post de 964 caractères, message 2/2 à 1978 caractères.

-> 1.0.0 -- 2026-10-05 (S170z-ccweb) : création. 2 messages de fil (1966 et 1976 caractères), annexe des 3 exécutions.
