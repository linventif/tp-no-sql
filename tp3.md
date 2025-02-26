# TP 5.3 MongoDB - Map-Reduce : Réponses et Explications

Ce document présente les réponses aux différents exercices du TP 5.3 portant sur l’utilisation du pattern Map-Reduce avec MongoDB. Vous y trouverez des exemples de fonctions JavaScript, l’utilisation des méthodes filter, map et reduce, ainsi que l’implémentation de Map-Reduce sur la collection `notes1` et des calculs sur l’ensemble et des parties de la collection.

---

## Table des Matières

-   [TP 5.3 MongoDB - Map-Reduce : Réponses et Explications](#tp-53-mongodb---map-reduce--réponses-et-explications)
    -   [Table des Matières](#table-des-matières)
    -   [Préambule JavaScript : Fonctions et Méta-fonctions](#préambule-javascript--fonctions-et-méta-fonctions)
    -   [Préambule JavaScript : filter, map, reduce](#préambule-javascript--filter-map-reduce)
    -   [Préambule Map-Reduce](#préambule-map-reduce)
    -   [Exercice 1 : Un premier exemple détaillé](#exercice-1--un-premier-exemple-détaillé)
    -   [Exercice 2 : Calculs sur l’ensemble de la collection](#exercice-2--calculs-sur-lensemble-de-la-collection)
    -   [Exercice 3 : Calcul sur des parties de la collection](#exercice-3--calcul-sur-des-parties-de-la-collection)
    -   [Nettoyage final](#nettoyage-final)
    -   [Conclusion](#conclusion)

---

## Préambule JavaScript : Fonctions et Méta-fonctions

**Q1.** Écrire une fonction `somme` qui renvoie la somme de 2 entiers.

```javascript
function somme(a, b) {
	return a + b;
}
console.log(somme(3, 4)); // Doit afficher 7
```

**Q2.** Écrire une fonction `calcul` à 3 paramètres qui applique la fonction passée en premier paramètre aux deux autres.

```javascript
function calcul(fn, a, b) {
	return fn(a, b);
}
console.log(calcul(somme, 3, 4)); // Doit afficher 7
```

**Q3.** Appeler `calcul` en passant une fonction anonyme pour calculer le produit de 2 paramètres.

```javascript
console.log(calcul(function(x, y) { return x \* y; }, 3, 4)); // Doit afficher 12
```

---

## Préambule JavaScript : filter, map, reduce

On crée un tableau `tab` contenant les 20 premiers entiers naturels.

```javascript
var tab = [];
for (var i = 1; i <= 20; i++) {
	tab.push(i);
}
```

**Q4.** Utiliser `filter` pour retourner un tableau avec uniquement les multiples de 3.

```javascript
var multiples3 = tab.filter(function (el) {
	return el % 3 === 0;
});
console.log(multiples3);
```

**Q5.** Utiliser `map` pour mettre chaque élément au carré.

```javascript
var carres = tab.map(function(el) { return el \* el; });
console.log(carres);
```

**Q6.** Utiliser `reduce` pour calculer la somme de tous les éléments.

```javascript
var sommeTab = tab.reduce(function (acc, el) {
	return acc + el;
}, 0);
console.log(sommeTab);
```

---

## Préambule Map-Reduce

Le pattern Map-Reduce dans MongoDB s’appuie sur deux fonctions :

-   **mapFunction :** Pour extraire et émettre des paires clé/valeur de chaque document (via `emit(key, value)`).
-   **reduceFunction :** Pour agréger les valeurs ayant la même clé.

La commande est :

```javascript
db.collection.mapReduce(
<mapFunction>,
<reduceFunction>,
{ /_ options : query, sort, limit, out, etc. _/ }
)
```

> Pour ce TP, nous utiliserons la collection `notes1` (importée depuis `notes1.json`).

**Q7.** Récupérez le fichier `notes1.json` sur Moodle.
**Q8.** Importez ces données dans votre base avec :

```bash
mongoimport -d test -c notes1 --file notes1.json
```

---

## Exercice 1 : Un premier exemple détaillé

L’objectif est de calculer la somme des notes de tous les étudiants dans la collection `notes1`.

**Q1.** Récupérez un document dans une variable `d`.

```javascript
var d = db.notes1.findOne();
```

**Q2.** Écrire une fonction `emit` qui affiche la clé et la valeur.

```javascript
function emit(key, value) {
	print('Clé : ' + key + ' | Valeur : ' + value);
}
```

**Q3.** Écrire une fonction `emettre` qui appelle `emit` avec la clé `null` et la donnée issue du document via `this`.

```javascript
function emettre() {
	emit(null, this.valeur);
}
```

**Q4.** Tester la fonction avec `apply` sur le document `d`.

```javascript
emettre.apply(d);
```

**Q5.** Écrire une fonction `reduire` qui, pour une clé et un tableau de valeurs, renvoie la somme.

```javascript
function reduire(key, values) {
	return Array.sum(values);
}
```

**Q6.** Tester `reduire` sur un tableau d’entiers.

```javascript
print('Test reduire : ' + reduire(null, [1, 2, 3, 4, 5])); // Doit afficher 15
```

**Q7.** Utiliser les fonctions `emettre` et `reduire` avec `mapReduce` sur la collection `notes1`.

```javascript
db.notes1.mapReduce(emettre, reduire, { out: { inline: 1 } });
```

**Q8.** Réécrire l’appel en une seule instruction en utilisant des fonctions anonymes.

```javascript
db.notes1.mapReduce(
	function () {
		emit(null, this.valeur);
	},
	function (key, values) {
		return Array.sum(values);
	},
	{ out: { inline: 1 } }
);
```

**Q9.** Modifier la fonction d’émission pour émettre `this.groupe` comme clé et relancer la requête.

```javascript
db.notes1.mapReduce(
	function () {
		emit(this.groupe, this.valeur);
	},
	function (key, values) {
		return Array.sum(values);
	},
	{ out: { inline: 1 } }
);
```

_Explication :_ En émettant `this.groupe` comme clé, les valeurs seront regroupées par groupe, donnant ainsi la somme des notes par groupe.

---

## Exercice 2 : Calculs sur l’ensemble de la collection

Pour chaque question, une solution avec `aggregate` et une solution avec `mapReduce` est proposée.

**Q1. Affichez la note maximale de la collection.**

-   _Avec aggregate :_

```javascript
db.notes1.aggregate([{ $group: { _id: null, maxNote: { $max: '$valeur' } } }]);
```

-   _Avec mapReduce :_

```javascript
db.notes1.mapReduce(
	function () {
		emit(null, this.valeur);
	},
	function (key, values) {
		return Math.max.apply(null, values);
	},
	{ out: { inline: 1 } }
);
```

**Q2. Calculez le nombre de notes dans la collection.**

-   _Avec aggregate :_

```javascript
db.notes1.aggregate([{ $group: { _id: null, count: { $sum: 1 } } }]);
```

-   _Avec mapReduce :_

```javascript
db.notes1.mapReduce(
	function () {
		emit(null, 1);
	},
	function (key, values) {
		return Array.sum(values);
	},
	{ out: { inline: 1 } }
);
```

**Q3. Affichez en une fois le minimum et le maximum des notes de la collection.**

-   _Avec aggregate :_

```javascript
db.notes1.aggregate([
	{
		$group: {
			_id: null,
			minNote: { $min: '$valeur' },
			maxNote: { $max: '$valeur' },
		},
	},
]);
```

-   _Avec mapReduce :_

```javascript
db.notes1.mapReduce(
	function () {
		emit(null, this.valeur);
	},
	function (key, values) {
		return {
			min: Math.min.apply(null, values),
			max: Math.max.apply(null, values),
		};
	},
	{ out: { inline: 1 } }
);
```

**Q4. Calculez la moyenne des notes dans la collection.**

-   _Avec aggregate :_

```javascript
db.notes1.aggregate([{ $group: { _id: null, avgNote: { $avg: '$valeur' } } }]);
```

-   _Avec mapReduce :_

```javascript
db.notes1.mapReduce(
	function () {
		emit(null, { sum: this.valeur, count: 1 });
	},
	function (key, values) {
		var result = { sum: 0, count: 0 };
		values.forEach(function (v) {
			result.sum += v.sum;
			result.count += v.count;
		});
		return result;
	},
	{
		finalize: function (key, reducedVal) {
			return reducedVal.sum / reducedVal.count;
		},
		out: { inline: 1 },
	}
);
```

**Q5. Affichez le prénom le plus court dans la collection (le premier en cas d’égalité).**

-   _Avec aggregate :_

```javascript
db.notes1.aggregate([
	{ $group: { _id: null, prenoms: { $addToSet: '$prenom' } } },
	{
		$project: {
			prenomLePlusCourt: {
				$arrayElemAt: [
					{
						$slice: [
							{
								$sortArray: {
									input: '$prenoms',
									sortBy: { $strLenCP: 1 },
								},
							},
							1,
						],
					},
					0,
				],
			},
		},
	},
]);
```

-   _Avec mapReduce :_

```javascript
db.notes1.mapReduce(
	function () {
		emit(null, this.prenom);
	},
	function (key, values) {
		return values.reduce(function (shortest, current) {
			return current.length < shortest.length ? current : shortest;
		}, values[0]);
	},
	{ out: { inline: 1 } }
);
```

---

## Exercice 3 : Calcul sur des parties de la collection

**Q1. Calculer le nombre de notes par étudiant.**

-   _Avec aggregate :_

```javascript
db.notes1.aggregate([{ $group: { _id: '$nom', nbNotes: { $sum: 1 } } }]);
```

-   _Avec mapReduce :_

```javascript
db.notes1.mapReduce(
	function () {
		emit(this.nom, 1);
	},
	function (key, values) {
		return Array.sum(values);
	},
	{ out: { inline: 1 } }
);
```

**Q2. Fournir la distribution des notes de la collection.**

```javascript
db.notes1.aggregate([
	{ $group: { _id: '$valeur', count: { $sum: 1 } } },
	{ $sort: { _id: 1 } },
]);
```

**Q3. Calculer la moyenne des notes par groupe.**

-   _Avec aggregate :_

```javascript
db.notes1.aggregate([
	{ $group: { _id: '$groupe', avgNote: { $avg: '$valeur' } } },
]);
```

-   _Avec mapReduce :_

```javascript
db.notes1.mapReduce(
	function () {
		emit(this.groupe, { sum: this.valeur, count: 1 });
	},
	function (key, values) {
		var result = { sum: 0, count: 0 };
		values.forEach(function (v) {
			result.sum += v.sum;
			result.count += v.count;
		});
		return result;
	},
	{
		finalize: function (key, reducedVal) {
			return reducedVal.sum / reducedVal.count;
		},
		out: { inline: 1 },
	}
);
```

**Q4. Calculer la moyenne des notes pour l’ensemble des groupes sauf le groupe 'A'.**

-   _Q4.1. Avec un `if` dans la fonction d’émission (mapReduce) :_

```javascript
db.notes1.mapReduce(
	function () {
		if (this.groupe !== 'A') {
			emit(this.groupe, { sum: this.valeur, count: 1 });
		}
	},
	function (key, values) {
		var result = { sum: 0, count: 0 };
		values.forEach(function (v) {
			result.sum += v.sum;
			result.count += v.count;
		});
		return result;
	},
	{
		finalize: function (key, reducedVal) {
			return reducedVal.sum / reducedVal.count;
		},
		out: { inline: 1 },
	}
);
```

-   _Q4.2. Avec une clause query (aggregate) :_

```javascript
db.notes1.aggregate([
	{ $match: { groupe: { $ne: 'A' } } },
	{ $group: { _id: '$groupe', avgNote: { $avg: '$valeur' } } },
]);
```

**Q5. Calculer la moyenne des notes par groupe sur la collection `notes2`.**
(La collection `notes2` doit être créée au préalable en regroupant les notes par étudiant.)

```javascript
db.notes2.aggregate([
	{ $group: { _id: '$groupe', avgNote: { $avg: { $avg: '$valeurs' } } } },
]);
```

**Q6. Calculer la moyenne non coefficientée des notes par groupe sur la collection `notes3`.**

```javascript
db.notes3.aggregate([
	{ $unwind: '$notes' },
	{ $group: { _id: '$groupe', avgNote: { $avg: '$notes.valeur' } } },
]);
```

**Q7. Calculer la moyenne coefficientée des notes par groupe sur la collection `notes3`.**
Supposons que chaque note a un coefficient `coef`.

```javascript
db.notes3.aggregate([
	{ $unwind: '$notes' },
	{
		$group: {
			_id: '$groupe',
			total: { $sum: { $multiply: ['$notes.valeur', '$notes.coef'] } },
			totalCoef: { $sum: '$notes.coef' },
		},
	},
	{
		$project: {
			avgNote: { $divide: ['$total', '$totalCoef'] },
		},
	},
]);
```

---

## Nettoyage final

À la fin du TP, pensez à détruire les instances Docker et supprimer les volumes créés :

```bash
docker rm -f monmongo
docker volume rm mes_data_mongo
```

Vérifiez ensuite que :

```bash
docker ps --all
docker volume ls
```

---

## Conclusion

Ce TP vous a permis de découvrir et d’expérimenter le pattern Map-Reduce dans MongoDB. Vous avez appris à écrire des fonctions JavaScript pour la map et la reduce, à les tester individuellement et à les utiliser via la commande `mapReduce` pour réaliser des agrégations complexes. Par ailleurs, une comparaison avec des requêtes réalisées via `aggregate` vous permet de vérifier la cohérence des résultats.

Pour plus de détails, consultez la [documentation officielle MongoDB sur Map-Reduce](https://www.mongodb.com/docs/manual/core/map-reduce/).
