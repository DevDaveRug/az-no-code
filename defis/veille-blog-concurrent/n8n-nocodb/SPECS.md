# Version souveraine : n8n + NocoDB (veille multi-sources)

Construite le 13/11/2026, après le rendu du défi, pour la veille de David. Même besoin que Claire, avec la limite de la version Make levée : **ajouter un site = ajouter une ligne dans un tableau**, sans toucher au workflow.

## Tables NocoDB (base `Sales Closer Souverain`, `phwalskbrftv4o4`)

-> `AZ_VeilleSources` (`mhc7qvy9wji26ef`) : Nom, Flux RSS (URL), Actif (case à cocher), Usage (à quoi sert la source). Une ligne par site suivi. Décocher Actif suspend un site sans le supprimer.

-> `AZ_VeilleArticles` (`mwd0co1ih7zoft3`) : Titre, Date de publication, Lien, Source. Une ligne par article.

## Workflow n8n `AZ_Veille_RSS vers NocoDB v1.0.0` (id `9qpzVRdgJMq7vx3M`, actif)

-> **Déclencheurs** : toutes les heures (fuseau Europe/Paris), et un lancement manuel par webhook (adresse gardée hors du dépôt).

-> **Étapes** : liens déjà connus (1 lecture NocoDB) -> sources actives -> une ligne par source -> lecture du flux RSS de chaque source (une source en erreur n'arrête pas les autres) -> garder seulement les liens inconnus -> une ligne ajoutée par article.

-> **Pas de doublon** : le lien de l'article sert d'identifiant. Vérifié le 13/11 : 1er passage 10 articles ajoutés, 2e passage 0.

-> Export du workflow : `AZ_Veille_RSS-vers-NocoDB.workflow.json` (le jeton du webhook y est remplacé par `<JETON>`).

## Ajouter une source

Ouvrir `AZ_VeilleSources`, ajouter une ligne : nom, adresse du flux RSS, Actif coché. Au passage suivant (moins d'une heure), ses articles arrivent dans `AZ_VeilleArticles`.

## Make contre n8n, pour présenter les deux

-> **Make + Airtable** : le plus rapide à monter pour une personne seule, sans serveur. Un module RSS par site : ajouter un site, c'est modifier le scénario. Les opérations consommées sont comptées par l'abonnement Make.

-> **n8n + NocoDB** : hébergé chez nous (Coolify), aucune limite d'opérations, les sources se gèrent dans un tableau. Demande un serveur déjà en place.
