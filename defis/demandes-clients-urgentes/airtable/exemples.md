# Exemples anonymisés -- AZ_Clients + AZ_Demandes

Données fictives pour peupler la démo. Prénoms génériques, entreprises fictives, emails en `<slug>.example` (RFC 2606), téléphones `+33 6 00 00 00 XX`.

## AZ_Clients (5 lignes)

| Nom | Email | Telephone |
|---|---|---|
| Alice Martin | alice.martin@legrand.example | +33 6 00 00 00 11 |
| Bob Durand | bob.durand@techflow.example | +33 6 00 00 00 12 |
| Chloe Dubois | chloe.dubois@studio-zenith.example | +33 6 00 00 00 13 |
| Emma Petit | emma.petit@marketpro.example | +33 6 00 00 00 14 |
| Fabien Roux | fabien.roux@atelier-nord.example | +33 6 00 00 00 15 |

## AZ_Demandes (10 lignes, mix urgences et statuts)

| Ref | Client (Email pour matching) | Description | UrgenceClient | UrgenceReelle | Statut |
|---|---|---|---|---|---|
| #1 | alice.martin@legrand.example | Changer le logo dans le header du site, la nouvelle version est en pj. | Basse | Basse | Fait |
| #2 | bob.durand@techflow.example | Ajouter un bouton `Contact` dans le menu mobile, il manque depuis la refonte. | Moyenne | Moyenne | En cours |
| #3 | chloe.dubois@studio-zenith.example | Corriger la fonte du prix sur la landing, elle passe en Comic Sans en prod. | Haute | Haute | Nouveau |
| #4 | alice.martin@legrand.example | Le formulaire de contact ne renvoie plus d email, urgent avant demain. | Critique | Critique | En cours |
| #5 | emma.petit@marketpro.example | Retirer un ancien post du blog qui reference un partenariat termine. | Basse | Moyenne | Nouveau |
| #6 | fabien.roux@atelier-nord.example | Ajouter un lien vers ma nouvelle chaine YouTube dans le footer. | Moyenne | Basse | Nouveau |
| #7 | bob.durand@techflow.example | Peux tu me faire une version anglaise de la page tarifs ? | Haute | Moyenne | Bloque |
| #8 | chloe.dubois@studio-zenith.example | Refresh du menu principal, on veut inverser l ordre de 2 items. | Basse | Basse | Fait |
| #9 | emma.petit@marketpro.example | La photo hero est pixellisee sur mobile, elle prend toute la largeur mal. | Haute | Haute | Nouveau |
| #10 | alice.martin@legrand.example | Ajouter un pop up cookie conforme RGPD, on a un audit dans 15 jours. | Critique | Haute | En cours |

Ces exemples montrent :

- L'écart entre UrgenceClient (perçue) et UrgenceReelle (assumée par le propriétaire) : ligne 5, 6, 7, 10 -> le propriétaire a réévalué.

- La vue `Urgentes en premier` triera : #4 Critique -> #3 Haute -> #9 Haute -> #10 Haute -> #2 Moyenne -> #5 Moyenne -> #6 Basse -> #1 Fait (masqué par filter Statut != Fait).

- Le Kanban Par statut aura : Nouveau (4) / En cours (3) / Bloque (1) / Fait (2). Immédiatement lisible.

- Alice Martin apparait 3 fois dans AZ_Clients.Demandes -> NbDemandes = 3 -> en tête de Top demandeurs.
