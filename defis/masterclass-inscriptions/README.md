# Défi masterclass-inscriptions (Inscriptions masterclass + email de confirmation auto)

## Énoncé

Un client organise une masterclass le mois prochain. Il note ses inscrits dans un tableau et envoie les mails de confirmation un par un, à la main -- 1h par jour. Il veut un système qui :

-> stocke chaque inscrit (prénom, email, date de la masterclass)

-> envoie automatiquement un email de confirmation personnalisé dès qu'un inscrit est ajouté, reprenant le prénom + la date

## Client

Eva PRO (formation Alegria, défi hebdo n°3, semaine 38)

## Livraison

Vendredi 19 septembre 2026

## Livrables

### No-code

-> `airtable/SPECS.md` : structure complète Airtable + automatisation email (recette pas à pas)

-> `airtable/schema.json` : export prêt à importer via API Airtable

-> `airtable/exemples.md` : 5 inscrits anonymisés (dont David lui-même comme test explicite)

-> `nocodb/SPECS.md` : équivalent souverain (NocoDB self-host + n8n pour l'email)

-> `nocodb/exemples.md` : mêmes exemples

-> `captures/` : captures d'écran une fois la base créée (David remplit)

### Code (dans DevDaveRug/az-code)

-> `defis/masterclass-inscriptions/` : MVP Next.js déployable Vercel (Prisma + Neon + Resend)

## Convention exemples

Tous les noms, entreprises, emails, téléphones sont fictifs (domaines `.example` RFC 2606).

Une exception explicite : le test "ajoute-toi toi-même" est représenté par un inscrit `David Ruggieri` avec un email `david@salescloser.fr` -- c'est ce qui est demandé dans l'énoncé du défi.

## Étapes David pour livrer à Alegria

1. Ouvrir `airtable/SPECS.md` et créer la base Airtable (15 min : 1 table + 1 form + 1 automatisation)

2. Configurer l'automatisation `When record created -> Send email` (les 2 étapes sont dans SPECS.md)

3. Ajouter les 5 lignes de `airtable/exemples.md` OU passer par le formulaire pour tester "ajout auto = email envoyé"

4. Prendre les captures d'écran (base + formulaire + automatisation + preuve email reçu) dans `captures/`

5. Répondre à Eva sur Discord avec les captures

6. (Optionnel, bonus démo) : ouvrir le lien vivant Next.js déployé depuis `az-code/defis/masterclass-inscriptions/`

## Angle commercial

-> Airtable = outil de démo Alegria (livrable rapide, screenshots ok)

-> NocoDB + n8n = argument souverain "je peux le refaire chez toi, tu ne dépends pas d'Airtable"

-> Next.js code = argument "tu veux ton propre outil branded, hosté chez toi, avec la logique métier que tu veux"

## État

-> specs prêtes : oui

-> exemples anonymisés : oui (+1 ligne David explicite pour le test)

-> captures : en attente

-> version NocoDB : specs prêtes

-> version code : MVP Next.js prêt à déployer (guide dans az-code)
