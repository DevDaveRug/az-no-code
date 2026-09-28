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

### 7c- `AZ_Inscrits` n°5 : données réelles dans une base de démonstration (Val David requis)

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

## 9- Vues à créer dans l'UI

Filtres et tris de vues : UI (2 minutes), plus lisible que l'API. `À facturer`, `À vérifier (migration)`, `Toutes les missions` (Grid), `Par statut` (Kanban), `Échéances` (Calendar), `CA par client` (Grid sur `AZ_Clients`). Détail : `SPECS.md` et `INTERFACE.md`.

Note : les chemins d'API ci-dessus suivent l'API v2 NocoDB. Au 1er appel d'écriture, lire la réponse : si un champ du payload n'est pas reconnu par la version `2026.09.0`, la réponse le dit (`msg`), corriger avant de lancer la suite.
