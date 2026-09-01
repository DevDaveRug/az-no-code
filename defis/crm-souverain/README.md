# Défi crm-souverain (CRM Prospects avec relance 7 jours)

## Énoncé

Construire un CRM prospects simple, capable de :

-> lister tous les prospects avec leur statut

-> afficher une vue "à relancer" (prospects dont la dernière relance date de plus de 7 jours et qui ne sont pas gagnés/perdus)

-> permettre d'ajouter un prospect via un formulaire

-> afficher un calendrier des relances à venir

-> envoyer chaque lundi matin un email récapitulatif des prospects à relancer

## Client

Eva PRO (formation Alegria)

## Livraison

Vendredi 5 septembre 2026

## Livrables

### No-code

-> `airtable/SPECS.md` : structure complète Airtable prête à recréer

-> `airtable/schema.json` : export prêt à importer via API Airtable

-> `airtable/exemples.md` : 4 lignes couvrant les 4 statuts + 1 dans "à relancer"

-> `nocodb/SPECS.md` : équivalent souverain sur Coolify (sb-nocodb ou dédié)

-> `nocodb/exemples.md` : mêmes exemples

-> `captures/` : captures d'écran une fois la base créée (David remplit)

### Code (dans DevDaveRug/az-code)

-> `defis/crm-souverain/` : MVP Next.js exécutable + déployable Vercel

## Convention exemples

Tous les noms, entreprises, emails, téléphones sont fictifs.

Prénoms : Alice, Bob, Chloé, Emma (une par statut : Nouveau / En cours / Gagné / Perdu).

## Étapes David pour livrer à Alegria

1. Ouvrir `airtable/SPECS.md` et suivre pour créer la base Airtable

2. Ajouter les 4 lignes de `airtable/exemples.md`

3. Vérifier que la vue "à relancer" contient bien les prospects attendus

4. Configurer l'automatisation email récap lundi 9h

5. Prendre les captures d'écran (base + vue "à relancer" + formulaire + calendrier + automatisation) dans `captures/`

6. (Optionnel, pour bonus) Créer aussi la version NocoDB depuis `nocodb/SPECS.md`

7. (Optionnel, pour bonus) Ouvrir le lien vivant Next.js (URL Vercel) déployé depuis `az-code/defis/crm-souverain/`

## État

-> specs prêtes : oui

-> exemples anonymisés : oui

-> captures : en attente

-> version NocoDB : specs prêtes

-> version code : MVP Next.js prêt à déployer (guide dans az-code)
