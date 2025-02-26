# TP 5.2 MongoDB - Aggregate : Réponses et Explications

Ce document présente les réponses aux différents exercices du TP 5.2 portant sur l’utilisation du pipeline aggregate de MongoDB. Vous y trouverez pour chaque exercice des exemples de commandes avec explications détaillées.

---

## Table des Matières

-   [TP 5.2 MongoDB - Aggregate : Réponses et Explications](#tp-52-mongodb---aggregate--réponses-et-explications)
    -   [Table des Matières](#table-des-matières)
    -   [Préambule](#préambule)
    -   [Exercice 1 : Prise en main](#exercice-1--prise-en-main)
    -   [Exercice 2 : Jouer avec les structures – Documents plats](#exercice-2--jouer-avec-les-structures--documents-plats)
    -   [Exercice 3 : Jouer avec les structures – Document avec Array](#exercice-3--jouer-avec-les-structures--document-avec-array)
    -   [Exercice 4 : Jouer avec les structures – Document avec sous-documents](#exercice-4--jouer-avec-les-structures--document-avec-sous-documents)
    -   [Nettoyage final](#nettoyage-final)
    -   [Conclusion](#conclusion)

---

## Préambule

1. **Création de l'instance Docker :**
   Utilisez la dernière version de MongoDB en lançant, par exemple :

```bash
   docker run --name monmongo -v mes_data_mongo:/data/db -p 27017:27017 -d mongo:latest
```

2. **Importation du fichier employes.json :**
   Importez le fichier dans la base `test` dans la collection `employes` avec :

```bash
   mongoimport -d test -c employes --file employes.json
```

3. **Connexion via mongosh :**

```bash
   docker exec -it monmongo mongosh
```

4. **Liste des collections :**

```javascript
   show collections
```

---

## Exercice 1 : Prise en main

**Q1. Listez tous les employés (en utilisant find puis en utilisant aggregate)**

-   _Avec find :_

```javascript
db.employes.find();
```

-   _Avec aggregate :_

```javascript
db.employes.aggregate([{ $match: {} }]);
```

_Explication :_ La requête aggregate avec un $match vide retourne tous les documents.

**Q2. Listez les employés triés par nom décroissant. Quel est le dernier affiché ?**

-   _Avec find :_

```javascript
db.employes.find().sort({ nom: -1 });
```

-   _Avec aggregate :_

```javascript
db.employes.aggregate([{ $sort: { nom: -1 } }]);
```

_Explication :_ Le tri décroissant par le champ "nom" place en premier l’employé ayant le nom alphabétiquement le plus grand. Le dernier affiché sera celui avec le plus petit nom en ordre alphabétique.

**Q3. Listez uniquement les noms et prénoms des employés triés par nom décroissant**

-   _Avec find :_

```javascript
    db.employes.find({}, { \_id: 0, nom: 1, prenom: 1 }).sort({ nom: -1 })
```

-   _Avec aggregate :_

```javascript
db.employes.aggregate([
	{ $sort: { nom: -1 } },
	{ $project: { _id: 0, nom: 1, prenom: 1 } },
]);
```

_Explication :_ La projection permet d’exclure le champ \_id et de n’afficher que les champs désirés.

**Q4. Listez les employés dont l’ancienneté est supérieure à 20**

-   _Avec find :_

```javascript
db.employes.find({ anciennete: { $gt: 20 } });
```

-   _Avec aggregate :_

```javascript
db.employes.aggregate([{ $match: { anciennete: { $gt: 20 } } }]);
```

_Explication :_ Le $match filtre les documents selon la condition spécifiée.

**Q5. Listez uniquement le nom et l’ancienneté des employés, pour ceux dont l’ancienneté est supérieure à 10, affichés par ordre d’ancienneté décroissante**

-   _Avec find :_

```javascript
    db.employes.find({ anciennete: { $gt: 10 } }, { \_id: 0, nom: 1, anciennete: 1 }).sort({ anciennete: -1 })
```

-   _Avec aggregate :_

```javascript
db.employes.aggregate([
	{ $match: { anciennete: { $gt: 10 } } },
	{ $project: { _id: 0, nom: 1, anciennete: 1 } },
	{ $sort: { anciennete: -1 } },
]);
```

_Explication :_ On filtre, projette et trie pour obtenir le résultat désiré.

**Q6. Calculez et affichez la moyenne de toutes les anciennetés**

```javascript
db.employes.aggregate([
	{ $group: { _id: null, moyenneAnciennete: { $avg: '$anciennete' } } },
]);
```

_Explication :_ L’opérateur $group avec $avg calcule la moyenne sur tous les documents.

**Q7. Calculez et affichez la moyenne de l’ancienneté des employés qui ont plus de 20 ans d’ancienneté**

```javascript
db.employes.aggregate([
	{ $match: { anciennete: { $gt: 20 } } },
	{ $group: { _id: null, moyenneAnciennete: { $avg: '$anciennete' } } },
]);
```

_Explication :_ On applique d’abord un filtre puis on calcule la moyenne.

**Q8. Calculez et affichez la somme de l’ancienneté par prénom d’employé, l’ensemble trié par prénom croissant**

```javascript
db.employes.aggregate([
	{ $group: { _id: '$prenom', sommeAnciennete: { $sum: '$anciennete' } } },
	{ $sort: { _id: 1 } },
]);
```

_Explication :_ Les documents sont groupés par prénom et la somme des anciennetés est calculée pour chaque groupe.

**Q9. Calculez et affichez la somme de l’ancienneté par prénom d’employé mais uniquement pour les sommes supérieures à 20**

```javascript
db.employes.aggregate([
	{ $group: { _id: '$prenom', sommeAnciennete: { $sum: '$anciennete' } } },
	{ $match: { sommeAnciennete: { $gt: 20 } } },
]);
```

_Explication :_ Le pipeline regroupe d’abord par prénom, puis filtre les groupes dont la somme est supérieure à 20 (similaire à un HAVING).

---

## Exercice 2 : Jouer avec les structures – Documents plats

> **Contexte :** On utilise ici le fichier `notes1.json` qui contient des documents représentant une note unique par étudiant.

**Q1. Importez le fichier dans une collection `notes1`**

```bash
mongoimport -d test -c notes1 --file notes1.json
```

**Q2. Affichez le premier document**

```javascript
db.notes1.findOne();
```

**Q3. Listez tous les documents sans afficher l’id**

```javascript
db.notes1.find({}, { \_id: 0 })
```

**Q4. Affichez le nombre de documents**

```javascript
db.notes1.countDocuments();
```

**Q5. Affichez les documents de l’étudiant user18 sans afficher l’id**

```javascript
db.notes1.find({ nom: "user18" }, { \_id: 0 })
```

**Q6. Affichez la moyenne de chaque étudiant, avec son nom et son groupe**

```javascript
db.notes1.aggregate([
	{
		$group: {
			_id: { nom: '$nom', groupe: '$groupe' },
			moyenne: { $avg: '$valeur' },
		},
	},
	{
		$project: {
			_id: 0,
			nom: '$_id.nom',
			groupe: '$_id.groupe',
			moyenne: 1,
		},
	},
]);
```

_Explication :_ On groupe par nom et groupe pour calculer la moyenne des notes de chaque étudiant.

**Q7. Calculez le nombre de noms différents dans la collection**

```javascript
db.notes1.distinct('nom').length;
```

**Q8. Affichez le nombre de notes de chaque étudiant**

```javascript
db.notes1.aggregate([{ $group: { _id: '$nom', nbNotes: { $sum: 1 } } }]);
```

**Q9. Affichez la distribution des nombres de notes (combien d’étudiants ont x notes)**

```javascript
db.notes1.aggregate([
	{ $group: { _id: '$nom', nbNotes: { $sum: 1 } } },
	{ $group: { _id: '$nbNotes', etudiants: { $sum: 1 } } },
	{ $sort: { _id: 1 } },
]);
```

_Explication :_ Le premier $group compte le nombre de notes par étudiant, puis le second regroupe ces comptes pour obtenir la distribution.

**Q10. Affichez les notes minimum de chaque étudiant, avec son nom et son groupe**

```javascript
db.notes1.aggregate([
	{
		$group: {
			_id: { nom: '$nom', groupe: '$groupe' },
			noteMin: { $min: '$valeur' },
		},
	},
	{
		$project: {
			_id: 0,
			nom: '$_id.nom',
			groupe: '$_id.groupe',
			noteMin: 1,
		},
	},
]);
```

**Q11. Affichez la note min et la note max de chaque étudiant, avec son nom et son groupe**

```javascript
db.notes1.aggregate([
	{
		$group: {
			_id: { nom: '$nom', groupe: '$groupe' },
			noteMin: { $min: '$valeur' },
			noteMax: { $max: '$valeur' },
		},
	},
	{
		$project: {
			_id: 0,
			nom: '$_id.nom',
			groupe: '$_id.groupe',
			noteMin: 1,
			noteMax: 1,
		},
	},
]);
```

---

## Exercice 3 : Jouer avec les structures – Document avec Array

> **Contexte :** À partir de `notes1`, nous regroupons toutes les notes d’un même étudiant dans un tableau pour créer la collection `notes2`.

**Q1. Créez la collection `notes2` regroupant les notes par étudiant**

```javascript
db.notes1.aggregate([
	{
		$group: {
			_id: { nom: '$nom', groupe: '$groupe' },
			valeurs: { $push: '$valeur' },
		},
	},
	{
		$project: {
			_id: 0,
			nom: '$_id.nom',
			groupe: '$_id.groupe',
			valeurs: 1,
		},
	},
	{ $out: 'notes2' },
]);
```

_Explication :_ Le $group regroupe par nom et groupe et le $push crée un tableau de valeurs pour chaque étudiant.

**Q2. Affichez le premier document de `notes2`**

```javascript
db.notes2.findOne();
```

**Q3. Affichez tous les documents sans l’id**

```javascript
db.notes2.find({}, { \_id: 0 })
```

**Q4. Affichez le nombre de documents**

```javascript
db.notes2.countDocuments();
```

**Q5. Affichez les documents de l’étudiant user18 sans l’id**

```javascript
db.notes2.find({ nom: "user18" }, { \_id: 0 })
```

**Q6. Affichez la moyenne de chaque étudiant, avec son nom**

```javascript
db.notes2.aggregate([
	{ $project: { nom: 1, groupe: 1, moyenne: { $avg: '$valeurs' } } },
]);
```

**Q7. Calculez le nombre de noms différents dans la collection**

```javascript
db.notes2.distinct('nom').length;
```

**Q8. Affichez le nombre de notes de chaque étudiant**

```javascript
db.notes2.aggregate([{ $project: { nom: 1, nbNotes: { $size: '$valeurs' } } }]);
```

**Q9. Affichez la distribution des nombres de notes**

```javascript
db.notes2.aggregate([
	{ $project: { nbNotes: { $size: '$valeurs' } } },
	{ $group: { _id: '$nbNotes', count: { $sum: 1 } } },
	{ $sort: { _id: 1 } },
]);
```

**Q10. Affichez les notes minimum de chaque étudiant, avec son nom et son groupe**

```javascript
db.notes2.aggregate([
	{ $project: { nom: 1, groupe: 1, noteMin: { $min: '$valeurs' } } },
]);
```

**Q11. Supprimez le document des étudiants qui n’ont pas eu 10 à la moyenne**

```javascript
db.notes2.deleteMany({ $expr: { $lt: [{ $avg: '$valeurs' }, 10] } });
```

_Explication :_ La suppression s’appuie sur l’opérateur $expr pour comparer la moyenne calculée à 10.

---

## Exercice 4 : Jouer avec les structures – Document avec sous-documents

> **Contexte :** Le fichier `notes3.json` contient, pour chaque étudiant, un tableau `notes` d’objets (chaque objet représente une note avec des champs comme `mat`, `numcont` et `valeur`).

**Q1. Importez le fichier dans une collection `note3`**

```bash
mongoimport -d test -c note3 --file notes3.json
```

**Q2. Analyse de la collection**

-   **Q2.1. Affichez le premier document :**

```javascript
db.note3.findOne();
```

-   **Q2.2. Affichez le nombre de documents :**

```javascript
db.note3.countDocuments();
```

**Q3. Calculez la moyenne des notes de "bdd" pour chaque étudiant**

```javascript
db.note3.aggregate([
	{
		$project: {
			nom: 1,
			groupe: 1,
			bdd_notes: {
				$filter: {
					input: '$notes',
					as: 'n',
					cond: { $eq: ['$$n.mat', 'bdd'] },
				},
			},
		},
	},
	{
		$project: {
			nom: 1,
			groupe: 1,
			moyenne_bdd: { $avg: '$bdd_notes.valeur' },
		},
	},
]);
```

_Explication :_ Le $filter extrait les notes en "bdd" et $avg calcule leur moyenne.

**Q4. Supprimez dans l’Array `notes` :**

-   Pour l’étudiant user18, supprimez le document lié à "bdd".
-   Pour user24, supprimez ceux liés à "bdd" et "gestion".
-   _Pour user18 :_

```javascript
db.note3.updateOne({ nom: 'user18' }, { $pull: { notes: { mat: 'bdd' } } });
```

-   _Pour user24 (deux requêtes) :_

```javascript
db.note3.updateOne({ nom: 'user24' }, { $pull: { notes: { mat: 'bdd' } } });
```

```javascript
db.note3.updateOne({ nom: 'user24' }, { $pull: { notes: { mat: 'gestion' } } });
```

**Q5. Listez les étudiants dont l’Array `notes` contient moins de 7 documents**

```javascript
db.note3.aggregate([
	{ $project: { nom: 1, nbNotes: { $size: '$notes' } } },
	{ $match: { nbNotes: { $lt: 7 } } },
]);
```

**Q6. Affichez les noms des étudiants à qui il manque des notes en "gestion"**

```javascript
db.note3.find(
{ notes: { $not: { $elemMatch: { mat: "gestion" } } } },
{ \_id: 0, nom: 1 }
)
```

_Explication :_ La requête vérifie l'absence d'un élément dans l'Array dont le champ `mat` vaut "gestion".

**Q7. Créez une vue avec 4 champs (nom, mat, numcont, valeur) pour les étudiants n'ayant pas 7 documents dans l’Array**

```javascript
db.note3.aggregate([
	{ $project: { nom: 1, nbNotes: { $size: '$notes' }, notes: 1 } },
	{ $match: { nbNotes: { $ne: 7 } } },
	{ $unwind: '$notes' },
	{
		$project: {
			_id: 0,
			nom: 1,
			mat: '$notes.mat',
			numcont: '$notes.numcont',
			valeur: '$notes.valeur',
		},
	},
]);
```

_Explication :_ Le $unwind décompose l’Array pour afficher chaque note individuellement.

---

## Nettoyage final

À la fin du TP, pensez à détruire les instances Docker et les volumes créés :

```bash
docker rm -f monmongo
docker volume rm mes_data_mongo
```

Vérifiez ensuite que :

```bash
docker ps --all
docker volume ls
```

_Explication :_ Ces commandes garantissent la suppression de toutes les ressources utilisées pendant le TP.

---

## Conclusion

Ce TP vous a permis de vous familiariser avec les requêtes d’agrégation dans MongoDB, de la simple récupération de documents aux opérations plus complexes telles que le regroupement, le filtrage et la manipulation d’Arrays et de sous-documents. Pour toute information complémentaire, consultez la [documentation officielle de MongoDB](https://www.mongodb.co
