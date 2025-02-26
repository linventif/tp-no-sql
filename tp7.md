# TP 5.7 CASSANDRA : Réponses et Explications

Ce TP vous permet de découvrir Cassandra, une base de données NoSQL orientée colonnes adaptée aux très gros volumes de données. Vous apprendrez à installer Cassandra, créer un keyspace et des tables, insérer et interroger des données, et à gérer les contraintes sur les requêtes. Vous explorerez également la mise en place d’un cluster multi-nœuds et la tolérance aux pannes.

---

## Table des Matières

-   [TP 5.7 CASSANDRA : Réponses et Explications](#tp-57-cassandra--réponses-et-explications)
    -   [Table des Matières](#table-des-matières)
    -   [Exercice 1 : Installation du serveur Cassandra](#exercice-1--installation-du-serveur-cassandra)
    -   [Exercice 2 : Bien démarrer](#exercice-2--bien-démarrer)
    -   [Exercice 3 : Les contraintes sur le WHERE](#exercice-3--les-contraintes-sur-le-where)
    -   [Exercice 4 : Les contraintes sur le GROUP BY](#exercice-4--les-contraintes-sur-le-group-by)
    -   [Exercice 5 : Les contraintes sur le ORDER BY](#exercice-5--les-contraintes-sur-le-order-by)
    -   [Exercice 6 : Suivi de produits dans un magasin](#exercice-6--suivi-de-produits-dans-un-magasin)
    -   [Exercice 7 : Mise en place d’un cluster](#exercice-7--mise-en-place-dun-cluster)
    -   [Exercice 8 : Tolérance aux pannes](#exercice-8--tolérance-aux-pannes)
    -   [Conclusion](#conclusion)

---

## Exercice 1 : Installation du serveur Cassandra

**Q1.** Créez le service Docker avec la dernière version de Cassandra :

```bash
docker run --rm --name moncassandra -v mes_data_cassandra:/var/lib/cassandra -p 9042:9042 -d cassandra:latest
```

**Q2.** Connectez-vous à ce service via le client cqlsh :

```bash
docker exec -it moncassandra cqlsh
```

_Ou utilisez bash pour ensuite lancer cqlsh._

---

## Exercice 2 : Bien démarrer

**Q1.** Créez un keyspace nommé `db` et placez-vous dedans :

```cql
CREATE KEYSPACE db
WITH REPLICATION = { 'class': 'SimpleStrategy', 'replication_factor': 1 };
USE db;
```

**Q2.** Listez les keyspaces disponibles :

```cql
DESCRIBE KEYSPACES;
```

**Q3.** Créez une table `students` avec `nom` comme partition key et `prenom`, `groupe` comme clustering keys :

```cql
CREATE TABLE students (
nom text,
prenom text,
groupe text,
age int,
PRIMARY KEY ((nom), prenom, groupe)
);
```

**Q4.** Listez les tables disponibles :

```cql
DESCRIBE TABLES;
```

**Q5.** Insérez les 4 lignes suivantes (format SQL standard) :

```cql
INSERT INTO students (nom, prenom, groupe, age) VALUES ('Lefebvre', 'Léa', 'A', 20);
INSERT INTO students (nom, prenom, groupe, age) VALUES ('Lefebvre', 'Théo', 'B', 22);
INSERT INTO students (nom, prenom, groupe, age) VALUES ('Lefebvre', 'Lucas', 'B', 22);
INSERT INTO students (nom, prenom, groupe, age) VALUES ('Dubois', 'Clara', 'A', 20);
```

Vérifiez avec :

```cql
SELECT \* FROM students;
```

**Q6.** Insérez les 2 nouvelles lignes en format JSON :

```cql
INSERT INTO students JSON '{"nom": "Dubois", "prenom": "Inès", "groupe": "B", "age": 22}';
INSERT INTO students JSON '{"nom": "Dubois", "prenom": "Enzo", "groupe": "C", "age": 20}';
```

Vérifiez avec :

```cql
SELECT \* FROM students;
```

**Q7.** Affichez le contenu de la table en format JSON :

```cql
SELECT JSON \* FROM students;
```

**Q8.** Insérez une nouvelle étudiante avec TTL (1 minute) appliqué aux colonnes non clés :

```cql
INSERT INTO students (nom, prenom, groupe, age)
VALUES ('Moreau', 'Manon', 'C', 25) USING TTL 60;
```

**Q9.** Affichez le TTL de la colonne `age` :

```cql
SELECT ttl(age) FROM students;
```

**Q10.** Quittez cqlsh. Relancez-le avec l’option `--help` pour vérifier les options (notamment `-k` et `-e`) :

```bash
cqlsh --help
```

**Q11.** Affichez la liste des tables du keyspace `db` en ligne de commande (sans être connecté) :

```bash
cqlsh -e "DESCRIBE KEYSPACE db"
```

---

## Exercice 3 : Les contraintes sur le WHERE

Pour chaque requête, vérifiez qu’elle fonctionne sans erreurs :

**Q1.** Sélectionnez les Enzo (ici, 'Enzo' appartient à 'Dubois') :

```cql
SELECT \* FROM students WHERE nom = 'Dubois' AND prenom = 'Enzo';
```

**Q2.** Sélectionnez les Dubois :

```cql
SELECT \* FROM students WHERE nom = 'Dubois';
```

**Q3.** Sélectionnez les Dubois dont le prénom est Enzo :

```cql
SELECT \* FROM students WHERE nom = 'Dubois' AND prenom = 'Enzo';
```

**Q4.** Inversez le test (sélectionnez par prénom puis nom) :

```cql
SELECT \* FROM students WHERE prenom = 'Enzo' AND nom = 'Dubois' ALLOW FILTERING;
```

**Q5.** Sélectionnez les Dubois dont le groupe est C :

```cql
SELECT \* FROM students WHERE nom = 'Dubois' AND groupe = 'C' ALLOW FILTERING;
```

**Q6.** Sélectionnez les Dubois dont l’âge est 20 :

```cql
SELECT \* FROM students WHERE nom = 'Dubois' AND age = 20 ALLOW FILTERING;
```

**Q7.** Créez un index sur l’âge et retestez la requête :

```cql
CREATE INDEX ON students(age);
SELECT \* FROM students WHERE nom = 'Dubois' AND age = 20;
```

---

## Exercice 4 : Les contraintes sur le GROUP BY

_Note : Cassandra supporte les agrégations de base avec GROUP BY sur les colonnes de clustering._

**Q1.** Affichez le nombre de lignes de la table `students` :

```cql
SELECT count(\*) FROM students;
```

**Q2.** Affichez le nombre de noms identiques (par exemple, groupons par nom) :

```cql
SELECT nom, count(\*) FROM students GROUP BY nom;
```

**Q3.** Affichez le nombre de Lefebvre :

```cql
SELECT count(\*) FROM students WHERE nom = 'Lefebvre';
```

**Q4.** Affichez la taille des groupes (nombre de lignes par groupe) :

```cql
SELECT groupe, count(\*) FROM students GROUP BY groupe ALLOW FILTERING;
```

**Q5.** Affichez le nombre de Lefebvre par prénom :

```cql
SELECT prenom, count(\*) FROM students WHERE nom = 'Lefebvre' GROUP BY prenom;
```

**Q6.** Affichez le nombre de Lefebvre dans chaque groupe :

```cql
SELECT groupe, count(\*) FROM students WHERE nom = 'Lefebvre' GROUP BY groupe;
```

---

## Exercice 5 : Les contraintes sur le ORDER BY

Les requêtes ORDER BY sont possibles uniquement sur les colonnes de clustering.

**Q1.** (N/A) Le tri sur la partition n’est pas applicable de manière générale.

**Q2.** Affichez les students de 'Lefebvre' triés par prénom croissant :

```cql
SELECT \* FROM students WHERE nom = 'Lefebvre' ORDER BY prenom ASC;
```

**Q3.** Affichez les Dubois triés par prénom décroissant :

```cql
SELECT \* FROM students WHERE nom = 'Dubois' ORDER BY prenom DESC;
```

**Q4.** Affichez les Dubois triés par groupe croissant :

```cql
SELECT \* FROM students WHERE nom = 'Dubois' ORDER BY groupe ASC;
```

**Q5.** Affichez les Dubois triés par prénom croissant :

```cql
SELECT \* FROM students WHERE nom = 'Dubois' ORDER BY prenom ASC;
```

**Q6.** Recréez la table avec un clustering order ascendant sur `prenom`, puis retestez la requête :

```cql
DROP TABLE students;
CREATE TABLE students (
nom text,
prenom text,
groupe text,
age int,
PRIMARY KEY ((nom), prenom, groupe)
) WITH CLUSTERING ORDER BY (prenom ASC, groupe ASC);
```

Réinsérez les données et testez :

```cql
SELECT \* FROM students WHERE nom = 'Dubois' ORDER BY prenom ASC;
```

---

## Exercice 6 : Suivi de produits dans un magasin

Après étude des requêtes fréquentes, on doit concevoir une table pour stocker les produits.

**Q2.** Déterminez la clé de partition :
Utilisez `categorie` comme clé de partition (la requête la plus fréquente porte sur une catégorie donnée).

**Q3.** Choisissez les colonnes de clustering :
Utilisez `id` comme clé de clustering pour identifier de façon unique chaque produit.

**Q4.** Créez la table `produits` :

```cql
CREATE TABLE produits (
categorie text,
id uuid,
nom text,
prix decimal,
stock int,
PRIMARY KEY (categorie, id)
);
```

**Q5.** Insérez quelques données, par exemple :

```cql
INSERT INTO produits (categorie, id, nom, prix, stock) VALUES ('Electronics', uuid(), 'Smartphone', 399.99, 50);
INSERT INTO produits (categorie, id, nom, prix, stock) VALUES ('Electronics', uuid(), 'Tablet', 299.99, 30);
```

Testez la requête :

```cql
SELECT \* FROM produits WHERE categorie = 'Electronics';
```

**Q6.** Quittez cqlsh.

---

## Exercice 7 : Mise en place d’un cluster

**Q1.** Nettoyez votre espace Docker en supprimant toutes les instances et les volumes.

**Q2.** Créez un réseau Docker nommé `monreseau` :

```bash
docker network create monreseau
```

**Q3.** Créez le premier nœud (seed) :

```bash
docker run --name cassandra1 --network monreseau -e CASSANDRA_NUM_TOKENS=2 -d cassandra:latest
```

**Q4.** Affichez les logs pour attendre le message "Startup complete" :

```bash
docker logs -f cassandra1
```

Interrompez avec Ctrl+C une fois prêt.

**Q5.** Créez le second nœud :

```bash
docker run --name cassandra2 --network monreseau -e CASSANDRA_SEEDS=cassandra1 -e CASSANDRA_NUM_TOKENS=2 -d cassandra:latest
```

**Q6.** Vérifiez avec `nodetool status` sur cassandra1 que les nœuds sont UP (UN).

**Q7.** Créez le troisième nœud :

```bash
docker run --name cassandra3 --network monreseau -e CASSANDRA_SEEDS=cassandra1 -e CASSANDRA_NUM_TOKENS=2 -d cassandra:latest
```

**Q8.** Utilisez `nodetool ring` sur cassandra1 pour visualiser la distribution des tokens.

**Q9.** Connectez-vous à cassandra1 :

```bash
docker exec -it cassandra1 cqlsh
```

**Q10.** Créez un keyspace `db` avec réplication factor 2 :

```cql
CREATE KEYSPACE db
WITH REPLICATION = { 'class': 'SimpleStrategy', 'replication_factor': 2 };
```

**Q11.** Créez la table `students` dans le keyspace `db` :

```cql
USE db;
CREATE TABLE students (
nom text,
prenom text,
groupe text,
age int,
PRIMARY KEY ((nom), prenom, groupe)
);
```

**Q12.** Insérez 7 lignes en changeant de nœud :

_Sur cassandra1 :_

```cql
INSERT INTO students (nom, prenom, groupe, age) VALUES ('Lefebvre', 'Léa', 'A', 20);
INSERT INTO students (nom, prenom, groupe, age) VALUES ('Lefebvre', 'Théo', 'B', 22);
```

_Sur cassandra2 :_

```cql
INSERT INTO students (nom, prenom, groupe, age) VALUES ('Lefebvre', 'Lucas', 'B', 22);
INSERT INTO students (nom, prenom, groupe, age) VALUES ('Dubois', 'Clara', 'A', 20);
```

_Sur cassandra3 :_

```cql
INSERT INTO students (nom, prenom, groupe, age) VALUES ('Dubois', 'Inès', 'B', 22);
INSERT INTO students (nom, prenom, groupe, age) VALUES ('Dubois', 'Enzo', 'C', 20);
INSERT INTO students (nom, prenom, groupe, age) VALUES ('Moreau', 'Manon', 'C', 25);
```

**Q13.** Utilisez `nodetool getendpoints` pour déterminer sur quels nœuds chaque clé est stockée (complétez un tableau pour Lefebvre, Dubois, Moreau).

**Q14.** Expliquez le placement de la clé de partition "Moreau" à l’aide de la fonction `token()` :

```cql
SELECT token(nom) FROM students WHERE nom = 'Moreau';
```

_Explication :_ Cassandra hache la clé de partition pour déterminer le nœud responsable.

**Q15.** Affichez la première ligne de chaque partition avec la clause `PER PARTITION LIMIT` :

```cql
SELECT \* FROM students PER PARTITION LIMIT 1;
```

---

## Exercice 8 : Tolérance aux pannes

**Q1.** Vérifiez les données présentes dans le keyspace `db` :

```cql
SELECT \* FROM students;
```

**Q2.** Tuez un nœud au hasard (par exemple, cassandra1) :

```bash
docker kill cassandra1
```

**Q3.** Visualisez le contenu de la table `students` depuis un autre nœud (les données doivent être accessibles).

**Q4.** Visualisez la répartition des tokens via `nodetool ring` sur un nœud actif.

**Q5.** Redémarrez le nœud tué :

```bash
docker start cassandra1
```

**Q6.** Vérifiez à nouveau la distribution des tokens.

**Q7.** Vérifiez le niveau de consistance du cluster (effectuez plusieurs lectures).

**Q8.** Tuez deux nœuds simultanément et observez le comportement du cluster :
_Explication :_ Avec un facteur de réplication de 2, la perte de deux nœuds peut entraîner une indisponibilité partielle ou totale selon la distribution des données.

---

## Conclusion

Ce TP vous a permis de découvrir Cassandra depuis son installation via Docker jusqu’à la création d’un keyspace et de tables, l’insertion et la manipulation des données via CQL, et l’exploration des contraintes sur les requêtes (WHERE, GROUP BY, ORDER BY). Vous avez également mis en place un cluster multi-nœuds et étudié la tolérance aux pannes, ce qui est fondamental pour assurer la haute disponibilité dans un environnement distribué. Pour plus de détails, consultez la [documentation officielle de Cassandra](https://cassandra.apache.org/doc/latest/).
