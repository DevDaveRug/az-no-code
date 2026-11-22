# CRM libre (vide), version Souveraine : NocoDB

Version : 1.0.0
Date : 2026-11-21
Session : S261117z-ccdd

Équivalent NocoDB de `SC_CRM_LIBRE_VIDE.md` (Airtable). Mêmes 3 tables, même logique, sans quota ni abonnement : les données restent sur notre instance (`sb-nocodb.coolify.salescloser.fr`).

## Base

Base NocoDB `CRM libre (vide)` (identifiant `ponfsa5y2to4x2g`), séparée de `Sales Closer Souverain`. Aucune ligne de données.

Lien public en lecture seule, bouton « Copy Base » vérifié le 21/11 (la page s'ouvre, tables vides) :
https://sb-nocodb.coolify.salescloser.fr/#/base/368cd035-0ba0-43b7-81dc-9681a5ae34e2

## Les 3 tables

Schéma exact exporté dans `crm-libre-nocodb/` (`mk23g6rwi4lxz19.json` SC_Prospects, `mi33pnze6zoydd0.json` SC_RDV, `mshd6i3ybu8vxmr.json` SC_Suivis).

| Table | Champs |
|---|---|
| SC_Prospects | Prenom (champ principal), Nom, Entreprise, Email, Telephone, Statut (Nouveau, En cours, Gagne, Perdu), Action (Relancer, Repondre, Rien a faire), Source (LinkedIn, Recommandation, Site, Autre), DateEntree, DernierContact, MontantPotentiel, Notes, NomComplet (formule), ProchaineRelance (formule : DernierContact + 7 jours), liens RDV et Suivis |
| SC_RDV | Titre, DateRDV, Contexte (Decouverte, Suivi, Closing), NotesBrutes, CompteRendu, Statut (A traiter, Traite), lien Prospect (un seul), lien Suivis |
| SC_Suivis | Tache, Description, Echeance, Statut (A faire, En cours, Fait), Priorite (Haute, Moyenne, Basse), NotesCloture, lien Prospect (un seul), lien RDV (un seul) |

Différences avec Airtable (à connaître) :

-> `ProchaineRelance` ajoute toujours 7 jours à `DernierContact`, sans condition sur le Statut : NocoDB a refusé la formule conditionnelle (la création a répondu sans erreur mais sans créer le champ). Le tri sur le statut se fait dans une vue (filtre Statut).

-> Aucun champ IA, comme dans la version Airtable.

-> `Prenom` et `Nom` restent visibles dans NocoDB (le champ principal est `Prenom`). Dans la version Airtable, David les a masqués au profit de `Nom complet`.

-> Vues créées par l'API le 22/11 (voir « Vues » plus bas) : `SC_Prospects` « A relancer » (Statut Nouveau ou En cours, ProchaineRelance jusqu'à aujourd'hui, tri croissant) et « Pipeline » (Kanban par Statut) ; `SC_Suivis` « Par echeance » (Statut différent de Fait, tri par Echeance) et « Par statut » (Kanban). Comportement vérifié avec 3 lignes temporaires, supprimées ensuite : seul l'ancien contact au statut Nouveau apparaît dans « A relancer ».

## Trace et retour arrière (19/11 au 21/11, API NocoDB, jeton dans `$TOKEN`, en-tête `xc-token`)

| Objet | Retour arrière |
|---|---|
| Base `CRM libre (vide)` (`ponfsa5y2to4x2g`) et ses 3 tables (`mk23g6rwi4lxz19`, `mi33pnze6zoydd0`, `mshd6i3ybu8vxmr`) | `DELETE /api/v2/meta/bases/ponfsa5y2to4x2g` |
| Partage public de la base (uuid `368cd035-0ba0-43b7-81dc-9681a5ae34e2`) | `DELETE /api/v2/meta/bases/ponfsa5y2to4x2g/shared` |

Création des tables : `POST /api/v2/meta/bases/ponfsa5y2to4x2g/tables` avec le corps de colonnes ; liens : `POST /api/v2/meta/tables/<id>/columns` avec `type: "hm"` posé sur la table référencée (SC_Prospects, SC_RDV) : NocoDB crée alors le lien « belongs to » sur la table enfant.

## Correction dans la base `Sales Closer Souverain` (même session)

Les liens de `SC_Suivis` posés le 19/11 avec `type: "bt"` étaient à l'envers (un prospect aurait pointé vers un seul suivi, un RDV vers un seul suivi). Corrigé le 21/11 : colonnes inversées supprimées, liens recréés avec `type: "hm"` sur `SC_Prospects` (`cqcq355wcdk8m3u`) et `SC_CRs_de_RDV` (`cvsoqtp48vkm34x`), côté `SC_Suivis` les liens `Prospect` (`cof8tkb554iabg7`) et `RDV` (`cm41aup62tdoazl`) sont en « belongs to ». Retour arrière : `DELETE /api/v2/meta/columns/<id>` de ces colonnes (les anciennes colonnes inversées n'avaient aucune donnée).

## Vues (22/11, API NocoDB)

-> `POST /api/v2/meta/tables/<table>/grids` (A relancer `vw1yye68ff6rdixm`, Par echeance `vwa3hsi0mpg5k6mx`) et `/kanbans` avec `fk_grp_col_id` (Pipeline `vw8obxgkckm7w3ga`, Par statut `vwoctb2tgwj0ye4i`), puis `POST /api/v2/meta/views/<vue>/filters` et `/sorts`.

-> Retour arrière : `DELETE /api/v2/meta/views/<id>` pour chacune des 4 vues.

## Reste à faire

-> Vues dans l'interface NocoDB (A relancer, Pipeline en Kanban, Par echeance).

-> Version Code (Next.js, Prisma, Neon) : même schéma, à faire ensuite.
