# Exemples de données SC_Prospects (NocoDB)

Mêmes 4 lignes que la version Airtable, adaptées au vocabulaire NocoDB (ProchaineRelance en un mot, DateEntree sans accent).

Toutes fictives. Date de référence : `NOW() = 2026-09-01`.

## Ligne 1 - Alice Martin (Nouveau, relance aujourd'hui)

- Prenom : Alice

- Nom : Martin

- Entreprise : Cabinet Legrand

- Email : alice.martin@legrand.example

- Telephone : +33 6 00 00 00 12

- Statut : Nouveau

- Source : LinkedIn

- DateEntree : 2026-08-25

- DernierContact : 2026-08-25

- ProchaineRelance : 2026-09-01 (calculé)

- JoursRestants : 0 (calculé)

- Notes : A répondu au DM LinkedIn. Rappeler cette semaine.

- MontantPotentiel : (vide)

## Ligne 2 - Bob Durand (En cours, en retard 5 jours)

- Prenom : Bob

- Nom : Durand

- Entreprise : TechFlow SAS

- Email : b.durand@techflow.example

- Telephone : +33 6 00 00 00 34

- Statut : En cours

- Source : Recommandation

- DateEntree : 2026-08-10

- DernierContact : 2026-08-20

- ProchaineRelance : 2026-08-27 (calculé)

- JoursRestants : -5 (calculé, rouge)

- Notes : Attend un devis fin de mois. Sensible au prix.

- MontantPotentiel : 1500

## Ligne 3 - Chloé Dubois (Gagné)

- Prenom : Chloé

- Nom : Dubois

- Entreprise : Studio Zenith

- Email : c.dubois@zenith.example

- Telephone : +33 6 00 00 00 56

- Statut : Gagné

- Source : Site

- DateEntree : 2026-08-05

- DernierContact : 2026-08-30

- ProchaineRelance : (vide, statut gagné)

- JoursRestants : (vide)

- Notes : Contrat signé le 30/8. Onboarding sem du 8/9.

- MontantPotentiel : 2500

## Ligne 4 - Emma Petit (Perdu)

- Prenom : Emma

- Nom : Petit

- Entreprise : MarketPro

- Email : e.petit@marketpro.example

- Telephone : +33 6 00 00 00 78

- Statut : Perdu

- Source : LinkedIn

- DateEntree : 2026-08-08

- DernierContact : 2026-08-22

- ProchaineRelance : (vide)

- JoursRestants : (vide)

- Notes : Pas de budget. À rappeler début 2027.

- MontantPotentiel : (vide)

## Vérification vue "À relancer"

Doit afficher 2 lignes :

1. Bob Durand (rouge, -5)

2. Alice Martin (orange, 0)
