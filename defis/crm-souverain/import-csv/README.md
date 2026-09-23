# Import CSV -- raccourci Airtable + NocoDB (defi 1 crm-souverain)

2 CSV prêts à importer pour créer la structure + peupler les données + enregistrer le projet dans le portfolio.

## Fichiers livrés

- `prospects.csv` : 4 lignes de prospects de démo (table `SC_Prospects`), 1 par statut (Nouveau / En cours / Gagné / Perdu) + 1 en "à relancer".

- `crm-souverain-portfolio-row.csv` : 1 ligne prête à ajouter dans la table méta `AZ_Portfolio` de la base `Sales Closer Souverain` (raccourci Étape 8 du skill `/defi-hebdo-alegria` -- 30 sec au lieu de 5 min de saisie manuelle). Rétroactif S138z-ccdd (volet C IDEE_infra_198). Nom préfixé par slug (conforme code_archi R4 kebab-case pur zone repo, évite les collisions au téléchargement multiple).

## Ajouter la ligne AZ_Portfolio (raccourci Étape 8 du skill)

`crm-souverain-portfolio-row.csv` contient une ligne unique avec 17 des 18 champs remplis (`Captures` reste vide, c'est une pièce jointe à uploader manuellement dans la ligne Airtable après import). `Lien_Airtable_Demo` pointe vers le formulaire public "Ajouter un prospect" (`page28aUl1MwHBuqv/form`) : accès direct sans authentification, préféré à l'Interface Dashboard qui exige un login. `Lien_NocoDB_Demo` vide -- à remplir quand NocoDB sera déployé (IDEE_infra_196). Preview_Vercel = URL Vercel par défaut, custom domain à basculer plus tard (backlog IDEAS_PRO).

Deux voies pour l'ajouter à `AZ_Portfolio` :

1. **Import en ajout** (recommandé si la table existe déjà) : Airtable ne propose pas d'`Append CSV` natif dans l'UI -- utiliser l'extension `CSV Import` (Extensions -> Add extension -> CSV Import -> table cible `AZ_Portfolio` -> Upload -> Match fields -> Import). Ajoute la ligne sans écraser.

2. **Copier-coller manuel** (fallback 30 sec) : ouvrir `crm-souverain-portfolio-row.csv` dans un tableur, copier la ligne de données, coller dans une nouvelle ligne vide de `AZ_Portfolio`. Airtable colle valeur par valeur.

Après import : `DateLivraison` = `2026-09-04` doit être Date, `Statut` = `Livré` doit être SingleSelect, `Numéro` = 1 et `Semaine` = 36 doivent être Number.

---

## Airtable

1. Ouvre la base cible (`Sales Closer Souverain` ou une base dediee CRM)

2. Bouton `+` (Add or import) -> `CSV file` -> `prospects.csv` -> `Upload`

3. Airtable detecte automatiquement la plupart des types (Email, PhoneNumber, Date, Number, SingleLineText). Verifier :
   - `Statut` : changer en `Single select` (Airtable creera les 4 options Nouveau / En cours / Gagne / Perdu)
   - `Source` : `Single select` (LinkedIn / Recommandation / Site)
   - `DateEntree`, `DernierContact` : `Date` (format ISO detecte)
   - `MontantPotentiel` : `Number`
   - `Notes` : `Long text`

4. Nom de la table : renomme en `SC_Prospects`.

5. Ajouter apres import les champs calc manquants (cf. `airtable/SPECS.md` §Champs calc) :
   - `NomComplet` : Formula `{Prenom} & " " & {Nom}`
   - `ProchaineRelance` : Formula avec IF sur Statut + DATEADD sur DernierContact
   - `Delai` : Formula DATETIME_DIFF pour classer les relances

Total : ~5 min pour la base + ~5 min pour les champs calc.

## NocoDB

Meme methode via `Create > Import CSV` sur la base cible NocoDB. Types auto-detectes similaires, ajouter les Formula fields en post-import (equivalents NocoDB).

## Cas particulier defi 1

Ce defi n a qu une seule table SC_Prospects donc pas de Link Record post-import a peupler manuellement (contrairement au defi 4 demandes-clients-urgentes qui a 2 tables liees).
