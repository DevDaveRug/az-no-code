# NocoDB -- commandes API prêtes (projet 5 + rattrapages base unifiée)

Instance `https://sb-nocodb.coolify.salescloser.fr` (NocoDB `2026.09.0`), base `phwalskbrftv4o4`. Identifiants de tables et de colonnes **réels**, lus le 28/09/2026 via le partage public de la base (lecture seule, sans jeton).

Prérequis : variable d'environnement `NOCODB_API_TOKEN` (jeton API NocoDB avec droits d'écriture sur la base), `curl`, `jq`. Jamais de jeton en clair dans ce fichier ni dans le chat.

Règle de rejeu (mémoire `feedback_trace_api_writes_toujours`) : chaque écriture a sa commande de retour arrière juste en dessous. Les réponses sont à garder dans le journal de session (identifiants créés).

```bash
NC=https://sb-nocodb.coolify.salescloser.fr
H=(-H "xc-token: $NOCODB_API_TOKEN" -H "Content-Type: application/json")
BASE=phwalskbrftv4o4
```

## Identifiants connus

| Table | Id table | Colonnes utiles |
|---|---|---|
| AZ_Clients | `my2gc6wwrlaqlh2` | Nom `c5x2madwt4exff6`, Email `c1yrnt0fomnpz02` (SingleLineText), Telephone `cfyqvt7ijmn0jt1` (SingleLineText), Prospect `ckh0r3l3eqlto16` (Links mm) |
| AZ_Demandes | `mka0odea97qenk1` | Description `caqtrskhe3auc9z` (SingleLineText), Client `ctyi57g0wdgc0mw` (mm) |
| SC_Prospects | `m163jpgx02zhgnc` | Email `c2hb1xs5axn8nmg`, AZ_Clients `clrbgx542dn3swz`, SC_CRs_de_RDVs `cspjb52l8ltf5ye`, AZ_Inscrits `ci5huhqmbsmieeh` |
| SC_CRs_de_RDV | `mkhq6zt5pv5gz0v` | Prospect `c8nt1nee0r7y59v` (Links mm) |
| AZ_Inscrits | `ml25u20dkb9gfxm` | Prenom `c7kohq8i5fgoi33`, Email `co1cj50lwrqgest`, Prospect `chdjhpzlb02gpoh` (Links mm) |

Enregistrements `AZ_Clients` : 1 Alice Martin, 2 Bob Durand, 3 Chloe Dubois, 4 Emma Petit, 5 Fabien Roux.

## 0- Contrôle d'accès (lecture)

```bash
curl -s "${H[@]}" "$NC/api/v2/meta/bases/$BASE/tables" | jq -r '.list[] | "\(.id) \(.title)"'
```

Attendu : les 5 tables ci-dessus. Si `AZ_Missions` existe déjà, sauter l'étape 1.

## 1- Créer la table `AZ_Missions`

```bash
MISSIONS=$(curl -s "${H[@]}" -X POST "$NC/api/v2/meta/bases/$BASE/tables" -d '{
  "table_name": "AZ_Missions",
  "title": "AZ_Missions",
  "columns": [
    {"column_name": "id", "title": "Id", "uidt": "ID"},
    {"column_name": "Mission", "title": "Mission", "uidt": "SingleLineText", "pv": true},
    {"column_name": "Statut", "title": "Statut", "uidt": "SingleSelect",
     "colOptions": {"options": [
       {"title": "À faire", "color": "#d9d9d9"},
       {"title": "En cours", "color": "#fdf0b0"},
       {"title": "Terminée", "color": "#c2f5e9"},
       {"title": "Arrêtée", "color": "#ffdce5"}]}},
    {"column_name": "Montant", "title": "Montant", "uidt": "Currency",
     "meta": {"currency_locale": "fr-FR", "currency_code": "EUR"}},
    {"column_name": "DateFin", "title": "DateFin", "uidt": "Date", "meta": {"date_format": "DD/MM/YYYY"}},
    {"column_name": "Facture", "title": "Facturé", "uidt": "Checkbox"},
    {"column_name": "NotesMigration", "title": "NotesMigration", "uidt": "LongText"}
  ]}' | jq -r '.id')
echo "AZ_Missions = $MISSIONS"
```

Retour arrière : `curl -s "${H[@]}" -X DELETE "$NC/api/v2/meta/tables/$MISSIONS"`

## 2- Lien `AZ_Clients` (1) -> `AZ_Missions` (N)

```bash
curl -s "${H[@]}" -X POST "$NC/api/v2/meta/tables/my2gc6wwrlaqlh2/columns" -d "{
  \"title\": \"Missions\", \"column_name\": \"Missions\", \"uidt\": \"Links\",
  \"parentId\": \"my2gc6wwrlaqlh2\", \"childId\": \"$MISSIONS\", \"type\": \"hm\"}" | jq '.id'

# Relire les identifiants des 2 côtés du lien
LINK_MISSIONS=$(curl -s "${H[@]}" "$NC/api/v2/meta/tables/my2gc6wwrlaqlh2" | jq -r '.columns[] | select(.title=="Missions") | .id')
LINK_CLIENT=$(curl -s "${H[@]}" "$NC/api/v2/meta/tables/$MISSIONS" | jq -r '.columns[] | select(.uidt=="Links" or .uidt=="LinkToAnotherRecord") | .id')
echo "AZ_Clients.Missions = $LINK_MISSIONS ; AZ_Missions.(lien client) = $LINK_CLIENT"
```

Le côté `AZ_Missions` du lien est créé avec le titre `AZ_Clients` : le renommer `Client` dans l'UI (clic sur l'en-tête > Edit, 10 secondes). L'API de mise à jour de colonne exige le payload complet de la colonne, l'UI est plus sûre ici.

Retour arrière : `curl -s "${H[@]}" -X DELETE "$NC/api/v2/meta/columns/$LINK_MISSIONS"`

## 3- Formules d'aide dans `AZ_Missions` (cumul conditionnel absent du plan Free)

```bash
for f in \
  '{"title":"MontantFacturé","column_name":"MontantFacture","uidt":"Formula","formula_raw":"IF({Facturé}, {Montant}, 0)"}' \
  '{"title":"MontantResteAFacturer","column_name":"MontantResteAFacturer","uidt":"Formula","formula_raw":"IF({Facturé}, 0, IF({Statut} = \"Terminée\", {Montant}, 0))"}' \
  '{"title":"MontantEnCours","column_name":"MontantEnCours","uidt":"Formula","formula_raw":"IF({Statut} = \"En cours\", {Montant}, 0)"}'
do curl -s "${H[@]}" -X POST "$NC/api/v2/meta/tables/$MISSIONS/columns" -d "$f" | jq -c '{id, title, msg}'; done
```

## 4- `AZ_Clients` : Entreprise + cumuls

```bash
# Entreprise
curl -s "${H[@]}" -X POST "$NC/api/v2/meta/tables/my2gc6wwrlaqlh2/columns" \
  -d '{"title":"Entreprise","column_name":"Entreprise","uidt":"SingleLineText"}' | jq -c '{id, title}'

curl -s "${H[@]}" -X PATCH "$NC/api/v2/tables/my2gc6wwrlaqlh2/records" -d '[
  {"Id":1,"Entreprise":"Cabinet Legrand"},{"Id":2,"Entreprise":"TechFlow SAS"},
  {"Id":3,"Entreprise":"Studio Zenith"},{"Id":4,"Entreprise":"MarketPro"},
  {"Id":5,"Entreprise":"Atelier Nord"}]' | jq -c '.'

# Cumuls (Rollup sum sur les formules d'aide)
col() { curl -s "${H[@]}" "$NC/api/v2/meta/tables/$MISSIONS" | jq -r --arg t "$1" '.columns[] | select(.title==$t) | .id'; }
for pair in "CAFacturé:MontantFacturé" "ResteAFacturer:MontantResteAFacturer" "MontantEnCours:MontantEnCours"; do
  T=${pair%%:*}; SRC=$(col "${pair##*:}")
  curl -s "${H[@]}" -X POST "$NC/api/v2/meta/tables/my2gc6wwrlaqlh2/columns" -d "{
    \"title\": \"$T\", \"uidt\": \"Rollup\", \"fk_relation_column_id\": \"$LINK_MISSIONS\",
    \"fk_rollup_column_id\": \"$SRC\", \"rollup_function\": \"sum\"}" | jq -c '{id, title, msg}'
done
```

Si une réponse contient `msg` (Rollup sur Formula refusé par cette version) : appliquer le plan B de `SPECS.md` (Grid groupée par Client avec agrégation `Sum`), sans insister.

Le champ `Missions` (Links) affiche déjà le nombre de missions liées : `NbMissions` est facultatif côté NocoDB.

## 5- Données d'exemple (7 missions) + liens

```bash
curl -s "${H[@]}" -X POST "$NC/api/v2/tables/$MISSIONS/records" -d @- <<'JSON' | jq -c '.'
[
 {"Mission":"Refonte site","Statut":"Terminée","Montant":2500,"DateFin":"2026-03-12","Facturé":true,"NotesMigration":null},
 {"Mission":"Création d'une app mobile","Statut":"Terminée","Montant":80000,"DateFin":"2026-05-31","Facturé":true,"NotesMigration":"Date d'origine 'mai 2026' sans jour : dernier jour du mois retenu, à confirmer."},
 {"Mission":"Maintenance page","Statut":"Arrêtée","Montant":500,"DateFin":"2026-06-30","Facturé":true,"NotesMigration":"Date d'origine 'juin' sans jour ni année : 30/06/2026 retenu, à confirmer. Facturé d'origine 'x' lu comme oui, à confirmer. Doublon possible avec 'Maintenance' (même cliente)."},
 {"Mission":"Audit","Statut":"En cours","Montant":1200,"DateFin":"2026-06-24","Facturé":false,"NotesMigration":"Facturé d'origine vide lu comme non."},
 {"Mission":"Maintenance","Statut":"En cours","Montant":450,"DateFin":"2026-07-31","Facturé":false,"NotesMigration":"Date d'origine 'Juillet' sans jour ni année : 31/07/2026 retenu, à confirmer. Doublon possible avec 'Maintenance page' (même cliente)."},
 {"Mission":"Tunnel de vente","Statut":"Terminée","Montant":3200,"DateFin":"2026-05-15","Facturé":false,"NotesMigration":null},
 {"Mission":"Atelier CRM","Statut":"À faire","Montant":900,"DateFin":"2026-10-30","Facturé":false,"NotesMigration":"Facturé d'origine vide lu comme non."}
]
JSON
```

Attendu : `[{"Id":1},...,{"Id":7}]` dans l'ordre ci-dessus. Liens (côté client, 1 appel par client) :

```bash
link() { curl -s "${H[@]}" -X POST "$NC/api/v2/tables/my2gc6wwrlaqlh2/links/$LINK_MISSIONS/records/$1" -d "$2" | jq -c '.'; }
link 1 '[{"Id":1},{"Id":3},{"Id":5}]'   # Alice Martin : Refonte site, Maintenance page, Maintenance
link 2 '[{"Id":2}]'                     # Bob Durand : app mobile
link 3 '[{"Id":4}]'                     # Chloe Dubois : Audit
link 4 '[{"Id":6}]'                     # Emma Petit : Tunnel de vente
link 5 '[{"Id":7}]'                     # Fabien Roux : Atelier CRM
```

Contrôle : `curl -s "${H[@]}" "$NC/api/v2/tables/my2gc6wwrlaqlh2/records?fields=Nom,CAFacturé,ResteAFacturer,MontantEnCours" | jq -c '.list[]'` doit rendre 3000 / 80000 / 0 / 0 / 0 pour CAFacturé (Alice, Bob, Chloe, Emma, Fabien), 3200 en ResteAFacturer pour Emma, 450 et 1200 en MontantEnCours pour Alice et Chloe.

Retour arrière des liens : même appel avec `-X DELETE`.

## 6- (Optionnel, Val David) Corriger les types de `AZ_Clients` et `AZ_Demandes`

Constat : `AZ_Clients.Email` et `AZ_Clients.Telephone` sont en `SingleLineText`, `AZ_Demandes.Description` aussi (Airtable : Email, Phone number, Long text). C'est le défaut n°1 du diagnostic, dans notre propre base. Correction la plus sûre : UI, en-tête de colonne > Edit > type `Email` / `PhoneNumber` / `LongText` (3 x 10 secondes, NocoDB convertit les valeurs existantes).

## 7- Rattrapage Link Records Fabien Roux (P2 S163z)

Constat du 28/09 : `SC_Prospects` n'a que 4 enregistrements (Alice, Bob, Chloe, Emma). Fabien Roux existe dans `AZ_Clients` (5), `SC_CRs_de_RDV` (5, « Fabien Roux - Atelier Nord ») mais pas dans `SC_Prospects`, d'où les 3 liens vides.

```bash
# 7a- Créer le prospect Fabien Roux
FABIEN=$(curl -s "${H[@]}" -X POST "$NC/api/v2/tables/m163jpgx02zhgnc/records" -d '[{
  "Prenom":"Fabien","Nom":"Roux","Entreprise":"Atelier Nord",
  "Email":"fabien.roux@atelier-nord.example","Telephone":"+33 6 00 00 00 15",
  "Statut":"En cours","Source":"Recommandation","DateEntree":"2026-09-20","DernierContact":"2026-09-24"}]' | jq -r '.[0].Id')
echo "SC_Prospects Fabien = $FABIEN"

# 7b- Relier AZ_Clients 5 et SC_CRs_de_RDV 5
curl -s "${H[@]}" -X POST "$NC/api/v2/tables/my2gc6wwrlaqlh2/links/ckh0r3l3eqlto16/records/5" -d "[{\"Id\":$FABIEN}]" | jq -c '.'
curl -s "${H[@]}" -X POST "$NC/api/v2/tables/mkhq6zt5pv5gz0v/links/c8nt1nee0r7y59v/records/5" -d "[{\"Id\":$FABIEN}]" | jq -c '.'
```

Retour arrière : `-X DELETE` sur les 2 liens, puis `curl -s "${H[@]}" -X DELETE "$NC/api/v2/tables/m163jpgx02zhgnc/records" -d "[{\"Id\":$FABIEN}]"`.

### 7c- `AZ_Inscrits` n°5 : données réelles dans une base de démonstration (Val David S163z, 28/09)

État au 28/09/2026 18h : correction validée, pas encore appliquée (aucun jeton dans la session). Voie sans jeton : interface NocoDB, voir §8 bis étape 0.

L'inscrit n°5 est `David` / `david@salescloser.fr` : ton vrai prénom et ton vrai e-mail dans une base partagée publiquement, contraire à la règle d'anonymisation du skill (« ne jamais utiliser le vrai nom de David comme fake data »). Proposition : le remplacer par Fabien Roux, puis le relier.

```bash
curl -s "${H[@]}" -X PATCH "$NC/api/v2/tables/ml25u20dkb9gfxm/records" \
  -d '[{"Id":5,"Prenom":"Fabien","Email":"fabien.roux@atelier-nord.example"}]' | jq -c '.'
curl -s "${H[@]}" -X POST "$NC/api/v2/tables/ml25u20dkb9gfxm/links/chdjhpzlb02gpoh/records/5" -d "[{\"Id\":$FABIEN}]" | jq -c '.'
```

Retour arrière : `-d '[{"Id":5,"Prenom":"David","Email":"david@salescloser.fr"}]'` (déconseillé). Vérifier aussi l'équivalent côté Airtable (`AZ_Inscrits`).

### 7d- (Optionnel) Même personne, 2 e-mails

`SC_Prospects` et `AZ_Clients` n'ont pas le même e-mail pour 3 personnes (`b.durand@techflow.example` / `bob.durand@techflow.example`, `c.dubois@zenith.example` / `chloe.dubois@studio-zenith.example`, `e.petit@marketpro.example` / `emma.petit@marketpro.example`), ni le même téléphone pour Alice (`...12` / `...11`). C'est le défaut n°2 du diagnostic (le même client écrit de 2 façons). À harmoniser si tu veux une démonstration « base unifiée » irréprochable : choisir une source, PATCH de l'autre table.

## 8- 4 formulaires publics (P2 S163z)

Tables : `SC_Prospects`, `SC_CRs_de_RDV`, `AZ_Inscrits`, `AZ_Missions`. Seul `AZ_Demandes` a déjà un formulaire public (`#/nc/form/1a730f9f-2091-47c5-88e5-3d6f40c145fa`).

```bash
form() { # $1 = id table, $2 = titre du formulaire
  V=$(curl -s "${H[@]}" -X POST "$NC/api/v2/meta/tables/$1/forms" -d "{\"title\":\"$2\"}" | jq -r '.id')
  U=$(curl -s "${H[@]}" -X POST "$NC/api/v2/meta/views/$V/share" | jq -r '.uuid')
  echo "$2 : view=$V -> $NC/#/nc/form/$U"
}
form m163jpgx02zhgnc "Nouveau prospect"
form mkhq6zt5pv5gz0v "Nouveau compte rendu de RDV"
form ml25u20dkb9gfxm "Inscription masterclass"
form "$MISSIONS"     "Nouvelle mission"
```

Après création : dans l'UI, masquer les champs techniques de chaque formulaire (Links vers d'autres tables, formules, `CRFormate`, `Cout_LLM`, `StatutEmail`), 1 minute par formulaire. Les 4 URL publiques vont dans le journal de session, et celle de `Nouvelle mission` dans `Lien_NocoDB_Demo` du projet 5.

Retour arrière : `curl -s "${H[@]}" -X DELETE "$NC/api/v2/meta/views/$V/share"` (dépublie) puis `-X DELETE "$NC/api/v2/meta/forms/$V"`.

## 8 bis- Sans jeton : les mêmes actions dans l'interface NocoDB (Cor David S163z)

Constat du 28/09/2026 (lecture du partage public) : seul `AZ_Demandes` (projet 4) a un formulaire public. `SC_Prospects` (projet 1), `SC_CRs_de_RDV` (projet 2) et `AZ_Inscrits` (projet 3) n'en ont aucun.

### Étape 0- Anonymiser `AZ_Inscrits` n°5 et relier Fabien Roux (3 minutes)

1- `SC_Prospects` : nouvelle ligne `Fabien` / `Roux` / `Atelier Nord` / `fabien.roux@atelier-nord.example` / `+33 6 00 00 00 15`, Statut `En cours`, Source `Recommandation`

2- `AZ_Inscrits`, ligne 5 : Prenom `David` -> `Fabien`, Email `david@salescloser.fr` -> `fabien.roux@atelier-nord.example`, puis cellule `Prospect` -> `+` -> Fabien Roux

3- `AZ_Clients`, ligne 5 (Fabien Roux) : cellule `Prospect` -> `+` -> Fabien Roux

4- `SC_CRs_de_RDV`, ligne 5 (Fabien Roux - Atelier Nord) : cellule `Prospect` -> `+` -> Fabien Roux

Vérifier aussi `AZ_Inscrits` côté Airtable : même ligne probable.

### Étape 1- Valeurs par défaut (elles remplacent les champs cachés des formulaires Airtable)

Un champ masqué d'un formulaire NocoDB n'est pas envoyé : c'est la valeur par défaut de la colonne qui s'applique. En-tête de colonne -> `Edit` -> valeur par défaut :

-> `SC_Prospects.Statut` : `Nouveau`

-> `SC_CRs_de_RDV.Statut` : `A formater`

-> `AZ_Inscrits.StatutEmail` : `En attente`

### Étape 2- Créer chaque formulaire (5 minutes par table)

Barre latérale, sous la table -> `+` (créer une vue) -> `Form` -> nommer. Dans l'éditeur : garder uniquement les champs listés, cocher `Required` quand indiqué, renseigner le message après envoi.

| Table (projet) | Nom du formulaire | Champs visibles, dans l'ordre | Masqués | Message après envoi |
|---|---|---|---|---|
| `SC_Prospects` (1) | Ajouter un prospect | Prenom (requis), Nom (requis), Entreprise, Email (requis), Telephone, Source, Notes | Statut, DateEntree, DernierContact, MontantPotentiel, tous les liens | Prospect ajouté. Tu peux le suivre depuis la vue Tous les prospects. |
| `SC_CRs_de_RDV` (2) | Nouveau CR de RDV | DateRDV (requis), Client (requis), ContexteRDV (requis), NotesBrutes (requis) | CRFormate, Statut, DateFormatage, Cout_LLM, Prospect | Merci, ton compte rendu brut est bien enregistré. |
| `AZ_Inscrits` (3) | Inscription masterclass | Prenom (requis), Email (requis), DateMasterclass (requis) | StatutEmail, Notes, Prospect | Merci pour ton inscription ! Un e-mail de confirmation arrive dans quelques secondes. |

Toujours masquer les champs `Links` (Prospect, AZ_Clients...) dans un formulaire public : sinon le visiteur voit et peut choisir les enregistrements de la table liée.

`DateEntree` et `DernierContact` (projet 1) : si l'édition de colonne propose une date du jour par défaut, l'activer ; sinon les laisser vides (le prospect n'apparaît dans `À relancer` qu'une fois `DernierContact` rempli).

### Étape 3- Publier

En haut à droite du formulaire : `Share` -> activer le partage public -> copier le lien (format `https://sb-nocodb.coolify.salescloser.fr/#/nc/form/<uuid>`). Tester chaque lien en navigation privée : une soumission doit créer une ligne visible dans la table.

### Étape 4- Reporter les liens

`Lien_NocoDB_Demo` de `AZ_Portfolio` (projets 1, 2, 3) + les 3 `<slug>-portfolio-row.csv`. Le lien de la base partagée reste utilisable pour la consultation, le formulaire est le lien de démonstration.

## 9- Vues à créer dans l'UI

Filtres et tris de vues : UI (2 minutes), plus lisible que l'API. `À facturer`, `À vérifier (migration)`, `Toutes les missions` (Grid), `Par statut` (Kanban), `Échéances` (Calendar), `CA par client` (Grid sur `AZ_Clients`). Détail : `SPECS.md` et `INTERFACE.md`.

Note : les chemins d'API ci-dessus suivent l'API v2 NocoDB. Au 1er appel d'écriture, lire la réponse : si un champ du payload n'est pas reconnu par la version `2026.09.0`, la réponse le dit (`msg`), corriger avant de lancer la suite.

## 10- Exécution du 28/09/2026 (session fille S163z)

Jetons présents dans l'environnement (`NOCODB_API_TOKEN`, `AIRTABLE_PAT`), jamais affichés. Lectures préalables : liste des tables, swagger de la base (routes données uniquement, les routes méta ne sont pas dans ce swagger : utilisées selon l'API v2 et contrôlées par un GET après chaque écriture), méta des 4 tables, enregistrements concernés. Aucune table créée, rien supprimé.

```bash
NC=https://sb-nocodb.coolify.salescloser.fr
H=(-H "xc-token: $NOCODB_API_TOKEN" -H "Content-Type: application/json")
AT=https://api.airtable.com/v0/appTqLo3JDg7d1fak
AH=(-H "Authorization: Bearer $AIRTABLE_PAT" -H "Content-Type: application/json")
```

### 10a- `AZ_Inscrits` n°5 anonymisé

```bash
curl -s "${H[@]}" -X PATCH "$NC/api/v2/tables/ml25u20dkb9gfxm/records" \
  -d '[{"Id":5,"Prenom":"Fabien","Email":"fabien.roux@atelier-nord.example"}]'
```

Réponse : `[{"Id":5}]`. GET de contrôle : Prenom `Fabien`, Email `fabien.roux@atelier-nord.example`. Valeurs d'avant : `David` / `david@salescloser.fr` (retour arrière : même PATCH avec ces valeurs, déconseillé).

### 10b- Prospect Fabien Roux + 3 liens

```bash
curl -s "${H[@]}" -X POST "$NC/api/v2/tables/m163jpgx02zhgnc/records" -d '[{
  "Prenom":"Fabien","Nom":"Roux","Entreprise":"Atelier Nord",
  "Email":"fabien.roux@atelier-nord.example","Telephone":"+33 6 00 00 00 15",
  "Statut":"En cours","Source":"Recommandation","DateEntree":"2026-09-20","DernierContact":"2026-09-24"}]'
curl -s "${H[@]}" -X POST "$NC/api/v2/tables/my2gc6wwrlaqlh2/links/ckh0r3l3eqlto16/records/5" -d '[{"Id":5}]'  # AZ_Clients 5
curl -s "${H[@]}" -X POST "$NC/api/v2/tables/mkhq6zt5pv5gz0v/links/c8nt1nee0r7y59v/records/5" -d '[{"Id":5}]'  # SC_CRs_de_RDV 5
curl -s "${H[@]}" -X POST "$NC/api/v2/tables/ml25u20dkb9gfxm/links/chdjhpzlb02gpoh/records/5" -d '[{"Id":5}]'  # AZ_Inscrits 5
```

Réponses : `[{"Id":5}]` (création : **SC_Prospects Id 5**), puis `true` x 3. GET de contrôle : les 10 champs conformes, et chaque lien renvoie `[{"Id":5,"Prenom":"Fabien"}]`.

Retour arrière : les 3 appels de lien avec `-X DELETE`, puis `curl -s "${H[@]}" -X DELETE "$NC/api/v2/tables/m163jpgx02zhgnc/records" -d '[{"Id":5}]'`.

### 10c- Valeurs par défaut des colonnes

L'API exige le payload complet de la colonne. Méthode sans risque retenue : GET de la colonne, modification du seul `cdf`, PATCH du même objet (mêmes options, mêmes identifiants d'options). Contrôle avant/après : identifiants d'options inchangés et valeurs des enregistrements identiques (comparaison des instantanés) sur les 3 tables.

```bash
setdef() { # $1 = id colonne, $2 = valeur par défaut
  curl -s "${H[@]}" "$NC/api/v2/meta/columns/$1" | jq -c --arg v "$2" '.cdf=$v' \
  | curl -s "${H[@]}" -X PATCH "$NC/api/v2/meta/columns/$1" -d @- | jq -c '{msg}'; }
setdef coy4deomg31alb7 "Nouveau"      # SC_Prospects.Statut
setdef c87ppy0qhvsv546 "A formater"   # SC_CRs_de_RDV.Statut
setdef c1d9y0hz40lm29z "En attente"   # AZ_Inscrits.StatutEmail
```

Réponses : `{"msg":null}` x 3, `cdf` relu conforme. Valeur d'avant : `cdf` null partout. Retour arrière : même fonction avec `jq '.cdf=null'`.

### 10d- 3 formulaires publics

| Table | Formulaire | Id vue | Champs visibles (* requis) |
|---|---|---|---|
| `SC_Prospects` | Ajouter un prospect | `vw1ka15rxzp856j2` | Prenom*, Nom*, Entreprise, Email*, Telephone, Source, Notes |
| `SC_CRs_de_RDV` | Nouveau CR de RDV | `vwpo6k6790xpwxhy` | DateRDV*, Client*, ContexteRDV*, NotesBrutes* |
| `AZ_Inscrits` | Inscription masterclass | `vwft4pbieg1qzdrc` | Prenom*, Email*, DateMasterclass* |

Tous les autres champs masqués (dont tous les `Links`). Messages après envoi : ceux du §8 bis, à l'identique.

```bash
# Création (une fois par table)
V=$(curl -s "${H[@]}" -X POST "$NC/api/v2/meta/tables/m163jpgx02zhgnc/forms" -d '{"title":"Ajouter un prospect"}' | jq -r .id)
# Champs : pour chaque colonne de formulaire (GET $NC/api/v2/meta/forms/$V -> .columns[].id)
curl -s "${H[@]}" -X PATCH "$NC/api/v2/meta/form-columns/<id colonne de formulaire>" -d '{"show":true,"required":true,"order":1}'   # visible
curl -s "${H[@]}" -X PATCH "$NC/api/v2/meta/form-columns/<id colonne de formulaire>" -d '{"show":false,"required":false,"order":100}' # masqué
# Message après envoi
curl -s "${H[@]}" -X PATCH "$NC/api/v2/meta/forms/$V" -d '{"success_msg":"Prospect ajouté. Tu peux le suivre depuis la vue Tous les prospects."}'
# Partage public
curl -s "${H[@]}" -X POST "$NC/api/v2/meta/views/$V/share" | jq -r .uuid
```

Script complet de configuration des champs (correspondance titre -> colonne via la méta de la table) : boucle sur `.columns[]` du formulaire, `show` vrai uniquement pour les titres listés ci-dessus. Réponses : aucun `msg` d'erreur, relecture conforme.

Retour arrière : `curl -s "${H[@]}" -X DELETE "$NC/api/v2/meta/views/$V/share"` (dépublie) puis `-X DELETE "$NC/api/v2/meta/forms/$V"`.

### 10e- URL publiques (vérifiées par `GET $NC/api/v2/public/shared-view/<uuid>/meta`, sans jeton, sans soumission : HTTP 200, titre, champs visibles, requis et message conformes)

-> Projet 1, `SC_Prospects` : https://sb-nocodb.coolify.salescloser.fr/#/nc/form/0476d0c5-1db4-4cd7-b62a-552adbdf818c

-> Projet 2, `SC_CRs_de_RDV` : https://sb-nocodb.coolify.salescloser.fr/#/nc/form/4aca5fa0-f7f5-4216-85ce-092959518893

-> Projet 3, `AZ_Inscrits` : https://sb-nocodb.coolify.salescloser.fr/#/nc/form/e3eb2619-338b-4451-9c9e-57cd07ed13e8

### 10f- Airtable

`AZ_Inscrits` : aucune ligne `David` ni `david@salescloser.fr` (7 lignes lues), aucune écriture.

`AZ_Portfolio`, `Lien_NocoDB_Demo` des projets 1, 2, 3 (`typecast` false) :

```bash
F=https://sb-nocodb.coolify.salescloser.fr/#/nc/form
curl -s "${AH[@]}" -X PATCH "$AT/AZ_Portfolio" -d "{\"typecast\":false,\"records\":[
 {\"id\":\"recpzC5yp61ZyqL89\",\"fields\":{\"Lien_NocoDB_Demo\":\"$F/0476d0c5-1db4-4cd7-b62a-552adbdf818c\"}},
 {\"id\":\"recemskuGIXm6vbnD\",\"fields\":{\"Lien_NocoDB_Demo\":\"$F/4aca5fa0-f7f5-4216-85ce-092959518893\"}},
 {\"id\":\"recMatGGZkUBzu15V\",\"fields\":{\"Lien_NocoDB_Demo\":\"$F/e3eb2619-338b-4451-9c9e-57cd07ed13e8\"}}]}"
```

Réponse : les 3 identifiants renvoyés, GET de contrôle conforme (projet 4 inchangé). Valeur d'avant, identique pour les 3 : `https://sb-nocodb.coolify.salescloser.fr/#/base/22ca37d4-9d7a-4cf2-86ed-6e569babae1d` (retour arrière : même PATCH avec cette URL).

CSV mis à jour : `Lien_NocoDB_Demo` des 3 `*-portfolio-row.csv` (projets 1, 2, 3).

### 10g- Sauté ou à noter

-> Rien de sauté dans le périmètre.

-> Vue grille `SC_Prospects` renommée « Tous les prospects » par David le 29/09 (UI) : le message du formulaire `Ajouter un prospect` est cohérent.

-> Aucun test de soumission réel (consigne) : à faire une fois en navigation privée si tu veux la preuve bout en bout, puis supprimer la ligne de test.

-> Note « Lien_NocoDB_Demo vide tant que NocoDB pas déployé » : corrigée, voir 10h.

### 10h- Suivi du 29/09/2026

NocoDB est déployé depuis S153c-ccdd : la mention « NocoDB pas déployé » était obsolète, rien à déployer.

-> CSV projet 1 : `Notes` déjà corrigée par la session mère (commit `ebc307d`).

-> `import-csv/README.md` des défis 1, 2, 3 : la phrase « `Lien_NocoDB_Demo` vide » remplacée par le lien du formulaire NocoDB.

-> Airtable `AZ_Portfolio` projet 1 (`recpzC5yp61ZyqL89`), champ `Notes` aligné sur le CSV :

```bash
python3 -c "import csv,json;r=list(csv.DictReader(open('defis/crm-souverain/import-csv/crm-souverain-portfolio-row.csv')))[0];print(json.dumps({'typecast':False,'records':[{'id':'recpzC5yp61ZyqL89','fields':{'Notes':r['Notes']}}]}))" \
  | curl -s "${AH[@]}" -X PATCH "$AT/AZ_Portfolio" -d @-
```

Réponse : `recpzC5yp61ZyqL89` renvoyé, GET conforme. Retour arrière : même PATCH avec l'ancienne fin de note « Lien_NocoDB_Demo vide tant que NocoDB pas déployé (IDEE_infra_196). » à la place de la phrase `Lien_NocoDB_Demo = ...`. Projets 2 à 5 : aucune mention obsolète.
