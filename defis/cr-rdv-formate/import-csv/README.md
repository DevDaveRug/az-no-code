# Import CSV -- raccourci portfolio pour cr-rdv-formate (défi 2)

1 CSV prêt à importer pour enregistrer le projet dans la table méta `AZ_Portfolio`.

## Cas particulier du défi 2

Contrairement aux autres défis, le défi 2 (`cr-rdv-formate`) **ne livre pas de CSV de données** (pas de table à peupler avec des exemples) : le livrable central est un prompt LLM réutilisable (`PROMPT_LLM.md` dans `az-code/defis/cr-rdv-souverain/`) et un runbook Airtable qui **étend** la base `Sales Closer Souverain` existante avec une table `SC_CRs_de_RDV` linkée à `SC_Prospects` (défi 1).

C'est pour ça que ce dossier `import-csv/` contient uniquement `cr-rdv-formate-portfolio-row.csv` -- pas de `cr.csv` ni équivalent.

## Fichier livré

- `cr-rdv-formate-portfolio-row.csv` : 1 ligne prête à ajouter dans la table `AZ_Portfolio` (raccourci Étape 8 du skill `/defi-hebdo-alegria`, rétroactif S138z-ccdd, volet C IDEE_infra_198). Nom préfixé par slug (conforme code_archi R4, évite collision au téléchargement).

## Ajouter la ligne AZ_Portfolio (raccourci Étape 8 du skill)

`cr-rdv-formate-portfolio-row.csv` contient les 17 champs remplis (`Captures` reste vide). `Lien_Airtable_Demo` pointe vers le formulaire public "Ajouter un CR de RDV" (`pag5VRfyhBBi1lKfy/form`) : accès direct sans authentification, préféré au partage Interface Dashboard qui exige un login préalable. `Lien_NocoDB_Demo` est vide (écart S136z-E, NocoDB pas encore déployé, IDEE_infra_196). Preview_Vercel = URL Vercel par défaut, custom domain à basculer plus tard (backlog IDEAS_PRO).

Deux voies pour l'ajouter :

1. **Import en ajout** : extension `CSV Import` (Extensions -> Add extension -> CSV Import -> table cible `AZ_Portfolio` -> Upload -> Match fields -> Import).

2. **Copier-coller manuel** (fallback 30 sec) : ouvrir `cr-rdv-formate-portfolio-row.csv` dans un tableur, copier la ligne, coller dans une nouvelle ligne vide de `AZ_Portfolio`.

Après import : `DateLivraison` = `2026-09-10` doit être Date, `Statut` = `Livré` doit être SingleSelect, `Numéro` = 2 et `Semaine` = 37 doivent être Number, `Repo_Code` doit pointer vers `cr-rdv-souverain` (le slug du code souverain diffère du slug no-code).

## Compagnon

- Prompt central : `az-code/defis/cr-rdv-souverain/PROMPT_LLM.md`

- Runbook Airtable : `../RUNBOOK.md`

- Interface unifiée : `../interfaces/AZ_PORTFOLIO_INTERFACE.md` (rattrape défi 1 + livre défi 2)
