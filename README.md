# az-no-code

Solutions no-code (Airtable + NocoDB + alternatives) pour les défis Alegria + prospects + clients futurs.

Compagnon code souverain : [DevDaveRug/az-code](https://github.com/DevDaveRug/az-code)

## Pourquoi ce repo

Le no-code livre vite et démontre le résultat. Ce repo garde :

-> les schémas Airtable (structure des bases, vues, formulaires, automatisations)

-> les schémas NocoDB (équivalent souverain sur infra Coolify)

-> les captures d'écran des livrables (rendu Alegria)

-> les alternatives testées (Notion, Baserow, etc.) au fur et à mesure

Chaque défi hebdo se livre AUSSI en code souverain dans [DevDaveRug/az-code](https://github.com/DevDaveRug/az-code).

## Structure

```
defis/
  eva-pro-crm-relance/           # défi actuel, livraison 5/9/2026
    README.md                    # énoncé + livrables + timeline
    airtable/
      SPECS.md                   # tables, vues, formulaire, automatisation
      schema.json                # export/import schema Airtable
      exemples.md                # 4 lignes anonymisées + 1 dans vue "à relancer"
    nocodb/
      SPECS.md                   # équivalent NocoDB self-host
      exemples.md
    captures/                    # captures d'écran (David remplit après création)
      .gitkeep
```

## Défis livrés

| Défi | Slug | Livraison | État |
|---|---|---|---|
| Eva PRO - CRM prospects avec relance 7 jours | `eva-pro-crm-relance` | 2026-09-05 | specs prêtes |

## Convention exemples

Les tables d'exemples illustrent tous les statuts + au moins un cas dans chaque vue conditionnelle. Tous les noms, entreprises, téléphones, emails sont fictifs, jamais les vrais infos de David ou d'un prospect réel.

Prénoms génériques utilisés : Alice, Bob, Chloé, Emma, Fabien, Gabrielle, etc.
Entreprises génériques : Cabinet Legrand, TechFlow SAS, Studio Zenith, MarketPro, etc.
Domaines emails : `*.example` (RFC 2606, réservé aux exemples).

## Convention nommage

-> slug défi kebab-case (ex : `eva-pro-crm-relance`)

-> tables préfixées par domaine : `SC_` (Sales Closer), `SB_` (Second Brain), `AZ_` (interne Alegria)

## Skill de génération

Un skill `defi-hebdo-alegria` (dans `dr-context/.claude/skills/`) génère le squelette de chaque nouveau défi (specs Airtable + NocoDB en miroir).

Trigger : `PAUSE_Defi_Hebdo_Alegria <énoncé du défi>`
