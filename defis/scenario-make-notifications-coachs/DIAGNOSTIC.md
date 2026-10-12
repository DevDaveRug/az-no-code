# Diagnostic -- scénario « surveiller une table, notifier, marquer comme traité »

Version : 1.0.0
Date : 2026-10-13

Grille réutilisable pour dépanner un scénario d'automatisation (Make, n8n, Zapier) du type : une table de prospects -> un mail à l'équipe -> la fiche marquée comme notifiée. Cas d'origine anonymisé : un centre de coaching dont les coachs reçoivent les prospects en double et ne voient jamais ceux du site.

## Les 3 symptômes et leur cause

| Symptôme | Cause | Correction |
|---|---|---|
| Le même prospect envoyé plusieurs fois par jour | Le déclencheur surveille la date de dernière modification ; le dernier module modifie la fiche (case « Notifié ») ; la fiche repasse à chaque exécution et le filtre ne lit pas la case | Déclencher sur la date de création ; filtrer sur « Notifié non coché » |
| Un mail par centre d'intérêt coché | Un Iterator découpe la liste à choix multiples : un passage par valeur, donc un mail par valeur | Supprimer l'Iterator ; afficher la liste dans un seul mail avec `join(liste; ", ")` |
| Les prospects du formulaire jamais transmis | Le filtre exige `Statut = Nouveau` ; le formulaire laisse le Statut vide ; vide est différent de « Nouveau » | Filtre `Statut = Nouveau OU Statut n'existe pas` ; valeur par défaut « Nouveau » à la source, vérifiée par un test |

## Grille d'audit en 5 questions

1- **Le déclencheur réagit-il aux créations ou à toute modification ?** Si c'est à toute modification, chercher dans le scénario un module qui modifie la fiche surveillée : c'est une boucle.

2- **Un module découpe-t-il un enregistrement en plusieurs paquets ?** (Iterator, split, « for each »). Tout ce qui suit est exécuté une fois par paquet.

3- **Que fait le filtre d'une valeur vide ?** Une égalité stricte écarte les vides sans alerte. Dans Make, une case décochée ou un champ vide venant d'Airtable n'est pas envoyé : tester « n'existe pas », pas « égal à vide ».

4- **La marque « déjà traité » est-elle lue, ou seulement écrite ?** Une case cochée en fin de scénario ne protège de rien si aucun filtre ne la lit.

5- **Que deviennent les enregistrements déjà ratés ?** Un nouveau déclencheur sur la date de création ne les reprendra pas. Les isoler dans une vue (« Statut vide et non notifié »), les relire, les transmettre en une fois.

## Scénario corrigé

1- Watch Records, champ déclencheur = date de création

2- Filtre : « Notifié » n'existe pas ET (« Statut » = Nouveau OU « Statut » n'existe pas)

3- Envoi du mail : un seul, avec la liste des centres d'intérêt jointe

4- Mise à jour de la fiche : cocher « Notifié »

Option si chaque membre de l'équipe a ses thèmes : un aiguillage (Router) après le filtre, une route par membre, condition sur la liste des centres d'intérêt.
