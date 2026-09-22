# Import CSV -- raccourci Airtable + NocoDB (defi 1 crm-souverain)

1 CSV pret a importer pour creer la structure + peupler les donnees en 5 min.

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
