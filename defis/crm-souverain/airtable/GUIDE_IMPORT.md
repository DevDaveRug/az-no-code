# Guide pas à pas -- Créer la base Airtable CRM Souverain (défi Alegria Eva PRO)

Version : 1.1.0
Date : 2026-09-03
Session : S132c
Public : David, à partir de zéro sur airtable.com, sans expérience préalable de l'API Airtable.

Deux voies possibles :

-> **Voie A -- Import via API Airtable Metadata** (rapide, ~10 min, nécessite un Personal Access Token, script PowerShell natif fourni)

-> **Voie B -- Création manuelle depuis SPECS.md** (~25 min, aucun token nécessaire)

Les deux voies aboutissent au même résultat. Labels d'interface donnés en français (l'UI Airtable de David est configurée en français, voir Cor David S131c).

## Voie A -- Import via API Airtable Metadata

### Étape 1 -- Créer un compte Airtable si tu n'en as pas

-> https://airtable.com/signup

-> Plan gratuit suffisant pour ce défi (limite : 1200 lignes par base, aucun problème pour 4 exemples)

### Étape 2 -- Créer une base vide dans un espace de travail

-> Aller sur https://airtable.com/

-> Cliquer sur `+ Créer une base` -> `Repartir de zéro`

-> Nommer la base : `CRM Souverain`

-> Choisir un espace de travail (par défaut : `Espace de travail personnel`)

-> La base contient une table `Table 1` par défaut, à SUPPRIMER après l'import (voir étape 5)

-> Noter le `baseId` de la base :

  1. Ouvrir la base

  2. Regarder l'URL dans le navigateur, elle a le format : `https://airtable.com/appXXXXXXXXXX/tblYYYYYYY/...`

  3. La partie `appXXXXXXXXXX` est le `baseId`. Le noter dans un fichier temporaire.

### Étape 3 -- Créer un Personal Access Token

-> Aller sur https://airtable.com/create/tokens

-> Cliquer sur `+ Créer un nouveau jeton`

-> Nom : `CRM Souverain S132c` (ou ce que tu veux)

-> Scopes (permissions) à cocher :

  -> `data.records:read`

  -> `data.records:write`

  -> `schema.bases:read`

  -> `schema.bases:write`

-> Accès : `Ajouter une base` -> sélectionner la base `CRM Souverain`

-> Cliquer `Créer un jeton`

-> **COPIER LE JETON AFFICHÉ** (il ne sera plus visible après). Format : `patXXXXXXXXXXXXXX.YYYYYYYYYYYYYY...`

-> Le mettre dans le fichier temporaire.

### Étape 4 -- Importer le schéma via API (script PowerShell natif)

**Recommandé pour PowerShell 7 (le shell par défaut sur Windows).** Le script `import.ps1` ci-dessous utilise `Invoke-RestMethod` natif, pas de heredoc bash (l'ancien script bash cassait en PowerShell 7 -- blocage S131c).

Créer le fichier `import.ps1` dans `C:\Users\conta\dev\az-no-code\defis\crm-souverain\airtable\` avec le contenu suivant :

```powershell
# import.ps1 -- creation table SC_Prospects via API Airtable Metadata
# Usage : .\import.ps1 -BaseId "appXXX" -Token "patXXX.YYY"

param(
    [Parameter(Mandatory=$true)] [string]$BaseId,
    [Parameter(Mandatory=$true)] [string]$Token
)

$Uri = "https://api.airtable.com/v0/meta/bases/$BaseId/tables"

$Headers = @{
    "Authorization" = "Bearer $Token"
    "Content-Type"  = "application/json; charset=utf-8"
}

$Body = @{
    name        = "SC_Prospects"
    description = "Prospects avec statut et relance a 7 jours (defi crm-souverain, origine Alegria)"
    fields      = @(
        @{ name = "Prenom";            type = "singleLineText" },
        @{ name = "Nom";               type = "singleLineText" },
        @{ name = "Entreprise";        type = "singleLineText" },
        @{ name = "Email";             type = "email" },
        @{ name = "Telephone";         type = "phoneNumber" },
        @{ name = "Statut";            type = "singleSelect"; options = @{ choices = @(
            @{ name = "Nouveau" }, @{ name = "En cours" }, @{ name = "Gagne" }, @{ name = "Perdu" }
        ) } },
        @{ name = "Source";            type = "singleSelect"; options = @{ choices = @(
            @{ name = "LinkedIn" }, @{ name = "Recommandation" }, @{ name = "Site" }, @{ name = "Autre" }
        ) } },
        @{ name = "Date entree";       type = "date"; options = @{ dateFormat = @{ name = "iso" } } },
        @{ name = "Dernier contact";   type = "date"; options = @{ dateFormat = @{ name = "iso" } } },
        @{ name = "Notes";             type = "multilineText" },
        @{ name = "Montant potentiel"; type = "currency"; options = @{ precision = 2; symbol = "EUR" } }
    )
} | ConvertTo-Json -Depth 10

try {
    $Response = Invoke-RestMethod -Uri $Uri -Method Post -Headers $Headers -Body $Body
    Write-Host "OK -- Table creee : $($Response.name)" -ForegroundColor Green
    Write-Host "     Table id     : $($Response.id)" -ForegroundColor Green
    Write-Host "     Champs (nb)  : $($Response.fields.Count)"
} catch {
    Write-Host "ERREUR API Airtable :" -ForegroundColor Red
    Write-Host $_.Exception.Message
    if ($_.ErrorDetails.Message) { Write-Host $_.ErrorDetails.Message }
    exit 1
}
```

Lancer depuis PowerShell 7 :

```powershell
cd C:\Users\conta\dev\az-no-code\defis\crm-souverain\airtable
.\import.ps1 -BaseId "appXXXXXXXXXX" -Token "patXXX.YYY..."
```

Réponse attendue en vert : `OK -- Table creee : SC_Prospects` + son `id` (`tblZZZZZZZ`). Noter cet id, on va s'en servir juste après.

Note : le script écrit les accents en ASCII (`Gagne`, `Date entree`, `EUR`) pour éviter les soucis d'encodage entre PowerShell 7 et l'API Airtable. Une fois la table créée, RENOMMER manuellement dans l'UI Airtable :

-> `Gagne` -> `Gagné` (dans le champ `Statut`, éditer l'option)

-> `Date entree` -> `Date entrée` (éditer le nom du champ)

-> Symbole EUR -> `€` (éditer le champ `Montant potentiel`, format monétaire)

2 minutes de retouches, propre après.

Note bis : les champs formule (`Nom complet`, `Prochaine relance`, `Jours restants`) sont ajoutés manuellement à l'étape 5 -- l'API Airtable Metadata ne supporte PAS la création de formulas via `fields.type = "formula"` (limitation Airtable connue).

### Étape 5 -- Ajouter les champs formule à la main + finaliser

Dans l'interface Airtable, ouvrir la table `SC_Prospects` :

-> Ajouter un champ à la fin (`+` en bout de ligne d'en-tête) :

  -> Nom : `Nom complet`

  -> Type : `Formule`

  -> Formule : `{Prenom} & " " & {Nom}`

-> Ajouter un autre champ :

  -> Nom : `Prochaine relance`

  -> Type : `Formule`

  -> Formule : `IF(OR({Statut}='Nouveau',{Statut}='En cours'), DATEADD({Dernier contact}, 7, 'days'), BLANK())`

  -> Format d'affichage : Date (iso)

-> Ajouter un autre champ :

  -> Nom : `Jours restants`

  -> Type : `Formule`

  -> Formule : `IF({Prochaine relance}, DATETIME_DIFF({Prochaine relance}, TODAY(), 'days'), BLANK())`

-> Faire du champ `Nom complet` le champ principal (clic droit sur la colonne -> `Définir comme champ principal`)

-> **Supprimer la table par défaut `Table 1`** (clic droit sur son onglet -> `Supprimer la table`)

### Étape 6 -- Créer les 4 vues (Grille / À relancer / Calendrier / Kanban)

Voir `airtable/SPECS.md` section Vues pour les filtres exacts. En résumé (labels UI FR) :

-> Vue par défaut `Vue grille` : renommer en `Tous les prospects`, tri par `Date entrée` décroissant

-> Nouvelle vue `Grille` : nommer `À relancer`, filtres :

  -> `Statut` est `Nouveau` OU `En cours`

  -> ET `Prochaine relance` est `Aujourd'hui ou avant`

  -> Tri : `Prochaine relance` croissant

-> Nouvelle vue `Calendrier` : nommer `Relances`, champ date = `Prochaine relance`, filtre : `Statut` est Nouveau, En cours

-> Nouvelle vue `Kanban` : nommer `Pipeline`, grouper par `Statut`

### Étape 7 -- Injecter les 4 lignes d'exemples anonymisés

Ouvrir `airtable/exemples.md` dans un onglet à côté, puis dans Airtable (vue `Tous les prospects`) :

-> Alice Martin (Nouveau, dernier contact 2026-08-25)

-> Bob Durand (En cours, dernier contact 2026-08-20)

-> Chloé Dubois (Gagné, dernier contact 2026-08-30)

-> Emma Petit (Perdu, dernier contact 2026-08-22)

Vérifier après injection : la vue `À relancer` doit afficher **Bob (rouge, -6)** et **Alice (orange, -1)**.

### Étape 8 -- Créer le formulaire d'ajout

Dans la table `SC_Prospects` :

-> Clic sur `+ Créer...` en bas à gauche de la liste des vues -> `Formulaire`

-> Nommer : `Ajouter un prospect`

-> Champs visibles à cocher (dans l'ordre) : Prenom, Nom, Entreprise, Email, Telephone, Source, Notes

-> Champs cachés (auto-remplis à la création) : Statut (défaut Nouveau), Date entrée (défaut aujourd'hui), Dernier contact (défaut aujourd'hui)

-> Message post-soumission : `Prospect ajouté. Tu peux le suivre depuis la vue Tous les prospects.`

-> Cliquer `Partager le formulaire` en haut à droite pour obtenir l'URL publique (à partager avec Eva PRO ou intégrer sur un site).

### Étape 9 -- Créer l'automatisation email hebdo

Dans la base, panneau gauche -> `Automatisations` -> `+ Créer une automatisation` :

-> Nom : `Email hebdo - Prospects à relancer`

-> Déclencheur : `À l'heure planifiée` -> Hebdomadaire / Lundi / 09:00 (Europe/Paris)

-> Étape 1 -- Ajouter une action : `Trouver des enregistrements`

  -> Table : `SC_Prospects`

  -> Vue : `À relancer`

-> Étape 2 -- Ajouter une action : `Envoyer un email`

  -> Destinataire : ton email (test) ou `owner@exemple.com`

  -> Sujet : `[CRM] Prospects à relancer cette semaine`

  -> Corps : voir SPECS.md pour le template Handlebars complet

-> `Tester l'action` puis `Activer l'automatisation`

### Étape 10 -- Prendre les captures d'écran

Sauvegarder dans `az-no-code/defis/crm-souverain/captures/` (nommage code_archi respecté, cf. captures S131c déjà déposées) :

-> `YYMMDD_DefiAlegria_CRM-Souverain_Tab_Prospects.png` -- Vue "Tous les prospects" avec les 4 exemples

-> `YYMMDD_DefiAlegria_CRM-Souverain_Table_ARelancer.png` -- Vue "À relancer" avec Bob (rouge) + Alice (orange)

-> `YYMMDD_DefiAlegria_CRM-Souverain_Cal_Relances.png` -- Vue calendrier avec relances

-> `YYMMDD_DefiAlegria_CRM-Souverain_Kanban_Pipeline.png` -- Vue Kanban avec les 4 colonnes remplies

-> `YYMMDD_DefiAlegria_CRM-Souverain_Form_AjoutProspect.png` -- Vue du formulaire d'ajout

-> `YYMMDD_DefiAlegria_CRM-Souverain_Autom_EmailHebdo.png` -- Config de l'automatisation email hebdo

## Voie B -- Création manuelle depuis SPECS.md

Si tu préfères sans API (pas de token à créer, ni de PowerShell à lancer), suis directement `airtable/SPECS.md` section par section :

-> Table SC_Prospects -> section "Table SC_Prospects" (13 champs à créer)

-> Vues -> section "Vues" (4 vues)

-> Formulaire -> section "Formulaire d'ajout"

-> Automatisation -> section "Automatisation"

Puis exemples et captures = étapes 7 et 10 ci-dessus.

## Rappel important

Les 4 exemples sont **entièrement fictifs**. Ne pas mettre tes vraies infos ni celles d'Eva PRO ou d'un vrai prospect.

## Cas de blocage

Si un champ formule refuse d'être créé (erreur Airtable), vérifier que TOUS les champs référencés (Prenom, Nom, Statut, Dernier contact) existent déjà et sont écrits EXACTEMENT comme dans la formule (majuscules/accents).

Si l'API renvoie `INVALID_REQUEST_UNKNOWN`, vérifier que le token a bien le scope `schema.bases:write` et que la base a bien été ajoutée à ses accès.

Si la vue `À relancer` reste vide alors qu'il devrait y avoir Alice et Bob, vérifier :

-> Que la date du jour est bien postérieure au dernier contact de Bob + 7 jours

-> Que le champ `Prochaine relance` est bien calculé (voir formule étape 5)

-> Que le filtre utilise bien `Aujourd'hui ou avant` (pas `est aujourd'hui`)

Si `Invoke-RestMethod` renvoie une erreur d'encodage sur les accents dans PowerShell 7, vérifier que le script `import.ps1` est bien enregistré en UTF-8 sans BOM (`Notepad++ -> Encodage -> UTF-8 (sans BOM)`), et que le header `Content-Type` inclut bien `charset=utf-8`.

## Après livraison Eva PRO

Une fois les captures faites et livrées à Alegria, le repo garde la trace. Pour un vrai usage sur des vrais prospects, il faudra :

-> Créer une nouvelle base ou dupliquer celle-ci

-> Effacer les 4 exemples

-> Ajouter les vraies infos David / prospects réels

C'est aussi le moment de tester la Voie C : bascule vers NocoDB self-host (voir `nocodb/SPECS.md`) ou vers le MVP code souverain Next.js (`az-code/defis/crm-souverain/README.md`).

## Changelog

-> 1.1.0 (2026-09-03, S132c) : Voie A étape 4 réécrite avec script PowerShell natif `import.ps1` (Invoke-RestMethod, plus de heredoc bash cassé en PowerShell 7 -- blocage S131c). Labels UI Airtable passés en français partout (Cor David S131c : UI Airtable en FR). Étape 10 : nommage captures aligné code_archi (`YYMMDD_DefiAlegria_CRM-Souverain_[Type]_[Sujet].png`) au lieu des noms génériques `01_base_grille.png`. Header version + changelog ajoutés (règle S60 BLOQUANTE). Cas de blocage étendu avec astuce encodage UTF-8 pour PowerShell 7. Fichier récupéré depuis commit orphelin a4e4c0a (non mergé sur main après squash PR #1).

-> 1.0.0 (2026-09-01, S131c) : version initiale (commit a4e4c0a, orphelin sur branche `claude/s131c-rename-crm-souverain`, jamais fusionné sur main).
