# Exemples anonymisés NocoDB -- même dataset qu'Airtable

Copier tel quel les 5 clients + 10 demandes de `airtable/exemples.md`. NocoDB accepte l'import CSV directement dans l'UI (bouton `Import Data`).

## CSV `AZ_Clients` (à importer)

```
Nom,Email,Telephone
Alice Martin,alice.martin@legrand.example,+33 6 00 00 00 11
Bob Durand,bob.durand@techflow.example,+33 6 00 00 00 12
Chloe Dubois,chloe.dubois@studio-zenith.example,+33 6 00 00 00 13
Emma Petit,emma.petit@marketpro.example,+33 6 00 00 00 14
Fabien Roux,fabien.roux@atelier-nord.example,+33 6 00 00 00 15
```

## CSV `AZ_Demandes` (à importer)

```
Client_Id,Description,UrgenceClient,UrgenceReelle,Statut,NomClientTemp,EmailClientTemp,TelephoneClientTemp
1,Changer le logo dans le header du site la nouvelle version est en pj,Basse,Basse,Fait,Alice Martin,alice.martin@legrand.example,+33 6 00 00 00 11
2,Ajouter un bouton Contact dans le menu mobile il manque depuis la refonte,Moyenne,Moyenne,En cours,Bob Durand,bob.durand@techflow.example,+33 6 00 00 00 12
3,Corriger la fonte du prix sur la landing elle passe en Comic Sans en prod,Haute,Haute,Nouveau,Chloe Dubois,chloe.dubois@studio-zenith.example,+33 6 00 00 00 13
1,Le formulaire de contact ne renvoie plus d email urgent avant demain,Critique,Critique,En cours,Alice Martin,alice.martin@legrand.example,+33 6 00 00 00 11
4,Retirer un ancien post du blog qui reference un partenariat termine,Basse,Moyenne,Nouveau,Emma Petit,emma.petit@marketpro.example,+33 6 00 00 00 14
5,Ajouter un lien vers ma nouvelle chaine YouTube dans le footer,Moyenne,Basse,Nouveau,Fabien Roux,fabien.roux@atelier-nord.example,+33 6 00 00 00 15
2,Peux tu me faire une version anglaise de la page tarifs,Haute,Moyenne,Bloque,Bob Durand,bob.durand@techflow.example,+33 6 00 00 00 12
3,Refresh du menu principal on veut inverser l ordre de 2 items,Basse,Basse,Fait,Chloe Dubois,chloe.dubois@studio-zenith.example,+33 6 00 00 00 13
4,La photo hero est pixellisee sur mobile elle prend toute la largeur mal,Haute,Haute,Nouveau,Emma Petit,emma.petit@marketpro.example,+33 6 00 00 00 14
1,Ajouter un pop up cookie conforme RGPD on a un audit dans 15 jours,Critique,Haute,En cours,Alice Martin,alice.martin@legrand.example,+33 6 00 00 00 11
```

Le `Client_Id` référence le champ Link Record vers AZ_Clients. NocoDB accepte l'ID numérique dans l'import CSV pour les Links.

Après import, vérifier :

- La vue `Urgentes en premier` remonte #4 (Critique) et #10 (Haute avec refexion propriétaire) en tête.

- Alice Martin a 3 demandes -> apparait en haut de `Top demandeurs`.

- Le Kanban Par statut a 4 Nouveau / 3 En cours / 1 Bloque / 2 Fait.
