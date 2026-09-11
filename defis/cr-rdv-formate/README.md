# Défi cr-rdv-formate (no-code)

Version : 0.5.0
Livraison v0.1 : 2026-09-08 (specs)
Livraison v0.2 : 2026-09-10 (runbook + seed CSV)
Livraison v0.3 : 2026-09-10 (architecture bases connectables)
Livraison v0.4 : 2026-09-10 (étend crm-souverain existant + Interface unifiée + 3 chemins d'usage)
Livraison v0.5 : 2026-09-10 (Airtable Trigger n8n + Email fallback, contourne le passage payant du webhook Airtable)

Compagnon no-code du défi Alegria Eva PRO du 8/9/2026 -- comptes rendus de RDV formatés en 2 colonnes.

Compagnon code souverain : [az-code/defis/cr-rdv-souverain](https://github.com/DevDaveRug/az-code/tree/main/defis/cr-rdv-souverain)

Livrable central du défi (utilisable seul, hors Airtable) : [PROMPT_LLM.md](https://github.com/DevDaveRug/az-code/blob/main/defis/cr-rdv-souverain/PROMPT_LLM.md) -- prompt réutilisable à coller dans n'importe quelle IA.

**Livrable construction (25 min)** : [RUNBOOK.md](./RUNBOOK.md) v1.3.0 -- guide copy-paste pour **étendre** la base Airtable existante `CRM Souverain` (livrée défi 1 avec correction Eva) en la renommant `Sales Closer Souverain` + ajout table `SC_CRs_de_RDV` + FK `Prospect` linkée vers `SC_Prospects` existante + Interface unifiée `Sales Closer Souverain` (rattrape défi 1 + livre défi 2). Connecté à **SB_WF10 v1.1.0** (Niveau 2, accents FR + année 2026 validés end-to-end sur Julie Marchand + Karim Benhaddad). Voir §XII du RUNBOOK pour les 3 chemins d'usage, §XIII pour l'architecture complète. Utilise le CSV `seed/crs-seed.csv` pour l'auto-détection des colonnes + 4 seeds de test.

## 3 chemins d'usage SB_WF10 (même workflow, 3 clients possibles)

| Critère | Chemin A (Airtable) | Chemin B (Code) | Chemin C (Prompt seul) |
|---|---|---|---|
| Setup client | 20 min RUNBOOK | 0 (déjà déployé) | 30 sec copier-coller |
| Interface | Airtable + Formulaire | Next.js + markdown + PDF | IA choisie par le client |
| Persistance | Airtable | Neon Postgres | Aucune |
| Rattachement CRM | oui (FK linkée) | oui (FK cross-schéma) | non |
| PDF | non | oui | non |
| Souveraineté | moyenne | forte | totale |
| Coût par CR | ~0,001-0,005€ | ~0,001-0,005€ | plan gratuit IA |

Détails complets dans [RUNBOOK §XII](./RUNBOOK.md#xii--les-3-chemins-dusage-sb_wf10-référence----même-workflow-3-clients).

## Objectif no-code

Reproduire le formatage automatique de CR de RDV en s'appuyant uniquement sur Airtable (interface + automation native), sans code Next.js. Résultat pour le client : il colle ses notes brutes dans un formulaire Airtable, un automation Airtable appelle une IA, le CR formaté apparait dans le même enregistrement en quelques secondes.

## Deux niveaux d'automatisation

### Niveau 1 (défaut) -- Automation Airtable native + intégration OpenAI officielle

Airtable propose depuis 2024 une intégration OpenAI native dans ses automations (bloc "Generate text with AI"). C'est le chemin le plus simple, zéro code, tout dans Airtable.

Coût : intégré au plan Airtable Team (~24€/mois/utilisateur) OU tokens OpenAI facturés séparément selon le plan.

### Niveau 2 (fallback souverain) -- Automation Airtable + webhook n8n + OpenRouter

Si le client veut :

-> Choisir le modèle LLM (Claude, Mistral, Gemini... via OpenRouter)

-> Garder la logique de prompt hors d'Airtable (versionnée dans n8n)

-> Payer les LLM au token (moins cher qu'un plan Airtable Team + OpenAI)

-> Rester compatible avec le workflow de la version code (mêmes prompts, mêmes modèles)

Alors on remplace le bloc "Generate text with AI" d'Airtable par une action "Send webhook request" qui pointe vers le workflow n8n `SB_WF_CR-RDV` (partagé avec la version code souverain).

## Spécifications Airtable

### Base Airtable

Nom de base : `CR-RDV Formaté`

### Tables

#### Table `CRs` (table principale)

| Nom du champ | Type Airtable | Options |
|---|---|---|
| `Nom` | Formule | `IF({Interlocuteur}, {Interlocuteur} & " -- " & DATETIME_FORMAT({Date RDV}, "DD/MM/YYYY"), "CR du " & DATETIME_FORMAT(CREATED_TIME(), "DD/MM/YYYY HH:mm"))` -- rend chaque ligne identifiable |
| `Notes brutes` | Long text | rich text OFF (pour rester compatible avec le prompt LLM) |
| `CR formaté` | Long text | rich text ON (l'IA écrit ici du markdown, Airtable le rend en visuel) |
| `Date RDV` | Date | avec heure, extraite par l'IA |
| `Interlocuteur` | Single line text | extrait par l'IA |
| `Sujet` | Single line text | extrait par l'IA |
| `Statut` | Single select | `À formater`, `Formaté`, `Envoyé au client`, `Erreur` |
| `Modèle utilisé` | Single line text | traçabilité du modèle LLM |
| `Créé le` | Created time | auto |

#### Vues

-> **Vue "Nouveaux à formater"** : filtre `Statut = À formater` -- ce que l'automation va traiter

-> **Vue "Récents"** : tri `Créé le` décroissant, limite 20 -- accueil de l'interface

-> **Vue "À envoyer au client"** : filtre `Statut = Formaté`, groupe par `Interlocuteur` -- prêts à copier dans un mail

### Automation Airtable -- Formatage automatique

**Trigger** : `When record enters view "Nouveaux à formater"` (déclenché à chaque nouvelle ligne créée avec `Statut = À formater`)

**Étape 1 -- Niveau 1 (OpenAI natif Airtable)** :

Action : `Generate text` (intégration OpenAI native)

-> **Model** : `gpt-4o-mini` (bon rapport qualité/prix) ou `gpt-4o` (meilleur si notes complexes)

-> **Prompt** :
```
Tu es un assistant qui formate des notes brutes de rendez-vous en compte rendu structuré.

RÈGLE ABSOLUE : tu ne modifies JAMAIS le contenu factuel des notes. Tu réorganises et clarifies uniquement.

[... copier ici l'intégralité du prompt de PROMPT_LLM.md section II ...]

NOTES BRUTES DU RDV :
{Notes brutes}
```

-> **Output field** : `CR formaté` (l'IA écrit directement dans ce champ)

**Étape 2 -- Extraire les métadonnées (2e appel LLM)** :

Action : `Generate text` (2e appel, court et cheap)

-> **Prompt** :
```
Tu vas extraire 3 métadonnées d'un CR de RDV. Réponds UNIQUEMENT en JSON valide.

Format attendu :
{
  "dateRdv": "YYYY-MM-DDTHH:MM:SS" ou null,
  "interlocuteur": "Nom (Entreprise)" ou null,
  "sujet": "1 ligne max" ou null
}

CR :
{CR formaté}
```

-> **Output** : parser le JSON en 3 étapes séparées (script Airtable ou Zapier-like Parse) pour remplir `Date RDV`, `Interlocuteur`, `Sujet`

**Étape 3 -- Marquer comme formaté** :

Action : `Update record` -> `Statut = Formaté`

### Alternative Étape 1 -- Niveau 2 (webhook n8n)

Remplacer l'étape 1 par :

Action : `Send webhook request`

-> **URL** : `https://n8n.davidruggieri.com/webhook/cr-rdv-format` (identique au workflow code souverain)

-> **Method** : POST

-> **Headers** : `X-Webhook-Secret: {{env:N8N_WEBHOOK_SECRET}}`

-> **Body** :
```json
{
  "notesBrutes": "{Notes brutes}",
  "airtableRecordId": "{Record ID}",
  "airtableBaseId": "app...",
  "modeleLlm": "anthropic/claude-sonnet-5"
}
```

n8n reçoit les notes, appelle OpenRouter, écrit le CR formaté directement dans Airtable via l'API Airtable (record id + base id transmis), puis met à jour le statut à `Formaté`.

## Interface Airtable

### Page 1 -- "Prendre un CR"

-> **Formulaire d'ajout** : 1 champ `Notes brutes` (large text) + submit

-> À la soumission : nouvelle ligne créée avec `Statut = À formater` -> automation se déclenche

### Page 2 -- "CR récents"

-> Grid view des 20 derniers CR (colonnes : Interlocuteur, Sujet, Date RDV, CR formaté)

-> Bouton "Voir le CR complet" -> ouvre le détail avec le markdown rendu

-> Bouton "Copier CR" (script custom Airtable) : copie le markdown formaté dans le presse-papier

### Page 3 -- "Prompt LLM utilisé"

-> Simple markdown viewer qui affiche le contenu de PROMPT_LLM.md -- pour que le client puisse le copier hors Airtable si besoin

## Exemples fictifs (4 lignes de seed)

À entrer manuellement dans la base au démarrage :

1- **Marie Dupont (Acme SARL)** -- 8/9/2026 -- refonte site vitrine avec devis avant 20/9

2- **Karim Bouziane (BTP+)** -- 8/9/2026 -- audit RGPD site institutionnel

3- **Chloé Renaud (Studio Zenith)** -- 5/9/2026 -- refonte identité visuelle + charte

4- **Jean-Philippe Huber (Coaching en Alsace)** -- 3/9/2026 -- accompagnement mise en place CRM

## Comparaison Niveau 1 vs Niveau 2

| Critère | Niveau 1 (OpenAI Airtable) | Niveau 2 (n8n OpenRouter) |
|---|---|---|
| Complexité setup | Basique -- tout Airtable | Moyenne -- workflow n8n à monter |
| Coût par CR | ~0,01-0,05€ (OpenAI) | ~0,001-0,005€ (OpenRouter cheap) |
| Choix du modèle | GPT uniquement | 20+ modèles (Claude, Gemini, Mistral...) |
| Prompt versionné hors Airtable | Non | Oui (dans n8n) |
| Compatible avec version code souverain | Non (chemins séparés) | Oui (même workflow n8n) |
| Souveraineté | Faible (dépendance Airtable + OpenAI) | Forte (n8n self-host + OpenRouter au token) |

**Recommandation** : démarrer Niveau 1 pour tester, migrer Niveau 2 quand le volume justifie l'économie.

## Limitations no-code

-> Pas de génération PDF native depuis Airtable (contourner : impression navigateur depuis la vue détail, ou passer par Page Designer add-on Airtable payant)

-> Le rendu markdown dans Airtable est basique (tableau OK, formatage limité). Pour un CR client "premium" -> utiliser la version code souverain.

-> Automation Airtable a une limite d'exécutions par mois selon le plan (25/mois plan Free, 25k/mois plan Team). Volume typique CR-RDV : 5 par semaine = 20/mois = OK Free.

## Fichiers de spec

Ce README **est** la spec. Le client (ou l'AZI) construit la base Airtable en suivant les tableaux et étapes ci-dessus. Pas de fichier de config à importer -- Airtable ne le permet pas nativement.

Pour un import semi-automatique : voir script `scripts/create-airtable-base.js` (à créer en v0.2 si demande utilisateurs).

## Changelog

-> 0.5.0 -- 2026-09-10 (S133z-ccweb, Cor David après test Airtable en cours) : RUNBOOK v1.3.0 -- 3 corrections. (a) Étape 2.a : Date RDV heure déjà incluse par défaut à l'import CSV (skip). (b) Étape 2.b : précision comportement du champ lié (lookup imposé à créer puis supprimable). (c) **Étape 4 entièrement refondue** -- retrait de l'action `Envoyer une requête webhook` (passée sur plan Team payant Airtable en 2024), remplacée par 2 options gratuites : Option A recommandée = Airtable Trigger natif dans n8n (polling API, PAT Airtable, workflow SB_WF10-2), Option B fallback = Email trigger (Airtable envoie email vers alias Ionos, n8n IMAP écoute, workflow SB_WF10-3). Tableau comparatif A vs B ajouté. Impact temps toi : 25 min (au lieu de 20).

-> 0.4.0 -- 2026-09-10 (S133z-ccweb, Val David après Cor "ignore l'existant") : RUNBOOK v1.2.0 refondu pour ÉTENDRE la base `CRM Souverain` existante (défi 1 crm-souverain livré 2026-09-05 avec correction Eva : SC_Prospects 14 champs, 4 vues, formulaire, automation email récap, Interface `CRM Souverain` 4 pages) au lieu de créer une base from scratch. Base renommée -> `Sales Closer Souverain`, nouvelle table `SC_CRs_de_RDV` (convention SC_ + underscore cohérente avec SC_Prospects), FK `Prospect` linkée vers SC_Prospects existante. Nouvelle Étape 7 : Interface unifiée `Sales Closer Souverain` (rattrape défi 1 + livre défi 2). Section XII : 3 chemins d'usage SB_WF10 (A no-code Airtable / B code souverain / C prompt standalone) + tableau comparatif. Labels UI FR (Airtable de David en français). Origine : Cor David "un MetaCoach ne peut pas ignorer avant de parler" -> IDEE_claude_180 + IDEE_metacoach_179 (dr-context IDEAS_PRO PR#441).

-> 0.3.0 -- 2026-09-10 (S133z-ccweb, Val David) : refonte "architecture bases connectables" -- base renommée `Sales Closer Souverain` (une seule pour toute la boîte-à-outils), table `Prospects` créée dès le RUNBOOK v1.1.0 (vide au démarrage, 4 champs), champ `Prospect` linké optionnel dans `CRs de RDV`. Section XI ajoutée dans le RUNBOOK détaillant le miroir no-code Airtable (une base multi-tables) / code Neon (une DB multi-schémas). **Obsolète v0.4.0** : cette version ignorait le défi 1 déjà livré.

-> 0.2.0 -- 2026-09-10 (S133z-ccweb, Val David) : livraison v0.2 -- `RUNBOOK.md` (guide construction manuelle 10-15 min, Niveau 2 SB_WF10) + `seed/crs-seed.csv` (4 seeds fictifs pour import CSV auto-détection colonnes). Choix : template Airtable pur non livrable côté CC (pas de compte hôte), variante CSV + runbook préserve l'esprit "rapide côté toi".

-> 0.1.0 -- 2026-09-08 (S133z-ccweb) : création. Specs Airtable (base + tables + vues + automations 2 niveaux) + 4 exemples fictifs + comparaison Niveau 1/2 + interface 3 pages. Défi Alegria Eva PRO semaine du 8/9.
