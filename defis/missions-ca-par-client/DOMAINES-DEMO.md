# Domaines de démonstration `*.demo.salescloser.fr` (Vercel)

Exécution du 29/09/2026, session fille S163z (P3). DNS fait par David : CNAME `*.demo.salescloser.fr` -> `cname.vercel-dns.com`. Aucune modification DNS, aucune variable d'environnement créée ni lue, aucun déploiement lancé.

Jeton : `VERCEL_API_TOKEN` absent de l'environnement, `VERCEL_TOKEN` présent et utilisé (même usage), jamais affiché. Équipe `david-ruggieris-projects` (`team_pqYrSzX7EybdRdAZBgZ7Wl1b`).

```bash
V=https://api.vercel.com
T=teamId=team_pqYrSzX7EybdRdAZBgZ7Wl1b
VH=(-H "Authorization: Bearer $VERCEL_TOKEN" -H "Content-Type: application/json")
```

## Résultat

| Projet Vercel | Id | Domaine | Config Vercel | HTTPS | Preview_Vercel avant (Airtable) | Preview_Vercel avant (CSV) |
|---|---|---|---|---|---|---|
| crm-souverain (1) | `prj_SBkVYFXoVXkja0mQ2aCAdpHer9s0` | https://crm-souverain.demo.salescloser.fr | CNAME, OK | 200 | `https://crm-souverain.vercel.app/` | `https://crm-souverain-david-ruggieris-projects.vercel.app` |
| cr-rdv-souverain (2) | `prj_4jAAtVNfCVWfvoX7kgJqAs2WB0dO` | https://cr-rdv-souverain.demo.salescloser.fr | CNAME, OK | 200 | `https://cr-rdv-souverain.vercel.app/` | `https://cr-rdv-souverain.vercel.app` |
| masterclass-inscriptions (3) | `prj_2r2XSQ2JDxKsHjqNPugi8rq8QOOQ` | https://masterclass-inscriptions.demo.salescloser.fr | CNAME, OK | 200 | `https://masterclass-inscriptions-olive.vercel.app/` | `https://masterclass-inscriptions-olive.vercel.app/` |
| demandes-clients-urgentes (4) | `prj_0ngMeO7gCnHPaRaKZ3M01iGfdqCc` | https://demandes-clients-urgentes.demo.salescloser.fr | CNAME, OK | 307 | `https://demandes-clients-urgentes.vercel.app` | `https://demandes-clients-urgentes.vercel.app` |
| missions-ca-par-client (5) | `prj_vGF9hiYfJ5emvsw5c8sMTpDI29m6` | https://missions-ca-par-client.demo.salescloser.fr | CNAME, OK | 404 (aucun déploiement, attendu) | ligne créée ce jour | `https://missions-ca-par-client.vercel.app` |

Certificats émis immédiatement (vérification TLS OK au premier appel). Les anciens domaines `*.vercel.app` restent attachés aux projets.

## 1- Domaines des projets 1 à 4

```bash
for pn in prj_SBkVYFXoVXkja0mQ2aCAdpHer9s0:crm-souverain prj_4jAAtVNfCVWfvoX7kgJqAs2WB0dO:cr-rdv-souverain \
          prj_2r2XSQ2JDxKsHjqNPugi8rq8QOOQ:masterclass-inscriptions prj_0ngMeO7gCnHPaRaKZ3M01iGfdqCc:demandes-clients-urgentes; do
  p=${pn%%:*}; n=${pn##*:}
  curl -s "${VH[@]}" -X POST "$V/v10/projects/$p/domains?$T" -d "{\"name\":\"$n.demo.salescloser.fr\"}" | jq -c '{name,verified}'
done
# Contrôle
curl -s "${VH[@]}" "$V/v6/domains/<domaine>/config?$T" | jq -c '{misconfigured, configuredBy}'
curl -s -o /dev/null -w '%{http_code}\n' https://<domaine>/
```

Réponses : `verified: true` x 4 ; config `misconfigured: false`, `configuredBy: CNAME` x 4 ; HTTPS 200, 200, 200, 307.

Retour arrière : `curl -s "${VH[@]}" -X DELETE "$V/v9/projects/<id>/domains/<domaine>?$T"`

## 2- Projet `missions-ca-par-client`

```bash
curl -s "${VH[@]}" -X POST "$V/v11/projects?$T" -d '{"name":"missions-ca-par-client","framework":"nextjs",
  "rootDirectory":"defis/missions-ca-par-client","gitRepository":{"type":"github","repo":"DevDaveRug/az-code"}}'
curl -s "${VH[@]}" -X POST "$V/v10/projects/prj_vGF9hiYfJ5emvsw5c8sMTpDI29m6/domains?$T" \
  -d '{"name":"missions-ca-par-client.demo.salescloser.fr"}'
```

Réponses : projet `prj_vGF9hiYfJ5emvsw5c8sMTpDI29m6`, framework `nextjs`, rootDirectory `defis/missions-ca-par-client`, lié à `DevDaveRug/az-code` (branche de production `main`), 0 variable d'environnement ; domaine `verified: true`, config CNAME OK. 0 déploiement (GET `/v6/deployments?projectId=...`). HTTPS 404 `DEPLOYMENT_NOT_FOUND` tant que rien n'est déployé.

Retour arrière : domaine, DELETE comme ci-dessus ; projet, `curl -s "${VH[@]}" -X DELETE "$V/v9/projects/prj_vGF9hiYfJ5emvsw5c8sMTpDI29m6?$T"`.

## 3- `AZ_Portfolio.Preview_Vercel` des projets 1 à 4 (Airtable)

Les 4 domaines répondent en HTTPS : les 4 lignes mises à jour.

```bash
AT=https://api.airtable.com/v0/appTqLo3JDg7d1fak
AH=(-H "Authorization: Bearer $AIRTABLE_PAT" -H "Content-Type: application/json")
D=demo.salescloser.fr
curl -s "${AH[@]}" -X PATCH "$AT/AZ_Portfolio" -d "{\"typecast\":false,\"records\":[
 {\"id\":\"recpzC5yp61ZyqL89\",\"fields\":{\"Preview_Vercel\":\"https://crm-souverain.$D\"}},
 {\"id\":\"recemskuGIXm6vbnD\",\"fields\":{\"Preview_Vercel\":\"https://cr-rdv-souverain.$D\"}},
 {\"id\":\"recMatGGZkUBzu15V\",\"fields\":{\"Preview_Vercel\":\"https://masterclass-inscriptions.$D\"}},
 {\"id\":\"recaFrSzZd4FZl0ZO\",\"fields\":{\"Preview_Vercel\":\"https://demandes-clients-urgentes.$D\"}}]}"
```

Réponse : pas d'erreur, GET de contrôle conforme, Statut `Livré` inchangé. Retour arrière : même PATCH avec les valeurs « avant (Airtable) » du tableau.

CSV mis à jour (colonne `Preview_Vercel` seule) : `crm-souverain`, `cr-rdv-formate`, `masterclass-inscriptions`, `demandes-clients-urgentes` (`import-csv/*-portfolio-row.csv`). Retour arrière : `git revert` du commit.

## Reste à faire dans l'interface

-> Vercel, projet `missions-ca-par-client` > Settings > Environment Variables : créer `DATABASE_URL` en copiant celle de `demandes-clients-urgentes` et en remplaçant `schema=demandes` par `schema=missions`, puis Deployments > Redeploy (ou un push sur `main` du dépôt `az-code`)

-> Vérifier ensuite https://missions-ca-par-client.demo.salescloser.fr (attendu 200) avant de passer le projet 5 en `Livré`

-> Projet 1 : le CSV avait une ancienne valeur différente d'Airtable (`crm-souverain-david-ruggieris-projects.vercel.app`), les deux pointent maintenant sur le domaine de démonstration

-> Optionnel : dans chaque projet, définir le domaine `*.demo.salescloser.fr` comme domaine principal (redirection de l'ancien `*.vercel.app`)
