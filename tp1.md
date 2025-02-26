# TP 5.1 MongoDB - CRUD : Réponses et Explications

Ce document présente les réponses aux différents exercices du TP 5.1 portant sur les opérations CRUD avec MongoDB, ainsi que des commentaires et explications pour chaque étape.

---

## Table des Matières

1. [Exercice 1 : Installation du serveur MongoDB](#exercice-1--installation-du-serveur-mongodb)
2. [Exercice 2 : Bien démarrer](#exercice-2--bien-démarrer)
3. [Exercice 3 : Premiers pas avec la collection "etudiant"](#exercice-3--premiers-pas-avec-la-collection-etudiant)
4. [Exercice 4 : Le CRUD - Partie R (Read)](#exercice-4--le-crud---partie-r-read)
5. [Exercice 5 : Le CRUD - Parties U (Update) et D (Delete)](#exercice-5--le-crud---parties-u-update-et-d-delete)
6. [Exercice 6 : Retour à Postgres (optionnel)](#exercice-6--retour-à-postgres-optionnel)
7. [Nettoyage final](#nettoyage-final)
8. [Conclusion](#conclusion)

---

## Exercice 1 : Installation du serveur MongoDB

### Q1. Création du service Docker avec la dernière version de MongoDB

```bash
docker run --name monmongo -v mes_data_mongo:/data/db -p 27017:27017 -d mongo:latest
```

**Commentaire :**
Cette commande lance un conteneur Docker nommé `monmongo` en utilisant l'image officielle `mongo:latest`. Le volume `mes_data_mongo` est monté pour assurer la persistance des données dans `/data/db`. Le port 27017 est exposé pour permettre les connexions externes.

### Q2. Connexion au service via le client `mongosh`

```bash
docker exec -it monmongo mongosh
```

ou

```bash
docker exec -it monmongo bash
```

**Commentaire :**
La première commande lance directement le shell MongoDB (`mongosh`) dans le conteneur, tandis que la seconde ouvre une session bash dans le conteneur, où vous pouvez ensuite lancer `mongosh`.

---

## Exercice 2 : Bien démarrer

### Q1. Tester un peu de code JavaScript dans `mongosh`

```javascript
2 + 3
a = 5
2 \* a

function f(x) {
return 2 \* x + 5;
}
f(3)

function fact(n) {
if (n == 0) {
return 1;
} else {
return n \* fact(n - 1);
}
}

for (i = 1; i < 20; i++) {
print(i + "\t" + fact(i));
}
```

**Commentaire :**
MongoDB utilise `mongosh`, qui est un shell JavaScript. Ces commandes permettent de tester des opérations arithmétiques, de définir et d'appeler des fonctions, ainsi que d'exécuter une boucle avec affichage.

### Q2. Lister les bases existantes (admin, config, local)

```javascript
show dbs
```

**Commentaire :**
MongoDB démarre avec trois bases système. La base `test` est aussi présente par défaut, mais n’apparaît dans `show dbs` que lorsqu’elle contient des données.

### Q3. Afficher la base de données courante

```javascript
db;
```

**Commentaire :**
Cette commande affiche la base de données sur laquelle vous travaillez. Par défaut, c'est `test`.

### Q4. Changer de base et revenir à la base par défaut

```javascript
use but3
db // Devrait afficher "but3"

use test
db // Devrait afficher "test"
```

**Commentaire :**
La commande `use` permet de changer de contexte de base. Notez que si une base est vide, elle ne figure pas dans la liste affichée par `show dbs`.

### Q5. Quitter le client `mongosh`

```javascript
exit;
```

**Commentaire :**
Cette commande termine votre session `mongosh`.

---

## Exercice 3 : Premiers pas avec la collection "etudiant"

### Q1. Reconnexion à la base `test`

```javascript
use test
```

### Q2. Création de la collection "etudiant"

```javascript
db.createCollection('etudiant');
```

**Commentaire :**
MongoDB crée automatiquement une collection dès la première insertion, mais cette commande permet de la créer explicitement.

### Q3. Insertion de 4 documents avec des schémas différents

```javascript
// Document avec toutes les clés
db.etudiant.insertOne({ nom: 'DURAND', prenom: 'philippe', ville: 'ici' });

// Document sans la clé "ville"
db.etudiant.insertOne({ nom: 'LEFEBVRE', prenom: 'jacques' });

// Document sans la clé "prenom"
db.etudiant.insertOne({ nom: 'HENRY', ville: 'ici' });

// Document sans la clé "nom"
db.etudiant.insertOne({ prenom: 'lucie', ville: 'la bas' });
```

**Commentaire :**
MongoDB ne force pas l'utilisation d'un schéma fixe. Vous pouvez insérer des documents avec des structures différentes dans la même collection.

### Q4. Insertion d'un document via un objet JavaScript

```javascript
let etu = { nom: 'DURAND', prenom: 'Pierre', ville: 'la bas' };
db.etudiant.insertOne(etu);
```

**Commentaire :**
On crée un objet JavaScript et on l’insère directement. Cela démontre la flexibilité de `mongosh` pour manipuler des objets JavaScript.

### Q5. Afficher le nombre de documents dans la collection

```javascript
db.etudiant.countDocuments();
```

**Commentaire :**
Le résultat attendu est 5, correspondant aux 4 documents insérés précédemment plus celui inséré via l'objet JavaScript.

### Q6. Lister l’ensemble des documents de la collection

```javascript
db.etudiant.find();
```

### Q7. Insertion multiple avec `insertMany`

```javascript
let liste = [
	{ nom: 'ALLART', prenom: 'Kevin', annee: 2023, login: 'kevinallartetu' },
	{ nom: 'COLLIN', prenom: 'Eliott', annee: 2023, login: 'eliottcolinetu' },
	{
		nom: 'DECORTE',
		prenom: 'Anthony',
		annee: 2023,
		login: 'anthonydecorteetu',
	},
	{ nom: 'FLORENT', prenom: 'Cyril', annee: 2023, login: 'cyrilflorentetu' },
	{
		nom: 'FOSSART',
		prenom: 'Thomas',
		annee: 2023,
		login: 'thomasfossartetu',
	},
	{
		nom: 'GRAFTEAUX',
		prenom: 'Edouard',
		annee: 2023,
		login: 'edouardgrafteauxetu',
	},
	{
		nom: 'INFELTA',
		prenom: 'Eliott',
		annee: 2023,
		login: 'eliottinfeltaetu',
	},
	{ nom: 'LHOTE', prenom: 'Basil', annee: 2023, login: 'basillhoteetu' },
	{
		nom: 'LABRE',
		prenom: 'Alexandre',
		annee: 2023,
		login: 'alexandrelabreetu',
	},
	{
		nom: 'LEDUC',
		prenom: 'Alexandre',
		annee: 2023,
		login: 'alexandreleducetu',
	},
	{
		nom: 'MALDEREZ',
		prenom: 'Nathanael',
		annee: 2023,
		login: 'nathanaelmalderezetu',
	},
	{
		nom: 'MILLEVERT',
		prenom: 'Matthieu',
		annee: 2023,
		login: 'matthieumillevertetu',
	},
	{ nom: 'PAREE', prenom: 'Hugo', annee: 2023, login: 'hugopareeetu' },
	{ nom: 'PARENT', prenom: 'Pierre', annee: 2023, login: 'pierreparentetu' },
	{
		nom: 'ROUSERE',
		prenom: 'Alexis',
		annee: 2023,
		login: 'alexisrousereetu',
	},
	{ nom: 'TACCOEN', prenom: 'Theo', annee: 2023, login: 'theotaccoenetu' },
	{ nom: 'THOMAS', prenom: 'Paco', annee: 2023, login: 'pacothomasetu' },
	{
		nom: 'VANOORENBERGHE',
		prenom: 'Amaury',
		annee: 2023,
		login: 'amauryvanoorengergheetu',
	},
	{
		nom: 'VERRIEST',
		prenom: 'Jordan',
		annee: 2023,
		login: 'jordanverriestetu',
	},
];
db.etudiant.insertMany(liste);
```

**Commentaire :**
La méthode `insertMany` permet d'insérer plusieurs documents en une seule commande, ce qui est efficace pour charger un grand nombre d'enregistrements.

### Q8. Afficher la liste des collections créées

```javascript
show collections
```

### Q9. Afficher le premier document de la collection

```javascript
db.etudiant.findOne();
```

### Q10. Afficher les documents contenant le prénom "Paco"

```javascript
db.etudiant.find({ prenom: 'Paco' });
```

### Q11. Afficher uniquement les noms et prénoms des étudiants (sans l'\_id)

```javascript
db.etudiant.find({}, { \_id: 0, nom: 1, prenom: 1 })
```

**Commentaire :**
La projection `{ _id: 0, nom: 1, prenom: 1 }` permet d'exclure le champ `_id` et de n'afficher que `nom` et `prenom`.

### Q12. Supprimer tous les documents contenant le prénom "Alexandre"

```javascript
db.etudiant.deleteMany({ prenom: 'Alexandre' });
```

### Q13. Rechercher l’étudiant "PARENT" et supprimer son document via une variable

```javascript
let x = db.etudiant.findOne({ nom: "PARENT" })
db.etudiant.deleteOne({ \_id: x.\_id })
```

**Commentaire :**
On récupère le document correspondant à "PARENT" dans la variable `x`, puis on utilise son identifiant unique `_id` pour le supprimer.

### Q14. Supprimer toute la collection "etudiant"

```javascript
db.etudiant.drop();
```

### Q15. Quitter le client `mongosh`

```javascript
exit;
```

**Commentaire :**
Cette commande termine votre session `mongosh`.

---

## Exercice 4 : Le CRUD - Partie R (Read)

### Q1. Récupération du fichier `employes.json`

> Le fichier doit être récupéré depuis Moodle (non traité ici).

### Q2. Importation du fichier dans la base `test`

```bash
mongoimport -d test -c employes --file employes.json
```

**Commentaire :**
Cette commande importe le contenu de `employes.json` dans la collection `employes` de la base `test`.

### Q3. Reconnexion à la base `test`

```javascript
use test
```

### Q4. Afficher toutes les collections de la base

```javascript
show collections
```

### Q5. Lister les documents de la collection `employes`

```javascript
db.employes.find();
```

**Commentaire :**
Par défaut, `mongosh` affiche un batch (souvent 20 documents) si le résultat est volumineux.

### Q6. Compter le nombre de documents dans `employes`

```javascript
db.employes.countDocuments();
```

### Q7. Afficher le premier document de la collection

```javascript
db.employes.findOne();
```

### Q8. Différence entre `findOne` et `find().limit(1)`

-   **findOne :** Retourne directement un document (objet JavaScript).
-   **find().limit(1) :** Retourne un curseur contenant un seul document.

**Commentaire :**
`findOne` est plus simple pour obtenir un seul résultat, tandis que `find().limit(1)` permet d'enchaîner d'autres opérations sur le curseur.

### Q9. Afficher trois documents de la collection

```javascript
db.employes.find().limit(3);
```

### Q10. Afficher les employés dont le prénom est "David"

```javascript
db.employes.find({ prenom: 'David' });
```

### Q11. Afficher uniquement les codes postaux (sans l'\_id)

```javascript
db.employes.find({}, { \_id: 0, code_postal: 1 })
```

**Commentaire :**
Assurez-vous que le champ dans vos documents correspond bien à `code_postal`.

### Q12. Afficher les prénoms sans doublon

```javascript
db.employes.distinct('prenom');
```

### Q13. Compter le nombre de prénoms distincts

```javascript
db.employes.distinct('prenom').length;
```

### Q14. Compter les prénoms distincts commençant par "D"

```javascript
db.employes.distinct('prenom', { prenom: /^D/ }).length;
```

### Q15. Afficher la liste des employés dont le prénom commence par "D" ou se termine par "e"

```javascript
db.employes.find({ $or: [{ prenom: /^D/ }, { prenom: /e$/ }] });
```

### Q16. Afficher les données triées sur le prénom

```javascript
db.employes.find().sort({ prenom: 1 });
```

### Q17. Afficher uniquement les nom et prénom des employés ayant une ancienneté > 10

```javascript
db.employes.find({ anciennete: { $gt: 10 } }, { \_id: 0, nom: 1, prenom: 1 })
```

### Q18. Afficher nom, prénom et ancienneté des employés dont le prénom est "David" ou l’ancienneté > 20

```javascript
db.employes.find(
	{ $or: [{ prenom: 'David' }, { anciennete: { $gt: 20 } }] },
	{ nom: 1, prenom: 1, anciennete: 1 }
);
```

### Q19. Refaire la requête précédente avec une projection inclusive (excluant \_id)

```javascript
db.employes.find(
{ $or: [ { prenom: "David" }, { anciennete: { $gt: 20 } } ] },
{ \_id: 0, nom: 1, prenom: 1, anciennete: 1 }
)
```

**Commentaire :**
Ici, la projection inclut uniquement les champs désirés tout en excluant `_id`.

### Q20. Afficher les noms des employés ayant touché une prime

```javascript
db.employes.find(
{ prime: { $exists: true } },
{ \_id: 0, nom: 1 }
)
```

### Q21. Afficher le nom et l’adresse complète des employés possédant l'attribut "rue" dans "adresse"

```javascript
db.employes.find(
{ "adresse.rue": { $exists: true } },
{ \_id: 0, nom: 1, adresse: 1 }
)
```

### Q22. Afficher les trois premières personnes ayant la plus grande ancienneté

```javascript
db.employes.find().sort({ anciennete: -1 }).limit(3);
```

### Q23. Afficher les employés dont la ville de résidence est "Toulouse" (nom, prénom, ancienneté)

```javascript
db.employes.find(
{ "adresse.ville": "Toulouse" },
{ \_id: 0, nom: 1, prenom: 1, anciennete: 1 }
)
```

### Q24. Afficher les statistiques sur la base courante

```javascript
db.stats();
```

---

## Exercice 5 : Le CRUD - Parties U (Update) et D (Delete)

### Q1. Incrémenter de 200 la prime des employés ayant déjà une prime

```javascript
db.employes.updateMany({ prime: { $exists: true } }, { $inc: { prime: 200 } });
```

**Commentaire :**
La commande `$inc` augmente la valeur du champ `prime` de 200 pour chaque document ciblé.

### Q2. Supprimer le champ `prime` de tous les documents

```javascript
db.employes.updateMany({}, { $unset: { prime: '' } });
```

### Q3. Mettre à jour l’adresse de Dominique Mani

```javascript
db.employes.updateOne(
	{ nom: 'Mani', prenom: 'Dominique' },
	{
		$set: {
			'adresse.numero': 20,
			'adresse.ville': 'Marseille',
			'adresse.codepostal': '13015',
		},
		$unset: { 'adresse.rue': '' },
	}
);
```

**Commentaire :**
La combinaison de `$set` et `$unset` permet de mettre à jour certains champs tout en supprimant d'autres (ici, le champ `rue`).

### Q4. Ajouter une clé `ordi` avec un tableau vide à tous les employés

```javascript
db.employes.updateMany({}, { $set: { ordi: [] } });
```

### Q5. Ajouter la valeur "macbook" à la clé `ordi` pour Jean Dupond

```javascript
db.employes.updateOne(
	{ nom: 'Dupond', prenom: 'Jean' },
	{ $push: { ordi: 'macbook' } }
);
```

### Q6. Supprimer la clé `ordi` de tous les documents

```javascript
db.employes.updateMany({}, { $unset: { ordi: '' } });
```

### Q7. Créer une copie de la collection `employes` dans `employes2`

```javascript
db.employes.aggregate([{ $match: {} }, { $out: 'employes2' }]);
```

**Commentaire :**
L'opérateur `$out` permet d'exporter le résultat d'une agrégation dans une nouvelle collection.

### Q8. Créer une nouvelle collection `result1` avec uniquement les champs `nom`, `prenom` et `ville`

```javascript
db.employes.aggregate([
	{ $project: { _id: 0, nom: 1, prenom: 1, ville: 1 } },
	{ $out: 'result1' },
]);
```

### Q9. Créer une nouvelle collection `result2` constituée des employés dont le prénom est "David"

```javascript
db.employes.aggregate([{ $match: { prenom: 'David' } }, { $out: 'result2' }]);
```

### Q10. Supprimer de la collection `employes` tous les employés dont le nom commence par "M"

```javascript
db.employes.deleteMany({ nom: { $regex: /^M/ } });
```

**Commentaire :**
L'expression régulière `/^M/` cible les documents dont le champ `nom` débute par la lettre "M".

---

## Exercice 6 : Retour à Postgres (Optionnel)

### Q1. Suivre le tutoriel fourni

Consultez le [PostgreSQL JSON Tutorial](https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-json/) pour une introduction à la gestion du JSON dans Postgres.

### Q2. Tester la fonction `row_to_json` sur une table (exemple avec la table `livre`)

```sql
SELECT row_to_json(l) FROM livre l;
```

### Q3. Utiliser la fonction d'agrégation `json_agg`

```sql
SELECT json_agg(l) FROM livre l GROUP BY ano;
```

**Commentaire :**
`json_agg` agrège les lignes d'une table dans un tableau JSON, utile pour regrouper des résultats.

### Q4. Utiliser `json_build_object` pour fabriquer des documents JSON

```sql
SELECT json_build_object(
'auteur', a.nom,
'livres', json_agg(l)
)
FROM auteur a
JOIN livre l ON a.id = l.auteur_id
GROUP BY a.nom;
```

### Q5. Exporter les auteurs avec leurs livres en JSON et importer dans MongoDB

-   Exportez le résultat en JSON (via un outil ou une commande SQL).
-   Utilisez ensuite `mongoimport` pour importer le fichier JSON dans MongoDB.

---

## Nettoyage final

À la fin du TP, pensez à détruire les instances Docker utilisées :

```bash
docker rm -f monmongo
docker volume rm mes_data_mongo
```

Vérifiez ensuite que :

```bash
docker ps --all
docker volume ls
```

**Commentaire :**
Ces commandes assurent que toutes les ressources (conteneurs et volumes) créés pour le TP sont bien supprimées.

---

## Conclusion

Ce TP vous a permis d'explorer les opérations CRUD avec MongoDB, depuis l'installation du serveur jusqu'aux manipulations avancées (agrégations, mises à jour et suppressions) et même l'interopérabilité avec Postgres pour la gestion du JSON. Chaque commande a été accompagnée d'explications pour faciliter la compréhension du fonctionnement de MongoDB et l'utilisation de `mongosh`.

Pour plus de détails, référez-vous à la [documentation officielle MongoDB](https://docs.mongodb.com/manual/).
