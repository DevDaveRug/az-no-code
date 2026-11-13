# Version souveraine : n8n + NocoDB (veille multi-sources)

Construite le 13/11/2026, après le rendu du défi, pour la veille de David. Même besoin que Claire, avec la limite de la version Make levée : **ajouter un site = ajouter une ligne dans un tableau**, sans toucher au workflow.

## Tables NocoDB (base `Sales Closer Souverain`, `phwalskbrftv4o4`)

-> `AZ_VeilleSources` (`mhc7qvy9wji26ef`) : Nom, Flux RSS (URL), Actif (case à cocher), Usage (à quoi sert la source). Une ligne par site suivi. Décocher Actif suspend un site sans le supprimer.

-> `AZ_VeilleArticles` (`mwd0co1ih7zoft3`) : Titre, Date de publication, Lien, Source. Une ligne par article. Depuis le 13/11, colonnes de travail demandées par la session d (publications) : Thème (coûts, confidentialité, outils, métier, règles), Pilier (COULISSES, PRODUIT, METHODE, RESEAU), Résumé, Statut (à lire, retenu, écarté, utilisé ; « à lire » posé par le workflow), Utilisé dans (code de la publication), Fait vécu, Date de lecture. Seule la session z modifie la structure de cette table.

-> Sources au 13/11 : lanutrition.fr (démo) + 9 sources « Publications » ajoutées par la session d (CNIL, Next, Le Monde Pixels, Journal du Net, Korben, Blog n8n, Mistral AI, OpenAI, ZDNet France).

-> **Archives** (Val David 13/11) : les articles publiés avant le 14/10/2026, importés d'un coup au premier relevé des 9 sources, sont en statut « écarté ». Rien n'est supprimé : c'est une réserve de contenu que la session d relit quand elle cherche de la matière, filtre Statut = écarté.

## Workflow n8n `AZ_Veille_RSS vers NocoDB v1.2.1` (id `9qpzVRdgJMq7vx3M`, actif)

-> **Déclencheurs** : toutes les heures (fuseau Europe/Paris), et un lancement manuel par webhook (adresse gardée hors du dépôt).

-> **Étapes** : liens déjà connus (1 lecture NocoDB) -> sources actives -> une ligne par source -> lecture du flux RSS de chaque source (une source en erreur n'arrête pas les autres) -> garder seulement les liens inconnus -> une ligne ajoutée par article.

-> **Pas de doublon** : le lien de l'article sert d'identifiant. Seuls les articles publiés depuis moins de 30 jours sont retenus, et les liens connus sont lus parmi les articles publiés depuis moins de 40 jours (une lecture NocoDB plafonne à 1000 lignes). Vérifié le 13/11 : passage suivant, 0 ligne ajoutée.

-> **Incident du 13/11, corrigé dans la v1.2.1** : avec 9 sources de plus, certains flux renvoyant toutes leurs archives (OpenAI : 720 articles), la table a dépassé 1000 lignes. La v1.0.0 lisait les liens connus dans les 1000 premières lignes seulement : 274 doublons créés en un passage. Les 274 doublons ont été supprimés (la ligne la plus ancienne de chaque lien est gardée), et les deux règles ci-dessus empêchent la récidive.

-> Export du workflow : `AZ_Veille_RSS-vers-NocoDB.workflow.json` (le jeton du webhook y est remplacé par `<JETON>`).

## Ajouter une source

Ouvrir `AZ_VeilleSources`, ajouter une ligne : nom, adresse du flux RSS, Actif coché. Au passage suivant (moins d'une heure), ses articles arrivent dans `AZ_VeilleArticles`.

## Make contre n8n, pour présenter les deux

-> **Make + Airtable** : le plus rapide à monter pour une personne seule, sans serveur. Un module RSS par site : ajouter un site, c'est modifier le scénario. Les opérations consommées sont comptées par l'abonnement Make.

-> **n8n + NocoDB** : hébergé chez nous (Coolify), aucune limite d'opérations, les sources se gèrent dans un tableau. Demande un serveur déjà en place.
