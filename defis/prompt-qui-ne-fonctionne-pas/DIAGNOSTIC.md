# Réparer un prompt qui ne fonctionne pas -- grille d'audit

Version : 1.0.0
Date : 2026-10-05

Fichier public et anonymisé : aucune cliente n'y est nommée. Grille réutilisable pour auditer n'importe quel prompt de rédaction récupéré en ligne.

## Le cas type

Une indépendante récupère un prompt de rédaction LinkedIn très partagé. Chez elle, il produit des posts qui se ressemblent tous, pleins de phrases creuses, et il place des emojis alors que le prompt lui en interdit. Prompt d'origine, 4 phrases :

    Tu es un expert en copywriting de renommée mondiale. Écris un post LinkedIn
    engageant sur mon sujet. Sois percutant et impactant. Pas d'emojis.
    Le post doit être viral.

Le prompt n'est pas cassé, il est vide. Les trois symptômes sortent du même vide.

## Les 3 symptômes et leur cause

### 1- Des sorties qui se ressemblent toutes

Cause : le prompt ne porte ni sujet ni lecteur. « sur mon sujet » désigne une variable que personne n'a remplie. Rien ne change d'une exécution à l'autre, donc rien ne change dans les sorties.

Vérification : exécuter le prompt seul, sans aucun autre message. S'il ne produit rien et pose des questions en retour, la matière manque. S'il produit quelque chose, c'est qu'il l'a inventée.

### 2- Des phrases creuses

Cause : le prompt commande des effets, pas un texte. « engageant », « percutant », « impactant », « viral » décrivent ce qu'on espère du lecteur, jamais le texte lui-même. Un modèle ne sait pas les mesurer, donc il imite ce qui leur ressemble dans ce qu'il a le plus vu : accroche choc, lignes courtes, formules à citer.

« Viral » est un cas à part : c'est un résultat qui se produit chez le lecteur. Aucun prompt ne peut le commander, seulement l'imiter.

Vérification : retirer chaque adjectif du prompt et se demander ce que le modèle perd. S'il ne perd rien de vérifiable, l'adjectif ne servait à rien.

### 3- Des caractères interdits qui reviennent quand même

Trois causes, par ordre de poids.

a- Le reste du prompt commande le style dont ces caractères font partie. « renommée mondiale », « percutant », « viral » appellent un registre où emojis, flèches et hashtags sont le format normal. Quatre mots d'interdiction contre quatre mots qui appellent l'inverse : le style gagne, et de plus en plus à mesure que le texte s'allonge.

b- L'interdiction est négative et isolée. « Pas d'emojis » demande de retenir quelque chose pendant toute la génération. Une consigne positive donne quelque chose à faire à la place : « texte brut uniquement, aucun caractère décoratif », placée dans son propre bloc, tient.

c- L'historique et la mémoire. Si la conversation ou la mémoire de l'outil contient d'anciennes sorties avec ces caractères, ces exemples pèsent plus lourd qu'une ligne d'interdiction. Ouvrir une conversation neuve.

Constat mesuré sur le cas type : aucun emoji littéral n'est revenu, mais 3 flèches et 5 hashtags. L'interdiction n'avait tenu que sur le mot écrit, pas sur la famille de caractères qu'elle visait. C'est le même mécanisme, et c'est ce qui explique pourquoi élargir la liste ne suffit pas : il faut nommer la famille et donner la consigne en positif.

## Grille d'audit en 7 points

| Point | Question | Défaut typique |
|---|---|---|
| 1 | Le sujet est-il écrit dans le prompt ? | « sur mon sujet », variable jamais remplie |
| 2 | Le lecteur visé est-il décrit en une ligne ? | absent, donc le texte ne parle à personne |
| 3 | Y a-t-il une matière vécue à raconter ? | absente, donc le modèle comble avec des généralités |
| 4 | Les consignes décrivent-elles le texte ou l'effet espéré ? | adjectifs d'effet, non mesurables |
| 5 | Le format est-il donné en positif, dans son propre bloc ? | une interdiction négative isolée en fin de prompt |
| 6 | L'invention est-elle explicitement bloquée ? | rien, donc chiffres et témoignages inventés |
| 7 | Le prompt se relit-il avant de rendre ? | aucune relecture, donc aucun garde-fou |

## Le gabarit, en 4 blocs

Les crochets sont la seule partie qui change d'une sortie à l'autre : c'est ce qui règle les sorties qui se ressemblent.

    CONTEXTE
    [MÉTIER, une ligne]
    Lecteur : [QUI, une ligne]
    Une seule idée : [SUJET]
    Mon cas réel : [À REMPLACER : qui, quand, la phrase entendue, ce qui a changé]
    Ce qu'il doit faire après : [ACTION]

    LE POST
    [LONGUEUR] caractères, en français, à la première personne.
    Mon cas réel en 3 à 5 phrases concrètes, puis l'idée à retenir,
    puis [ACTION] en une phrase affirmative.

    FORMAT
    Texte brut. Aucun emoji, pictogramme, flèche, puce, astérisque, hashtag,
    ni question. Paragraphes de 1 à 3 lignes.

    STYLE
    Phrases courtes, mots de tous les jours, ton calme.
    Bannis : [LISTE DE MOTS].
    N'invente rien : aucun chiffre, citation ou exemple que je ne t'ai pas donné.
    S'il te manque un détail, écris [À COMPLÉTER].

    Relis FORMAT, corrige, rends le post seul.

Quatre règles portent le gabarit :

-> le crochet « cas réel » reste celui de la personne. Un modèle ne peut pas inventer ce qui a été vécu, et s'il le fait, l'auteur signe un faux témoignage sous son nom

-> la ligne « n'invente rien... écris [À COMPLÉTER] » transforme un manque en trou visible au lieu d'un détail fabriqué

-> le bloc FORMAT nomme la famille de caractères, pas un seul exemple, et il est formulé en positif avant de lister ce qui est exclu

-> la dernière ligne fait relire le bloc FORMAT. C'est le seul endroit où le modèle repasse sur sa propre sortie

## Protocole de preuve

Un diagnostic de prompt ne s'affirme pas, il se mesure. Trois exécutions, contextes neufs, sans historique :

-> 1- prompt d'origine seul, mot pour mot

-> 2- prompt d'origine précédé de la seule ligne de présentation du métier, qui reproduit la situation réelle de l'utilisatrice

-> 3- prompt corrigé, crochets remplis

Mesures sur chaque sortie : nombre de caractères, de hashtags, de questions, de points de code décoratifs. Comptage avec `LC_ALL=C.UTF-8 wc -m` : sans locale UTF-8, `wc -m` compte les octets et surévalue un texte français d'environ 2 %, ce qui déclenche de fausses alertes de dépassement.

Résultat sur le cas type :

| | Prompt d'origine | Prompt corrigé |
|---|---|---|
| Post produit | aucun à la 1re exécution, 1884 caractères à la 2e | 964 caractères |
| Flèches et pictogrammes | 3 | 0 |
| Hashtags | 5 | 0 |
| Questions | 4 | 0 |
| Fait concret vécu | 0 | le cas fourni, en 3 phrases |

## Changelog

-> 1.0.0 -- 2026-10-05 : création (Étape 10 bis du skill, version publique anonymisée du diagnostic).
