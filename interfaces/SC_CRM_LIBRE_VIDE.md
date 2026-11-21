# CRM libre (vide) : base copiable pour clients et prospects

Version : 1.0.0
Date : 2026-11-20
Session : S261117z-ccdd

But : une base Airtable sans aucune donnée personnelle, que n'importe qui copie avec « Copy base » et utilise tout de suite. Ce n'est pas la base `Sales Closer Souverain` (privée, avec les défis) : c'est une base à part, `CRM libre (vide)`.

Compagnon : `SC_CRM_ERIC_DJ_COMPARAISON.md` (d'où viennent `SC_Suivis` et `Action`), `SC_PROSPECTS_TABLE_UNIQUE.md` (règle : une seule table de personnes).

## Les 3 tables

Une seule table de personnes (prospects et clients), un rendez-vous et un suivi toujours reliés à un prospect. Aucun nom ou e-mail recopié ailleurs.

### SC_Prospects

| Champ | Type |
|---|---|
| Nom complet (principal) | Formule : `{Prenom} & " " & {Nom}` |
| Prenom, Nom, Entreprise | Texte une ligne |
| Email | E-mail |
| Telephone | Téléphone |
| Statut | Sélection unique : Nouveau, En cours, Gagne, Perdu |
| Action | Sélection unique : Relancer, Repondre, Rien a faire |
| Source | Sélection unique : LinkedIn, Recommandation, Site, Autre |
| Date entree, Dernier contact | Date |
| Prochaine relance | Formule : `IF(OR({Statut}='Nouveau',{Statut}='En cours'), DATEADD({Dernier contact}, 7, 'days'), BLANK())` |
| Montant potentiel | Monnaie (EUR) |
| Notes | Texte long |
| RDV, Suivis | Liens inverses créés par les deux tables ci-dessous |

### SC_RDV

| Champ | Type |
|---|---|
| Titre (principal) | Texte une ligne |
| Prospect | Lien vers SC_Prospects (une seule valeur), recherche : Nom complet |
| Date RDV | Date |
| Contexte | Sélection unique : Decouverte, Suivi, Closing |
| Notes brutes, Compte rendu | Texte long |
| Statut | Sélection unique : A traiter, Traite |

### SC_Suivis

| Champ | Type |
|---|---|
| Tache (principal) | Texte une ligne |
| Prospect | Lien vers SC_Prospects (une seule valeur), recherche : Nom complet |
| RDV | Lien vers SC_RDV (une seule valeur), recherche : Date RDV |
| Description, Notes cloture | Texte long |
| Echeance | Date |
| Statut | Sélection unique : A faire, En cours, Fait |
| Priorite | Sélection unique : Haute, Moyenne, Basse |

## Vues

-> `SC_Prospects` : « Tous », « A relancer » (Statut Nouveau ou En cours, Prochaine relance passée ou aujourd'hui, tri croissant), « Pipeline » (Kanban groupé par Statut).

-> `SC_Suivis` : « Par echeance » (grille triée par Echeance, Statut différent de Fait), « Par statut » (Kanban).

## Ce que la base ne contient pas

-> Aucun champ IA (pas de crédits Airtable consommés).

-> Aucune ligne de contact réel : une ligne d'exemple par table, marquée « (exemple) », à supprimer.

## Fabrication et partage

1- Créer une base vide `CRM libre (vide)` dans un espace de travail qui n'est pas celui des défis.

2- Importer les 3 fichiers de `crm-libre-csv/` (Add or import, CSV file), un par table, puis régler les types de champs comme ci-dessus : l'import crée des champs texte.

3- Créer les liens `Prospect` et `RDV`, les formules et les vues.

4- Partager : Share, lien de partage en lecture seule, activer « Allow copying » (« Copy base »), tester dans une fenêtre privée.

5- Supprimer les 3 lignes d'exemple avant de partager le lien.

## État

Base construite à la main par David, guidé pas à pas (21/11, S261117z-ccdd), dans l'espace de travail Airtable « CRM libre », base « CRM Libre (vide) ». L'API Airtable étant bloquée par le quota mensuel, la base n'a pas été relue par l'API.

Lien public en lecture seule (bouton « Copier la base » vérifié le 21/11 : la page s'ouvre, 3 tables, 0 ligne, vues Prospects, Pipeline et A relancer) : https://airtable.com/appx78zW91WrDetpq/shrmW2gy5V4xXe7wf

Réserves relevées : la vue « Prospects » par défaut masque 2 champs (à afficher si on veut que le copieur voie tout) . La vue « Par échéance » de SC_Suivis est visible sur la page publique (constaté le 21/11) ; l'onglet SC_RDV n'a pas été ouvert.

À faire ensuite : version NocoDB (export de schéma) et version code.
