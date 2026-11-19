# CRM d'Eric Dj (template Airtable) : structure relevée et comparaison avec SC_Prospects

Version : 1.0.0
Date : 2026-11-19
Session : S261117z-ccdd

## Source

Template Airtable « SOLO AI MACHINE · CRM IA (template) », Bonus n°4 de la formation d'Eric Dj (leçon Skool de la semaine 1, section Ressources).

Lien de partage (lecture seule, bouton « Copier la base » à côté du titre) : https://airtable.com/appF4qp7ebSV5UBXF/shrueA6PQb4ymiSBz

Lien transmis par la session f (David l'avait collé dans cette session le 18/11). Décision de David du 18/11, notée dans la session f : garder le CRM LIA'M (`/prospects`) plutôt que dupliquer ce template. Décision du 19/11 dans la session z : examiner sans dupliquer, pour comparer à `SC_Prospects`.

## Structure relevée (19/11, vue partagée, sans copie)

Trois tables, une vue « Grid view » chacune, 3 prospects d'exemple, 1 appel, 2 tâches.

### Prospects (17 champs)

| Champ | Nature relevée |
|---|---|
| Full Name | champ principal, texte |
| Company Name | texte |
| Email Address | e-mail |
| Téléphone | téléphone |
| Type | sélection (valeur vue : Prospect) |
| Action | sélection (Relancer, Répondre, Rien à faire) |
| Statut | sélection (vide dans les exemples) |
| Source | sélection (Lead magnet, LinkedIn, Recommandation) |
| Notes | texte long |
| Enveloppe Potentielle (€) | monnaie, cumul affiché en pied de colonne |
| Last email sent | date |
| Last email received | date |
| Ressources | non renseigné dans les exemples |
| Calls | lien vers la table Calls |
| Follow Up Tasks | lien vers la table Follow Up Tasks |
| Call Notes Summary (AI) | champ IA |
| Next Best Action (AI) | champ IA |

### Calls (10 champs)

Call Time (principal), Call Date, Call Outcome (sélection, ex. Intéressé), Notes, Lien transcript, email, Prospect (lien), Follow Up Tasks (lien), Call Summary (AI), Next Steps Suggestion (AI).

### Follow Up Tasks (8 champs vus)

Task Name (principal), Description, Due Date, Status (À faire), Priority (Haute, Moyenne), Completion Notes, Related Prospect (lien), Related Call (lien).

Les 4 champs IA (Call Notes Summary, Next Best Action, Call Summary, Next Steps Suggestion) consomment des crédits IA Airtable (information de la session f, non vérifiée ici : la vue partagée ne montre pas la configuration des champs).

## Comparaison avec `SC_Prospects` (spec `defis/crm-souverain/airtable/SPECS.md`)

| Besoin | SC_Prospects | Template d'Eric Dj |
|---|---|---|
| Personnes (prospects et clients) | une table, `Statut` Nouveau / En cours / Gagné / Perdu | une table, `Type` + `Statut` |
| Identité | Prénom, Nom, Entreprise, E-mail, Téléphone | Full Name, Company Name, Email, Téléphone |
| Origine | `Source` (LinkedIn, Recommandation, Site, Autre) | `Source` (Lead magnet, LinkedIn, Recommandation) |
| Relance | `Dernier contact`, `Prochaine relance` calculée, `Jours restants`, vue « À relancer » | `Action` choisie à la main, `Last email sent` / `received` |
| Valeur | `Montant potentiel` | `Enveloppe Potentielle (€)` |
| Appels et CR de RDV | table `SC_CRs_de_RDV` | table `Calls` (résultat, transcript, résumé IA) |
| Tâches de suivi | aucune table dédiée (relance calculée) | table `Follow Up Tasks` (priorité, échéance, appel lié) |
| IA intégrée | aucune | 4 champs IA (résumés et prochaine action) |
| Vues | Grille, À relancer, Calendrier, Kanban Pipeline | une grille par table |

## Ce qui est à reprendre, sans dupliquer

-> La table `Follow Up Tasks` (priorité, échéance, lien vers le prospect et l'appel) : elle manque à `SC_Prospects` et sert à montrer un suivi dans un défi.

-> Le champ `Action` (Relancer, Répondre, Rien à faire) comme lecture rapide du « et maintenant ? ».

-> Le principe « un appel = une ligne liée au prospect », déjà couvert par `SC_CRs_de_RDV`.

## Ce qu'on ne reprend pas

-> Les champs IA : dépendance aux crédits Airtable, pas souverain. L'équivalent souverain (n8n + modèle) se décide dans la version Souveraine.

-> La séparation `Type` / `Statut` : `SC_Prospects` garde un seul `Statut` (un client = un prospect au statut Gagné, règle `/defi-hebdo-alegria`).

## Suite prévue (à valider par David)

1- CRM dédié aux défis Airtable : base ou vue construite sur `SC_Prospects`, enrichie de `Follow Up Tasks`.

2- CRM vide et copiable pour clients et prospects : même structure, sans données.

3- Adapter les défis précédents, puis les versions Souveraine (NocoDB, n8n) et Code (Next.js), pour que les 3 formats soient alignés d'ici la fin de la semaine du 23/11.

## Mise en place NocoDB (19/11, S261117z-ccdd)

Nom retenu par David : `SC_Suivis` (préfixe `SC_` pour les tables qui rapportent de l'argent, `AZ_` pour les autres).

Airtable : API refusée (`PUBLIC_API_BILLING_LIMIT_EXCEEDED`, quota mensuel atteint). La même table et le même champ restent à créer dans Airtable, à la main dans l'interface ou après le renouvellement du quota.

| Objet | Avant | Après | Retour arrière |
|---|---|---|---|
| NocoDB, table `SC_Suivis` (`mjxhpkhjhqotsbg`) : Tache, Description, Echeance, Statut (A faire, En cours, Fait), Priorite (Haute, Moyenne, Basse), NotesCloture, liens vers `SC_Prospects` et `SC_CRs_de_RDV` | absente | créée | `DELETE /api/v2/meta/tables/mjxhpkhjhqotsbg` |
| NocoDB, `SC_Prospects`, champ `Action` (`cbhn2i3z597g89w`) : Relancer, Repondre, Rien a faire | absent | créé | `DELETE /api/v2/meta/columns/cbhn2i3z597g89w` |

Trace de la création (jeton dans `$TOKEN`, en-tête `xc-token`) :

```
curl -X POST -H "xc-token: $TOKEN" -H "Content-Type: application/json" https://sb-nocodb.coolify.salescloser.fr/api/v2/meta/bases/phwalskbrftv4o4/tables -d '{"table_name":"SC_Suivis","title":"SC_Suivis","columns":[...6 colonnes...]}'
curl -X POST ... /api/v2/meta/tables/mjxhpkhjhqotsbg/columns -d '{"title":"Prospect","uidt":"LinkToAnotherRecord","parentId":"mjxhpkhjhqotsbg","childId":"m163jpgx02zhgnc","type":"bt"}'
curl -X POST ... /api/v2/meta/tables/mjxhpkhjhqotsbg/columns -d '{"title":"RDV","uidt":"LinkToAnotherRecord","parentId":"mjxhpkhjhqotsbg","childId":"mkhq6zt5pv5gz0v","type":"bt"}'
curl -X POST ... /api/v2/meta/tables/m163jpgx02zhgnc/columns -d '{"title":"Action","uidt":"SingleSelect","dtxp":"'Relancer','Repondre','Rien a faire'"}'
```

Relu par l'API : les 6 champs et `Action` existent. Les deux liens portent les noms `SC_Prospects` et `SC_CRs_de_RDVs` (NocoDB a ignoré le titre `Prospect` et `RDV`) : à renommer dans l'interface si besoin.
