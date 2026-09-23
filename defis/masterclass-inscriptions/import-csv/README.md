# Import CSV -- raccourci Airtable + NocoDB (defi 3 masterclass-inscriptions)

2 CSV prêts à importer pour créer la structure + peupler les données de démo + enregistrer le projet dans le portfolio.

## Fichiers livrés

- `inscrits.csv` : 5 lignes d'inscrits de démo (table `AZ_Inscrits`, 4 personas + David lui-même, cf. §Note ligne David).

- `masterclass-inscriptions-portfolio-row.csv` : 1 ligne prête à ajouter dans la table méta `AZ_Portfolio` de la base `Sales Closer Souverain` (raccourci Étape 8 du skill `/defi-hebdo-alegria`). Rétroactif S138z-ccdd (volet C IDEE_infra_198). Nom préfixé par slug (conforme code_archi R4, évite collision au téléchargement). `Statut` = `En cours` (Cor David S136z-D : passe en `Livré` une fois l'Interface publique montrable).

## Ajouter la ligne AZ_Portfolio (raccourci Étape 8 du skill)

`masterclass-inscriptions-portfolio-row.csv` contient une ligne unique avec 17 des 18 champs remplis (`Captures` reste vide, à uploader manuellement). `Lien_Airtable_Demo` pointe vers le formulaire public "Ajouter un inscrit à la masterclass" (`pagI3FYwUrzpkz2MO/form`) : accès direct sans authentification (préféré au partage Interface Dashboard bloqué par le forfait non-Team). `Lien_NocoDB_Demo` vide -- à remplir quand NocoDB sera déployé (IDEE_infra_196). Preview_Vercel = URL Vercel par défaut, custom domain à basculer plus tard (backlog IDEAS_PRO).

Deux voies pour l'ajouter à `AZ_Portfolio` :

1. **Import en ajout** (recommandé) : extension `CSV Import` (Extensions -> Add extension -> CSV Import -> table cible `AZ_Portfolio` -> Upload -> Match fields -> Import).

2. **Copier-coller manuel** (fallback 30 sec) : ouvrir `masterclass-inscriptions-portfolio-row.csv` dans un tableur, copier la ligne, coller dans une nouvelle ligne vide de `AZ_Portfolio`.

Après import : `DateLivraison` = `2026-09-18` doit être Date, `Statut` = `En cours` doit être SingleSelect (pas encore `Livré`), `Numéro` = 3 et `Semaine` = 38 doivent être Number.

---

## Airtable

1. Ouvre la base cible (`Sales Closer Souverain` ou une base dediee Masterclass)

2. Bouton `+` (Add or import) -> `CSV file` -> `inscrits.csv` -> `Upload`

3. Verifier types auto-detectes :
   - `Prenom` : Single line text
   - `Email` : Email
   - `DateMasterclass` : Date (format ISO)
   - `StatutEmail` : changer en `Single select` (options En attente / Envoye / Erreur)
   - `Notes` : Long text ou Single line text

4. Nom de la table : `AZ_Inscrits`.

5. Ajouter apres import les champs calc et auto (cf. `airtable/SPECS.md` §Fields calc) :
   - `DateInscription` : `Created time` (auto rempli a la creation, valeur = date d import ici)
   - Champ decoratif optionnel : `EmailError` (Long text) rempli par l automation en cas d echec Resend

Total : ~5 min pour la base.

## NocoDB

`Create > Import CSV` puis meme post-processing (renommage, StatutEmail en SingleSelect).

## Note ligne David

La ligne 5 (David lui-meme) est demandee explicitement par l enonce du defi 3 (Eva PRO 14/9). Le vrai email de David est `david@salescloser.fr`. En dev / demo publique, remplacer par un email de test avant capture (ex : `demo@salescloser.fr`) pour ne pas exposer son vrai email dans les screenshots.

## Piege Link Record

Ce defi n a qu une seule table AZ_Inscrits (contrairement au defi 4 qui a AZ_Clients + AZ_Demandes lies). Donc pas de Link Record a peupler manuellement post-import.

## Compagnon

- Spec complete : `defis/masterclass-inscriptions/airtable/SPECS.md`

- Interface Dashboard : `defis/masterclass-inscriptions/airtable/INTERFACE.md`

- Automation Resend + spec Postgres : `defis/masterclass-inscriptions/nocodb/SPECS.md`

- Code souverain equivalent : `az-code/defis/masterclass-inscriptions/`
