# Messages Discord -- défi 3 « Inscriptions masterclass » + rattrapage portfolio

Version : 1.0.0
Date : 2026-10-27
Session : S261019z-ccweb
Compagnon : `interfaces/SC_PROSPECTS_TABLE_UNIQUE.md` §6 (5 liens de vues partagées), `/defi-hebdo-alegria` v1.8.1 (Étape 10, archive des messages)

Ce message fusionne deux besoins (décision S261006z-J + IDEE_infra_196 étape 8) : le rendu rétroactif du défi 3 (jamais posté au salon Défis lors de sa livraison le 18/09, faute de NocoDB déployé à l'époque) et le rafraîchissement des 5 liens Airtable du portfolio, avec avertissement d'obsolescence du partage NocoDB base complète `22ca37d4`.

## Message : salon défi-hebdomadaire

Statut : posté le 27/10/2026 avec une erreur de format d'URL sur les 5 liens Airtable (manquait le préfixe de la base, `https://airtable.com/shr...` au lieu de `https://airtable.com/appTqLo3JDg7d1fak/shr...` -- Airtable répondait « Ce lien de partage privé n'est plus disponible »). **Corrigé le jour même en éditant le message dans Discord** (pas de nouveau message). Un paragraphe de contexte a été ajouté à cette occasion pour que les nouveaux membres du serveur comprennent le post sans avoir suivi les semaines précédentes.

Petit rattrapage (pas un nouveau défi) : les projets de démo du portfolio bougent souvent en coulisses -- nouvelle base, nouveau partage -- donc leurs liens publics changent sans prévenir. Ce post centralise les 5 liens à jour, et fait entrer le défi 3 (masterclass) qui n'avait pas encore eu son tour ici.

Si tu découvres ce serveur : chaque semaine un client fictif pose un problème business réel, et je le résous en live sous 3 formes différentes -- pourquoi, juste en dessous.

Pourquoi toujours 3 versions du même outil ? Parce que la bonne stack dépend du client, pas de mes goûts :

-> **Airtable** -- le plus rapide à assembler (15-30 min), parfait pour montrer vite un résultat concret ou pour un client qui veut juste tester avant de s'engager. Contrepartie : il est locataire du produit, il paye par utilisateur, et sa donnée vit chez Airtable.

-> **NocoDB souverain** -- sur mon serveur, quota illimité, le client est propriétaire de sa donnée sans payer de licence par poste. Bon compromis pour qui veut sortir de la dépendance à un SaaS sans financer un développement sur mesure.

-> **Code Next.js** -- à sa marque, hébergé chez lui, aucune limite, la logique métier exacte qu'il veut. Le plus long à construire, le plus solide dans la durée.

Je choisis en fonction de ce que le client cherche réellement : vitesse de mise en route, indépendance vis-à-vis des SaaS, ou outil sur mesure qui lui appartient vraiment.

**Défi 3 -- Inscriptions masterclass + email de confirmation auto**

L'organisateur qui passait 1h/jour à envoyer les confirmations à la main récupère cette heure : un formulaire, une automatisation, le mail part tout seul avec le prénom et la date.

Airtable
-> Formulaire : https://airtable.com/appTqLo3JDg7d1fak/pagI3FYwUrzpkz2MO/form
-> Table des inscrits : https://airtable.com/appTqLo3JDg7d1fak/shrPkZvY8ePkGIKTj

NocoDB souverain
-> Formulaire : https://sb-nocodb.coolify.salescloser.fr/#/nc/form/e3eb2619-338b-4451-9c9e-57cd07ed13e8

Code Next.js
-> https://masterclass-inscriptions.demo.salescloser.fr

**Les liens Airtable des 5 projets, à jour**
-> Projet 1 (CRM souverain) : https://airtable.com/appTqLo3JDg7d1fak/shr0rBkW1tzUUP8iM
-> Projet 2 (CR de RDV) : https://airtable.com/appTqLo3JDg7d1fak/shrBANd0o3QrTlOMr
-> Projet 3 (masterclass) : https://airtable.com/appTqLo3JDg7d1fak/shrPkZvY8ePkGIKTj
-> Projet 4 (demandes urgentes) : https://airtable.com/appTqLo3JDg7d1fak/shr4i0jd3dnKl1gbz
-> Projet 5 (missions et CA) : https://airtable.com/appTqLo3JDg7d1fak/shrACptSkNeVSZWxr

Si tu avais gardé un ancien lien vers la base NocoDB complète (il commençait par `22ca37d4`), il va cesser de fonctionner dans les prochains jours -- il n'était pas pensé pour être partagé tel quel. Les liens ci-dessus restent valables.

## Changelog

-> 1.0.0 -- 2026-10-27 (S261019z-ccweb) : création, message posté puis corrigé le jour même (format d'URL des 5 liens Airtable).
