# Airtable -- commandes API prêtes (projet 5)

Base `Sales Closer Souverain` (`appTqLo3JDg7d1fak`). Prérequis : variable `AIRTABLE_PAT` (scopes `data.records:read`, `data.records:write`, `schema.bases:read`, `schema.bases:write` sur cette base), `curl`, `jq`. Jamais de jeton en clair.

Chaque écriture a son retour arrière (mémoire `feedback_trace_api_writes_toujours`). Toutes les commandes se lancent depuis le dossier `airtable/` du projet (chemins relatifs vers `schema.json` et `../import-csv/`).

```bash
AT=https://api.airtable.com/v0
BASE=appTqLo3JDg7d1fak
H=(-H "Authorization: Bearer $AIRTABLE_PAT" -H "Content-Type: application/json")
tid() { curl -s "${H[@]}" "$AT/meta/bases/$BASE/tables" | jq -r --arg n "$1" '.tables[] | select(.name==$n) | .id'; }
CLIENTS=$(tid AZ_Clients); echo "AZ_Clients = $CLIENTS"
```

## 1- Table `AZ_Missions` (sans le lien)

```bash
jq '.tables[0]' schema.json > /tmp/az_missions.json
MISSIONS=$(curl -s "${H[@]}" -X POST "$AT/meta/bases/$BASE/tables" -d @/tmp/az_missions.json | jq -r '.id')
echo "AZ_Missions = $MISSIONS"
```

Retour arrière : pas de suppression de table par l'API Airtable, suppression dans l'UI (clic droit sur l'onglet > Supprimer la table).

## 2- Lien `Client` + champ `Entreprise`

```bash
curl -s "${H[@]}" -X POST "$AT/meta/bases/$BASE/tables/$MISSIONS/fields" \
  -d "{\"name\":\"Client\",\"type\":\"multipleRecordLinks\",\"options\":{\"linkedTableId\":\"$CLIENTS\",\"prefersSingleRecordLink\":true}}" | jq -c '{id, name}'
curl -s "${H[@]}" -X POST "$AT/meta/bases/$BASE/tables/$CLIENTS/fields" \
  -d '{"name":"Entreprise","type":"singleLineText"}' | jq -c '{id, name}'
```

Vérifier ensuite dans l'UI que le symétrique existe côté `AZ_Clients` et le renommer `Missions` (piège S135z).

## 3- Identifiants des clients existants + Entreprise

```bash
curl -s "${H[@]}" "$AT/$BASE/AZ_Clients?fields%5B%5D=Nom&fields%5B%5D=Email" \
  | jq -r '.records[] | "\(.id) \(.fields.Nom) \(.fields.Email)"' | tee /tmp/az_clients.txt
rec() { grep -i "$1" /tmp/az_clients.txt | cut -d' ' -f1; }
ALICE=$(rec alice.martin); BOB=$(rec bob.durand); CHLOE=$(rec chloe.dubois); EMMA=$(rec emma.petit); FABIEN=$(rec fabien.roux)

curl -s "${H[@]}" -X PATCH "$AT/$BASE/AZ_Clients" -d "{\"records\":[
 {\"id\":\"$ALICE\",\"fields\":{\"Entreprise\":\"Cabinet Legrand\"}},
 {\"id\":\"$BOB\",\"fields\":{\"Entreprise\":\"TechFlow SAS\"}},
 {\"id\":\"$CHLOE\",\"fields\":{\"Entreprise\":\"Studio Zenith\"}},
 {\"id\":\"$EMMA\",\"fields\":{\"Entreprise\":\"MarketPro\"}},
 {\"id\":\"$FABIEN\",\"fields\":{\"Entreprise\":\"Atelier Nord\"}}]}" | jq -c '.records[].id'
```

Si un identifiant est vide (e-mail différent côté Airtable), s'arrêter et comparer `/tmp/az_clients.txt` avec `../nocodb/exemples.md`.

## 4- Les 7 missions (API limitée à 10 enregistrements par appel)

```bash
jq -n --arg a "$ALICE" --arg b "$BOB" --arg c "$CHLOE" --arg e "$EMMA" --arg f "$FABIEN" '{typecast:false, records:[
 {fields:{Mission:"Refonte site",Client:[$a],Statut:"Terminée",Montant:2500,DateFin:"2026-03-12","Facturé":true}},
 {fields:{Mission:"Création d'\''une app mobile",Client:[$b],Statut:"Terminée",Montant:80000,DateFin:"2026-05-31","Facturé":true,NotesMigration:"Date d'\''origine mai 2026 sans jour : dernier jour du mois retenu, à confirmer."}},
 {fields:{Mission:"Maintenance page",Client:[$a],Statut:"Arrêtée",Montant:500,DateFin:"2026-06-30","Facturé":true,NotesMigration:"Date d'\''origine juin sans jour ni année : 30/06/2026 retenu, à confirmer. Facturé d'\''origine x lu comme oui, à confirmer. Doublon possible avec Maintenance (même cliente)."}},
 {fields:{Mission:"Audit",Client:[$c],Statut:"En cours",Montant:1200,DateFin:"2026-06-24","Facturé":false,NotesMigration:"Facturé d'\''origine vide lu comme non."}},
 {fields:{Mission:"Maintenance",Client:[$a],Statut:"En cours",Montant:450,DateFin:"2026-07-31","Facturé":false,NotesMigration:"Date d'\''origine Juillet sans jour ni année : 31/07/2026 retenu, à confirmer. Doublon possible avec Maintenance page (même cliente)."}},
 {fields:{Mission:"Tunnel de vente",Client:[$e],Statut:"Terminée",Montant:3200,DateFin:"2026-05-15","Facturé":false}},
 {fields:{Mission:"Atelier CRM",Client:[$f],Statut:"À faire",Montant:900,DateFin:"2026-10-30","Facturé":false,NotesMigration:"Facturé d'\''origine vide lu comme non."}}
]}' > /tmp/az_missions_records.json
curl -s "${H[@]}" -X POST "$AT/$BASE/AZ_Missions" -d @/tmp/az_missions_records.json | jq -r '.records[].id' | tee /tmp/az_missions_ids.txt
```

`typecast:false` : si une option de `Statut` n'existe pas à l'identique, l'API refuse au lieu de créer une option polluée.

Retour arrière : `curl -s "${H[@]}" -X DELETE "$AT/$BASE/AZ_Missions?$(sed 's/^/records%5B%5D=/' /tmp/az_missions_ids.txt | paste -sd'&')"`

## 5- Champs calculés, vues, formulaire

UI uniquement (API Metadata ne crée ni Count, ni Rollup, ni Lookup, ni vues) : voir `SPECS.md`. Le lien public du formulaire `Nouvelle mission` va dans `Lien_Airtable_Demo`.

## 6- Ligne `AZ_Portfolio`

```bash
python3 - <<'EOF' > /tmp/az_portfolio_row.json
import csv, json
r = next(csv.DictReader(open("../import-csv/missions-ca-par-client-portfolio-row.csv")))
f = {k: v for k, v in r.items() if v != "" and k != "Captures"}
f["Numéro"] = int(f["Numéro"]); f["Semaine"] = int(f["Semaine"])
print(json.dumps({"typecast": False, "records": [{"fields": f}]}, ensure_ascii=False))
EOF
curl -s "${H[@]}" -X POST "$AT/$BASE/AZ_Portfolio" -d @/tmp/az_portfolio_row.json | jq -r '.records[0].id // .'
```

`typecast:false` protège `Client`, `Statut` et `AnnéeSaison` des options polluées : les 3 valeurs doivent déjà exister (`Freelance / prestataire de services`, `En cours`, `2026-Automne`).

Retour arrière : `curl -s "${H[@]}" -X DELETE "$AT/$BASE/AZ_Portfolio/<recId>"`

Quand le formulaire est créé et le déploiement Vercel vérifié : PATCH `Statut` = `Livré`, `Lien_Airtable_Demo` = URL du formulaire, `Lien_NocoDB_Demo` = URL du formulaire NocoDB `Nouvelle mission`.
