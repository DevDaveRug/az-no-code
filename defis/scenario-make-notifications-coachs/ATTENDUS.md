# Grille des attendus -- défi 7 « Mon automatisation déconne » (Camille)

Version : 1.0.0
Date : 2026-10-13
Session : S261013z-ccdd
Compagnon : `/defi-hebdo-alegria` v1.9.0 (Étape 1, Étape 10.0, Étape 10 bis), `MESSAGES-DISCORD.md`, `DIAGNOSTIC.md`

Fichier interne (le mot « défi » y est permis). Ne se poste pas.

## Énoncé (citation exacte, extraits)

Message de la cliente : « Mon automatisation déconne et je n'y comprends rien. Depuis mardi, mes deux coachs reçoivent le même prospect 4 ou 5 fois dans la journée. Hier, un prospect qui avait coché 3 centres d'intérêt a généré 3 mails séparés. Et à l'inverse, les prospects qui arrivent par le formulaire de mon site ne remontent jamais, j'en ai perdu au moins une dizaine. Je vous laisse regarder, moi je suis dépassée. Camille »

Scénario : « 1. Airtable > Watch Records sur la table `Prospects`. Trigger field : `Last modified time` 2. Iterator sur le champ `Centres d'intérêt` (liste à choix multiples) 3. Filtre : `Statut` est égal à `Nouveau` 4. Gmail > Send an email au coach 5. Airtable > Update a record : coche la case `Notifié` »

Précisions : « Le formulaire de son site crée les prospects sans remplir le champ Statut » ; « Un prospect peut cocher plusieurs centres d'intérêt ».

Ce qu'il faut faire : « Trouver les 3 problèmes de ce scénario ; Expliquer la cause de chacun, avec des mots que Camille comprendrait ; Proposer la correction, module par module ». « Envoie tes explications ici, par message (pas de PDF ou de document !) ». « 15 points de participation + 15 points de réussite ». « Ouvert jusqu'au 16/10/2026 à 17h00. Poste ta réponse dans ce fil pour participer. »

## Grille

| Attendu (citation exacte) | Points | Où c'est rendu | Statut |
|---|---|---|---|
| « Trouver les 3 problèmes de ce scénario » | - | Message 1/2 : Problème 1, 2, 3 en intertitres | fait |
| « Expliquer la cause de chacun, avec des mots que Camille comprendrait » | - | Message 1/2 : une ligne « Cause : » par problème, images simples (« ton prospect est devenu trois prospects », « personne ne la lit ») | fait |
| « Proposer la correction, module par module » | - | Message 2/2 : Module 1 à 5 en intertitres, plus la source (Statut par défaut) et le rattrapage | fait |
| « mes deux coachs reçoivent le même prospect 4 ou 5 fois dans la journée » | - | Problème 1 (boucle Last modified time + module 5) -> Module 1 + Module 3 | fait |
| « un prospect qui avait coché 3 centres d'intérêt a généré 3 mails séparés » | - | Problème 2 (Iterator) -> Module 2 supprimé + Module 4 join() | fait |
| « les prospects qui arrivent par le formulaire de mon site ne remontent jamais » | - | Problème 3 (filtre Statut) -> Module 3 + Statut par défaut | fait |
| « j'en ai perdu au moins une dizaine » | - | Fin du message 1/2 (ils sont toujours dans Airtable) + « Ta dizaine de prospects perdus » au message 2/2 | fait |
| « Je vous laisse regarder, moi je suis dépassée » | - | 1re ligne : « rassure-toi : ton scénario n'est pas à refaire, trois réglages suffisent » | fait |
| « par message (pas de PDF ou de document !) » | - | 2 messages texte, 1548 et 1736 caractères (`LC_ALL=C.UTF-8 wc -m` moins le saut de ligne final) | fait |
| « 15 points de participation + 15 points de réussite » | 30 | Les 3 consignes rendues dans l'ordre demandé | fait |
| Indice | - | Les 2 précisions de Camille sont exploitées (Statut vide -> Problème 3 ; plusieurs centres -> Problème 2) | fait |
| « Ouvert jusqu'au 16/10/2026 à 17h00. Poste ta réponse dans ce fil » | - | À poster par David dans le fil du salon `défi-hebdomadaire` | à poster (David) |

## Écarts assumés

-> Comportement Make vérifié sur la documentation et la communauté Make (le module Watch Records exige un champ déclencheur de type Date de création ou Date de dernière modification), pas sur un scénario réellement exécuté : aucun compte Make n'est branché à cette session.

-> « Airtable n'envoie ni une case décochée ni un champ vide » : comportement connu de l'API Airtable (champs vides omis), non rejoué sur la base de Camille. D'où l'opérateur « n'existe pas » recommandé.

-> Valeur par défaut du champ Statut : non affirmé qu'elle s'applique aux fiches créées par le formulaire du site (on ne sait pas s'il s'agit d'un formulaire Airtable ou d'un formulaire externe qui écrit via l'API). La réponse demande un test avec un faux prospect et garde le filtre tolérant au Statut vide.

-> « Depuis mardi » : la cause du changement à cette date (ajout du module 5, changement du champ déclencheur) n'est pas connue ; la réponse ne l'invente pas.

-> Les remarques destinées aux autres participants ne sont pas archivées ici (pseudonymes de membres dans un repo public).

## Auto-check Étape 11 (skill v1.9.0)

| Point | État |
|---|---|
| `ATTENDUS.md`, chaque attendu avec son emplacement et son statut | fait |
| Message 1 étiqueté, répond à la question de la cliente dès la 1re ligne | fait |
| Messages sous 2000 caractères, comptés en UTF-8 | fait (1548 et 1736) |
| `DIAGNOSTIC.md` anonymisé | fait |
| 2 `INTERFACE.md`, 6 liens | sans objet : l'énoncé ne demande aucun outil (Étape 10 bis) |
| Mots interdits en mot entier dans les messages et `DIAGNOSTIC.md` | fait (aucune occurrence) |
| Noms de salons réels | fait (`défi-hebdomadaire`) |
