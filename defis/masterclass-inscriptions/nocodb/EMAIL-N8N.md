# E-mail de confirmation masterclass (NocoDB -> n8n -> SMTP IONOS)

Décisions David (05/10/2026) : expéditeur `david@salescloser.fr` via SMTP IONOS (pas de compte Resend retrouvé), webhook NocoDB vers n8n `sb-n8n.coolify.salescloser.fr`, test réel validé (inscription à l'adresse de David, puis ligne supprimée).

Workflow prêt à importer : `n8n/masterclass-inscrit-cree.json` (6 nœuds).

```
Webhook NocoDB (en-tête secret) -> Contrôle et nettoyage -> Envoi autorisé ?
   oui -> E-mail de confirmation -> succès : StatutEmail = Envoye
                                 -> échec  : StatutEmail = Erreur
   non -> StatutEmail = Erreur
```

Contrôles du nœud « Contrôle et nettoyage » (testés hors n8n le 05/10) :

-> e-mail valide, 120 caractères au plus, domaines réservés refusés (`.example`, `.test`, `.invalid`, `.localhost`)

-> prénom : balises et liens retirés, lettres, espace, tiret, apostrophe uniquement, 3 mots et 40 caractères au plus

-> date `AAAA-MM-JJ` convertie en `JJ/MM/AAAA`, refus si absente

-> 30 envois par heure au plus (protection contre l'envoi en rafale via le formulaire public)

## 1- À faire par David (une fois)

1- Variables d'environnement de la session Claude Code (menu de l'environnement, `Edit`) : `N8N_API_URL_SB` et `N8N_API_KEY_SB`. Jamais dans le chat. Une nouvelle session les voit.

2- Dans n8n (UI) : `Credentials` -> `Create` -> `SMTP`, nom exact `SMTP IONOS - david@salescloser.fr` :

-> Host `smtp.ionos.fr`, Port `465`, SSL/TLS activé

-> User `david@salescloser.fr`, mot de passe de la boîte (saisi dans n8n uniquement)

## 2- À faire par la session suivante (API)

Lire avant d'écrire, GET de contrôle après chaque écriture, trace dans `defis/missions-ca-par-client/nocodb/API.md` (§13), aucun secret dans le dépôt.

1- `GET $N8N_API_URL_SB/api/v1/workflows` (en-tête `X-N8N-API-KEY`) : accès et absence d'un workflow `AZ_Masterclass_inscrit_cree`.

2- Créer 2 credentials par l'API (`POST /api/v1/credentials`, type `httpHeaderAuth`) :

-> `NocoDB webhook secret (masterclass)` : en-tête `X-NocoDB-Secret`, valeur générée (`openssl rand -hex 24`), jamais affichée

-> `NocoDB xc-token (sb)` : en-tête `xc-token`, valeur `$NOCODB_API_TOKEN`

3- Retrouver l'id de la credential SMTP créée par David (via un workflow existant qui l'utilise, ou demander l'id à David s'il n'est pas lisible par l'API).

4- Remplacer `__CRED_WEBHOOK_SECRET__`, `__CRED_NOCODB__`, `__CRED_SMTP__` dans une copie locale du JSON, `POST /api/v1/workflows`, puis `POST /api/v1/workflows/<id>/activate`.

5- Webhook NocoDB sur `AZ_Inscrits` (`ml25u20dkb9gfxm`) : `After Insert`, `POST https://sb-n8n.coolify.salescloser.fr/webhook/masterclass-inscrit-cree`, en-tête `X-NocoDB-Secret` = même valeur. Lire la méta des hooks avant pour le format exact du payload de la version `2026.09.0`.

6- Test : une inscription par le formulaire public (`#/nc/form/e3eb2619-338b-4451-9c9e-57cd07ed13e8`) avec `david@salescloser.fr`. Attendu : e-mail reçu (David confirme), `StatutEmail` = `Envoye`. Puis 1 envoi avec `test@exemple.example` : attendu `StatutEmail` = `Erreur`, aucun e-mail. Supprimer les 2 lignes de test.

## 2-bis- Fait le 06/10/2026 (S170z-ccweb) : en service

Le §2 est exécuté, la chaîne fonctionne. Identifiants et trace complète : `defis/missions-ca-par-client/nocodb/API.md` §13.

**La configuration du hook NocoDB qui marche**, en 2026.09.0 :

```
version    : "v3"                   (v2 et l'absence de version sont refusees)
operation  : ["insert"]              (un TABLEAU, pas une chaine)
headers    : [{"name":"X-NocoDB-Secret","value":"<secret>","enabled":true}]
body       : "{{ json data }}"
```

Les trois pièges, aucun ne produisant d'erreur lisible :

-> `operation` en chaîne est rejeté par la validation, `version` absente ou `"v2"` aussi

-> un en-tête **sans `"enabled": true` est supprimé en silence** : la requête part sans le secret, n8n répond `403`. C'est le piège principal, et celui qui ressemble le plus à une panne de workflow

-> **sans gabarit `body`, NocoDB n'envoie aucun corps** ; `{{ json payload }}` envoie une chaîne vide. Seul `{{ json data }}` porte la ligne

Charge effectivement reçue, conforme à ce que lit le nœud `Contrôle et nettoyage` (`$json.body.data.rows`) :

```
{"type":"records.after.insert","id":"<uuid>","base_id":"phwalskbrftv4o4","version":"v3",
 "data":{"table_id":"ml25u20dkb9gfxm","table_name":"AZ_Inscrits","rows":[{...}]}}
```

**Les deux outils à utiliser d'emblée la prochaine fois** : `GET /api/v2/meta/hooks/<hookId>/logs` (NocoDB journalise les en-têtes réellement envoyés et la réponse reçue, c'est là qu'on voit le 403) et `POST /api/v2/meta/tables/<tableId>/hooks/test` (appel de test sans créer de ligne, pour itérer sur la configuration). Côté n8n, mettre `saveDataSuccessExecution` et `saveDataErrorExecution` à `all` : sans ça, « 0 exécution » se lit à tort comme « webhook non appelé ».

Tests validés : `test@exemple.example` donne `StatutEmail = Erreur` sans qu'aucun e-mail ne parte, `david@salescloser.fr` donne `Envoye` avec le nœud d'envoi exécuté. Les 2 lignes de test sont supprimées, `AZ_Inscrits` est rendue à ses 5 lignes d'origine.

Fait : e-mail de confirmation masterclass reçu dans la boîte david@salescloser.fr

## Retour arrière

-> NocoDB : `DELETE $NC/api/v2/meta/hooks/<hookId>` (stoppe tout envoi immédiatement)

-> n8n : `POST /api/v1/workflows/<id>/deactivate`, puis `DELETE /api/v1/workflows/<id>` et les 2 credentials créées

## Points d'attention

-> Les inscrits existants (n°4 Emma, n°5 Fabien, `En attente`) ne reçoivent rien : le webhook ne réagit qu'aux nouvelles lignes.

-> `StatutEmail` s'écrit `Envoye` (sans accent) dans NocoDB.

-> Délivrabilité : vérifier que SPF et DKIM de `salescloser.fr` autorisent IONOS (déjà le cas si la boîte envoie normalement).
