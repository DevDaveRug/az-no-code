# Exemples de données AZ_Inscrits (Airtable)

Toutes les infos sont **fictives** SAUF la ligne 5 (David lui-même) qui est demandée explicitement par l'énoncé "Pour tester, ajoute-toi toi-même comme inscrit avec ta propre adresse mail".

Date de référence : `TODAY() = 2026-09-14` (lundi de la semaine 38).

Les 3 dates de masterclass utilisées ci-dessous permettent de bien montrer le groupement par date :

-> `2026-10-14` (masterclass mercredi 14 octobre)

-> `2026-10-21` (masterclass mercredi 21 octobre)

-> `2026-11-05` (masterclass jeudi 5 novembre)

## Ligne 1 - Alice Martin (masterclass 14/10)

| Champ | Valeur |
|---|---|
| Prenom | Alice |
| Email | alice.martin@legrand.example |
| DateMasterclass | 2026-10-14 |
| StatutEmail | Envoyé (rempli auto par l'automation) |
| Notes | Inscrite via LinkedIn |

## Ligne 2 - Bob Durand (masterclass 14/10)

| Champ | Valeur |
|---|---|
| Prenom | Bob |
| Email | b.durand@techflow.example |
| DateMasterclass | 2026-10-14 |
| StatutEmail | Envoyé |
| Notes | Inscrit via le site |

## Ligne 3 - Chloé Dubois (masterclass 21/10)

| Champ | Valeur |
|---|---|
| Prenom | Chloé |
| Email | c.dubois@zenith.example |
| DateMasterclass | 2026-10-21 |
| StatutEmail | Envoyé |
| Notes | Recommandation d'un ancien client |

## Ligne 4 - Emma Petit (masterclass 05/11)

| Champ | Valeur |
|---|---|
| Prenom | Emma |
| Email | e.petit@marketpro.example |
| DateMasterclass | 2026-11-05 |
| StatutEmail | En attente (test : ne pas cocher, laisser l'automation faire) |
| Notes | Inscrite juste avant la démo Alegria |

## Ligne 5 - David Ruggieri (test explicitement demandé par l'énoncé)

| Champ | Valeur |
|---|---|
| Prenom | David |
| Email | david@salescloser.fr |
| DateMasterclass | 2026-10-14 |
| StatutEmail | En attente au moment de l'ajout, doit passer à Envoyé après trigger |
| Notes | Test défi Alegria semaine 38 - David s'inscrit lui-même via le formulaire pour prouver que l'automation marche |

## Vérification vue "Par masterclass"

Après import, le regroupement doit afficher :

-> Groupe `2026-10-14` : Alice, Bob, David (3 lignes)

-> Groupe `2026-10-21` : Chloé (1 ligne)

-> Groupe `2026-11-05` : Emma (1 ligne)

## Vérification vue "Erreurs d'envoi"

Doit être **vide** en démo -- si une ligne y apparaît, c'est qu'un email n'est pas parti (compte Airtable non vérifié, quota atteint, adresse invalide...).

## Preuve du défi (capture 06)

David reçoit vraiment un email sur `david@salescloser.fr` avec le sujet `Ta place pour la masterclass du 2026-10-14 est confirmée` et le corps commençant par `Salut David,`. C'est cette capture qui prouve à Alegria que l'automation fonctionne.
