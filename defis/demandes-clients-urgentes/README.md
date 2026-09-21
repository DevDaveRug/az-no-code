# Demandes clients urgentes -- MVP no-code

Projet portfolio : formulaire partageable de saisie de demandes clients avec vue triée par urgence, pour un freelance ou prestataire de services qui reçoit ses demandes par plusieurs canaux (mail, WhatsApp, SMS) et perd la vision d'ensemble.

## Pitch

Un lien unique que le client remplit tout seul : ta description, ton urgence, tes contacts. Toi tu vois tout dans un seul tableau, urgences en premier, avec les statuts (nouveau / en cours / bloqué / fait). Un email de confirmation part au client automatiquement, et si l'urgence est élevée, tu reçois une notif Telegram immédiatement.

## Deux stacks livrées

- `airtable/` : SPECS + schéma prêt à copier + exemples + spec Interface Dashboard native

- `nocodb/` : équivalent souverain (spec table + formulaire + Dashboard NocoDB natif)

Stack code souverain équivalent : `github.com/DevDaveRug/az-code/tree/main/defis/demandes-clients-urgentes` (Next.js + Prisma + Neon).

## Modèle de données

2 tables liées (Link Record bidirectionnel) :

- `AZ_Clients` : Nom, Email (clé métier), Téléphone, DateCréation, Demandes (Link vers AZ_Demandes)

- `AZ_Demandes` : Ref, Client (Link vers AZ_Clients), DateDemande, Description, UrgenceClient (déclarée par le client), UrgenceRéelle (éditable par le propriétaire), Statut, Commentaires, DateTraitement

Choix de conception : les 2 champs Urgence séparent la perception du client (UrgenceClient, non modifiable après soumission) et la priorité réelle assumée par le propriétaire (UrgenceRéelle, éditable en un clic). Ça évite la dérive « tout le monde met Critique » sans reprocher au client.

## Vues clés

- `AZ_Demandes` -> Grid `Urgentes en premier` : tri UrgenceRéelle desc puis DateDemande desc

- `AZ_Demandes` -> Kanban `Par statut` : Nouveau / En cours / Bloqué / Fait

- `AZ_Demandes` -> Grid `Nouvelles à traiter` : filtre Statut = Nouveau, tri urgence

- `AZ_Clients` -> Grid `Top demandeurs` : tri NbDemandes desc

## Automatisations

1. Email confirmation client à la soumission (envoi via Airtable Automation ou webhook n8n).

2. Notif Telegram au propriétaire si UrgenceRéelle in {Haute, Critique} sur record nouveau.

## Formulaire partageable

Formulaire Airtable natif (partage public) sur `AZ_Demandes` :

- Nom du client, Email du client, Téléphone (optionnel), Description, UrgenceClient (Basse / Moyenne / Haute / Critique, défaut Moyenne)

Une automatisation Airtable poste-lie ces demandes aux `AZ_Clients` existants par matching Email, ou crée un nouveau `AZ_Clients` si l'email est inconnu.

## Captures attendues (dans `captures/`)

- `airtable_01_demandes_urgentes.png` : vue Grid triée par urgence

- `airtable_02_kanban_statut.png` : vue Kanban Par statut

- `airtable_03_form_public.png` : formulaire partageable en preview

- `airtable_04_dashboard_interface.png` : page Interface Dashboard avec KPIs

- `airtable_05_automation_email.png` : Airtable Automation confirmation client configurée

- `airtable_06_automation_tg.png` : Airtable Automation notif TG urgence Haute/Critique

- `nocodb_01_demandes_urgentes.png` : vue équivalente NocoDB

- `nocodb_02_dashboard.png` : Dashboard NocoDB natif

- `nocodb_03_form_public.png` : formulaire NocoDB partagé public

## Angle commercial

Le prospect qui perd 50 % de ses demandes clients éparpillées sur 3 canaux découvre en 30 min qu'il peut centraliser, prioriser et rassurer ses clients avec un seul lien. La version souveraine (NocoDB ou Next.js) enlève la dépendance Airtable pour ceux qui veulent leur outil chez eux.
