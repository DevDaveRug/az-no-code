# Import CSV -- raccourci Airtable + NocoDB (defi 3 masterclass-inscriptions)

1 CSV pret a importer pour creer la structure + peupler les donnees de demo en 5 min.

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
