# Exemples de données SC_Prospects (Airtable)

Toutes les infos sont **fictives**. À copier tel quel dans la table.

Date de référence : `TODAY() = 2026-09-01` (lundi 1er septembre 2026).

## Ligne 1 - Alice Martin (Nouveau, à relancer AUJOURD'HUI)

| Champ | Valeur |
|---|---|
| Prenom | Alice |
| Nom | Martin |
| Entreprise | Cabinet Legrand |
| Email | alice.martin@legrand.example |
| Telephone | +33 6 00 00 00 12 |
| Statut | Nouveau |
| Source | LinkedIn |
| Date entrée | 2026-08-25 |
| Dernier contact | 2026-08-25 |
| Notes | A répondu au DM LinkedIn, demande d'infos sur l'offre. Rappeler cette semaine. |
| Montant potentiel | (vide) |

Prochaine relance calculée : 2026-09-01 (soit aujourd'hui). Apparaît dans "À relancer" en orange.

## Ligne 2 - Bob Durand (En cours, EN RETARD de 5 jours)

| Champ | Valeur |
|---|---|
| Prenom | Bob |
| Nom | Durand |
| Entreprise | TechFlow SAS |
| Email | b.durand@techflow.example |
| Telephone | +33 6 00 00 00 34 |
| Statut | En cours |
| Source | Recommandation |
| Date entrée | 2026-08-10 |
| Dernier contact | 2026-08-20 |
| Notes | Envoi de la maquette prévu. Il attend un devis avant fin de mois. Sensible au prix. |
| Montant potentiel | 1500 |

Prochaine relance calculée : 2026-08-27 (soit -5 jours). Apparaît dans "À relancer" en rouge.

## Ligne 3 - Chloé Dubois (Gagné, contrat signé)

| Champ | Valeur |
|---|---|
| Prenom | Chloé |
| Nom | Dubois |
| Entreprise | Studio Zenith |
| Email | c.dubois@zenith.example |
| Telephone | +33 6 00 00 00 56 |
| Statut | Gagné |
| Source | Site |
| Date entrée | 2026-08-05 |
| Dernier contact | 2026-08-30 |
| Notes | Contrat signé le 30/8. Onboarding la semaine du 8/9. |
| Montant potentiel | 2500 |

Prochaine relance : (vide, car Statut = Gagné). N'apparaît pas dans "À relancer".

## Ligne 4 - Emma Petit (Perdu, no-go budget)

| Champ | Valeur |
|---|---|
| Prenom | Emma |
| Nom | Petit |
| Entreprise | MarketPro |
| Email | e.petit@marketpro.example |
| Telephone | +33 6 00 00 00 78 |
| Statut | Perdu |
| Source | LinkedIn |
| Date entrée | 2026-08-08 |
| Dernier contact | 2026-08-22 |
| Notes | Pas de budget cette année. À rappeler début 2027 si l'offre évolue. |
| Montant potentiel | (vide) |

Prochaine relance : (vide, car Statut = Perdu). N'apparaît pas dans "À relancer".

## Vérification vue "À relancer"

Après import des 4 lignes, la vue "À relancer" doit afficher 2 lignes dans cet ordre :

1. **Bob Durand** (rouge, `-5` jours restants) - relance à faire d'urgence

2. **Alice Martin** (orange, `0` jour restant) - relance à faire aujourd'hui

## Vérification vue "Pipeline" (Kanban)

Doit afficher 4 colonnes avec 1 carte chacune :

- Nouveau : Alice Martin

- En cours : Bob Durand

- Gagné : Chloé Dubois

- Perdu : Emma Petit
