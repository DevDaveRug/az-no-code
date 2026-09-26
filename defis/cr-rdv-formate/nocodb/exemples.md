# NocoDB - Exemples CRs pour défi 2 cr-rdv-formate

5 CR bruts anonymisés (mêmes personas que exemples.md des autres défis, RFC 2606 emails) couvrant les 5 contextes.

## 1. CR Découverte

- DateRDV : `2026-09-08`
- Client : Alice Martin - Cabinet Legrand
- ContexteRDV : `Découverte`
- Statut : `Formaté`
- NotesBrutes : "Cabinet 30 pers, expert-comptable, veut CRM léger. Actuellement Excel + Post-it. Douleur : perd les relances. Budget flou, veut chiffres avant décision. Enfants au collège. Rappel : demander équipes de foot pour ice breaker. Devis à envoyer avant vendredi."
- CRFormate :
```
POINTS CLIENT
- Structure : Cabinet expert-comptable, 30 collaborateurs
- Outil actuel : Excel + Post-it
- Douleur principale : perd les relances de prospects
- Budget : flou, demande devis chiffré avant décision

ACTIONS À FAIRE
- Envoyer devis avant vendredi
- Ice breaker prochain call : demander équipe de foot des enfants
- Point de vigilance : arbitrage budget probablement long
```

## 2. CR Négociation

- DateRDV : `2026-09-15`
- Client : Bob Durand - TechFlow SAS
- ContexteRDV : `Négociation`
- Statut : `Validé`
- NotesBrutes : "SaaS de gestion, 15 salariés. Comparé à concurrent X 30% moins cher. Notre différenciation : support FR + hosting souverain. Ils reviennent mardi avec décision. Point bloquant : besoin de garantie SLA écrite. J'ai proposé 99.5% avec pénalité 1 mois offert."

## 3. CR Suivi

- DateRDV : `2026-09-18`
- Client : Chloé Dubois - Studio Zenith
- ContexteRDV : `Suivi`
- Statut : `Envoyé au client`
- NotesBrutes : "3ème RDV. Onboarding OK, ils utilisent l'outil depuis 2 semaines. Feedback positif sur la vue Kanban, demandent export Excel hebdo. Je propose une automation. Prochaine étape : formation équipe 2 personnes le mois prochain, à devis séparé."

## 4. CR Closing

- DateRDV : `2026-09-22`
- Client : Emma Petit - MarketPro
- ContexteRDV : `Closing`
- Statut : `Envoyé au client`
- NotesBrutes : "Signature bon de commande annuel. 3500€ HT. Démarrage 1er octobre. Ils envoient PV de signature avant vendredi. Facture à émettre 05/10. Onboarding démarre 08/10 en visio 2h."

## 5. CR Support

- DateRDV : `2026-09-24`
- Client : Fabien Roux - Atelier Nord
- ContexteRDV : `Support`
- Statut : `À formater`
- NotesBrutes : "Ticket support niveau 2. Problème remonté : export PDF corrompu après MAJ v2.3. Reproductible chez eux. J'ai vu le bug côté render. Ticket créé côté équipe, correctif prévu v2.4 dans 10 jours. Contournement : passer par export CSV puis import LibreOffice."

## Convention personas

Prénoms + entreprises repris des autres défis (crm-souverain + masterclass-inscriptions) pour cohérence du portfolio. Emails et téléphones anonymisés RFC 2606 si besoin. Aucune donnée client réelle.
