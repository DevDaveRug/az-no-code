# Exemples de données AZ_Inscrits (NocoDB)

Mêmes 5 lignes que `airtable/exemples.md`. À copier tel quel dans NocoDB pour rester cohérent entre les 2 démos.

## Ligne 1 - Alice Martin (masterclass 14/10)

| Field | Valeur |
|---|---|
| Prenom | Alice |
| Email | alice.martin@legrand.example |
| DateMasterclass | 2026-10-14 |
| StatutEmail | Envoyé |
| Notes | Inscrite via LinkedIn |

## Ligne 2 - Bob Durand (masterclass 14/10)

| Field | Valeur |
|---|---|
| Prenom | Bob |
| Email | b.durand@techflow.example |
| DateMasterclass | 2026-10-14 |
| StatutEmail | Envoyé |
| Notes | Inscrit via le site |

## Ligne 3 - Chloé Dubois (masterclass 21/10)

| Field | Valeur |
|---|---|
| Prenom | Chloé |
| Email | c.dubois@zenith.example |
| DateMasterclass | 2026-10-21 |
| StatutEmail | Envoyé |
| Notes | Recommandation d'un ancien client |

## Ligne 4 - Emma Petit (masterclass 05/11)

| Field | Valeur |
|---|---|
| Prenom | Emma |
| Email | e.petit@marketpro.example |
| DateMasterclass | 2026-11-05 |
| StatutEmail | En attente |
| Notes | Ajoutée juste avant la démo (laisser tourner l'automation) |

## Ligne 5 - David Ruggieri (test explicite énoncé)

| Field | Valeur |
|---|---|
| Prenom | David |
| Email | david@salescloser.fr |
| DateMasterclass | 2026-10-14 |
| StatutEmail | En attente au moment de l'ajout, doit passer à Envoyé une fois le workflow n8n exécuté |
| Notes | Test défi Alegria semaine 38 |

## Vérification vue "Par masterclass"

Regroupement attendu :

-> `2026-10-14` : Alice, Bob, David

-> `2026-10-21` : Chloé

-> `2026-11-05` : Emma

## Vérification email

David doit vraiment recevoir un email sur `david@salescloser.fr` :

-> Sujet : `Ta place pour la masterclass du 2026-10-14 est confirmée`

-> Corps commence par : `Salut David,`

C'est la capture de cet email (`nocodb_06_email_recu.png`) qui prouve que le pipeline NocoDB -> webhook -> n8n -> SMTP marche.
