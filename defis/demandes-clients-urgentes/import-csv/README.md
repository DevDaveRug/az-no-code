# Import CSV -- raccourci Airtable + NocoDB

Deux CSV prêts à importer pour créer la structure + peupler les données en 2 clics.

## Airtable

1. Ouvre la base cible (`Sales Closer Souverain` ou une base dédiée)

2. Bouton `+` (Add or import) -> `CSV file` -> choisis `clients.csv` -> `Upload`

3. Airtable détecte automatiquement les types (Email, PhoneNumber, SingleLineText). Vérifier et cliquer `Import`. Table `AZ_Clients` créée avec 5 lignes.

4. Rebelote pour `demandes.csv` -> table `AZ_Demandes` créée avec 10 lignes.

5. Créer le champ `Client` (Link record vers `AZ_Clients`) à la main dans `AZ_Demandes` :

   - Add field -> Link to another record -> AZ_Clients -> Allow linking to multiple records `Off`

6. Pour lier automatiquement chaque demande à son client existant via `EmailClientTemp` :

   - Option 1 (manuel, 30 secondes) : trier les demandes par EmailClientTemp, cliquer sur le champ Client de chaque groupe, chercher le nom, valider.

   - Option 2 (automatisation Airtable) : configurer l'Automatisation 1 de `airtable/SPECS.md` §Automatisation 1 -- elle applique le matching automatiquement au trigger `record is created`, mais pas rétroactivement sur les records déjà importés. Alternative : Airtable Script Extension one-shot qui parcourt tous les records `Client is empty` et applique le matching.

7. Ajouter les champs manquants après import (le CSV ne les porte pas) : `Ref` (Autonumber), `Commentaires` (LongText), `DateTraitement` (Date). Airtable a déjà créé les CreatedTime `Created time` par défaut sous un autre nom -- renommer en `DateCreation` sur `AZ_Clients` et `DateDemande` sur `AZ_Demandes`.

8. Vérifier que les SingleSelect (`UrgenceClient`, `UrgenceReelle`, `Statut`) ont bien les 4 options attendues (Airtable les crée à partir des valeurs uniques trouvées dans le CSV). Ajouter les couleurs selon `airtable/SPECS.md`.

Total : ~10 min pour avoir la base fonctionnelle avec données de démo.

## NocoDB

1. Ouvre la base cible (créer une nouvelle base `Demandes clients urgentes` si besoin)

2. Bouton `+` (Create) -> `Import CSV` -> `clients.csv` -> valider les mappings de type. Table `AZ_Clients` créée avec 5 lignes.

3. Rebelote pour `demandes.csv` -> `AZ_Demandes`.

4. Créer le champ `Client` (Links) vers `AZ_Clients` dans `AZ_Demandes`.

5. Peupler `Client` : script NocoDB `Scripts extension` ou boucle manuelle sur `EmailClientTemp`.

6. Configurer le webhook `After Insert` + le workflow n8n de `nocodb/SPECS.md` pour l'automatisation post-form future.

## Note sur les Link Records

Ni Airtable ni NocoDB ne permettent d importer les valeurs de Link Record via CSV -- l'import CSV crée uniquement des champs texte. C est pour ça que le CSV `demandes.csv` inclut les 3 champs dénormalisés `NomClientTemp` / `EmailClientTemp` / `TelephoneClientTemp` : ils servent au matching post-import (manuel ou automatisé) pour peupler le vrai Link Record.

Ce pattern (Link Record post-import via champ texte de matching) est la seule voie rapide pour importer 2 tables liées via CSV dans Airtable ou NocoDB. Il est aussi le pattern utilisé par le formulaire public en prod (Airtable Form ne permet pas non plus de créer un Link Record directement).
