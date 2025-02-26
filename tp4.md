# TP 5.4 MongoDB - Scaling horizontal : Réponses et Explications

Ce TP a pour objectif de vous familiariser avec le déploiement d’un cluster MongoDB, en abordant la réplication, le sharding et la tolérance aux pannes. Vous trouverez ci-dessous des exemples de commandes et des explications pour chaque exercice.

---

## Table des Matières

-   [TP 5.4 MongoDB - Scaling horizontal : Réponses et Explications](#tp-54-mongodb---scaling-horizontal--réponses-et-explications)
    -   [Table des Matières](#table-des-matières)
    -   [Exercice 1 : La réplication – Architecture à 3 machines](#exercice-1--la-réplication--architecture-à-3-machines)
    -   [Exercice 2 : Sharding – Architecture à 7 machines](#exercice-2--sharding--architecture-à-7-machines)
    -   [Exercice 3 : Distribuer des données](#exercice-3--distribuer-des-données)
    -   [Exercice 4 : Écrire un programme](#exercice-4--écrire-un-programme)
    -   [Exercice 5 : Tolérance aux pannes](#exercice-5--tolérance-aux-pannes)
    -   [Nettoyage final](#nettoyage-final)
    -   [Conclusion](#conclusion)

---

## Exercice 1 : La réplication – Architecture à 3 machines

**Q1.** Téléchargez le fichier `tp54_exo1.zip` contenant le fichier `docker-compose.yaml` qui crée un replica set de 3 machines.
_Explication :_ Ce fichier décrit une stack Docker simulant 3 serveurs (node1, node2, node3) dans un replica set nommé « shard1 ».

**Q2.** Étudiez le contenu du fichier `docker-compose.yaml` pour comprendre les noms de conteneurs et les commandes de démarrage.
_Explication :_ Prenez connaissance des ports utilisés, des volumes montés et des commandes d’initialisation.

**Q3.** Lancez la configuration avec :

```bash
docker compose up -d
```

_Explication :_ Cette commande démarre les 3 conteneurs en arrière-plan. Vérifiez leur état avec `docker ps`.

**Q4.** Affichez les options du client `mongosh` avec l’option `--help`.

```bash
mongosh --help
```

_Explication :_ Cela permet de repérer les options pour changer le port ou exécuter des commandes en ligne.

**Q5.** Configurez le replica set sur **node2** (qui utilise le port 27018).
_Explication :_ Connectez-vous à node2 via :

```bash
docker exec -it node2 mongosh
```

Puis, initialisez le replica set avec une commande comme :

```javascript
rs.initiate({
\_id: "shard1",
members: [
{ _id: 0, host: "node2:27018" },
{ _id: 1, host: "node1:27017" },
{ _id: 2, host: "node3:27017" }
]
})
```

**Q6.** Vérifiez les prompts des 3 nœuds en vous connectant à chacun.
_Résultat attendu :_

-   node2 affiche (primary)
-   node1 et node3 affichent (secondary)

**Q7.** Lancez sur n’importe quel nœud la commande :

```javascript
rs.status();
```

_Explication :_ Cette commande fournit des informations détaillées sur le replica set.

**Q8.** Écrivez une requête pour afficher uniquement les valeurs `name` et `stateStr` de l’array `members` de `rs.status()`.
_Exemple de solution en JavaScript dans mongosh :_

```javascript
var status = rs.status();
status.members.forEach(function (member) {
	print('Name: ' + member.name + ' | State: ' + member.stateStr);
});
```

**Q9.** Sur le nœud Primary, dans la base `test`, créez une collection `macoll` et insérez-y un document `{ x: 1 }`.

```javascript
use test
db.macoll.insertOne({ x: 1 })
```

**Q10.** Connectez-vous à un nœud secondaire et affichez la collection `macoll`.

```javascript
use test
db.macoll.find()
```

_Remarque :_ En lecture sur un secondaire, il faut peut-être ajouter l’option `readPreference` pour autoriser la lecture sur le secondaire.

**Q11.** Faites un export de la collection `macoll` à partir de **node1**.

```bash
mongoexport --host node1 --port 27017 -d test -c macoll -o macoll_export.json
```

**Q12.** Tuez brutalement un nœud secondaire (par exemple, `docker kill node1`) et vérifiez le statut du replica set avec `rs.status()`.

**Q13.** Ajoutez une donnée `{ y: 2 }` à la collection `macoll` sur le Primary.

```javascript
db.macoll.insertOne({ y: 2 });
```

**Q14.** Redémarrez le nœud précédemment tué (par exemple, `docker start node1`) et vérifiez de nouveau le statut avec `rs.status()`.

**Q15.** Exportez la collection `macoll` depuis node1 et constatez que le document `{ y: 2 }` est présent, même si node1 était mort lors de l’insertion.
_Explication :_ Cela démontre que la réplication a bien propagé les données.

**Q16.** Tuez brutalement le nœud Primary et, après environ 10 secondes, vérifiez le nouveau statut du replica set.
_Explication :_ Un autre nœud doit être élu Primary automatiquement.

**Q17.** Vérifiez que vous pouvez toujours lire la collection `macoll`.

**Q18.** Redémarrez le nœud précédemment tué et observez les changements.
_Explication :_ Le nœud rejoint le replica set et se synchronise avec les données manquantes.

---

## Exercice 2 : Sharding – Architecture à 7 machines

**Q1.** Téléchargez le fichier `tp54_exo2.zip` contenant le `docker-compose.yaml` pour créer 2 replica sets de données et un replica set de configuration.
_Explication :_ Ce fichier met en place deux shards de données ainsi qu’un ensemble de nœuds de configuration et un routeur mongos.

**Q2.** Étudiez attentivement le fichier pour comprendre la topologie du cluster (noms des nœuds, ports, etc.).

**Q3.** Lancez la configuration :

```bash
docker compose up -d
```

_Explication :_ Vérifiez que les conteneurs démarrent correctement (via `docker ps`).

**Q4.** Configurez le replica set du nœud de configuration (sur le port 27019). Connectez-vous à l’un des conteneurs de configuration et exécutez :

```javascript
rs.initiate({
\_id: "configReplSet",
members: [
{ _id: 0, host: "config1:27019" },
{ _id: 1, host: "config2:27019" },
{ _id: 2, host: "config3:27019" }
]
})
```

Vérifiez avec `rs.status()` que l’un est PRIMARY et les deux autres SECONDARY.

**Q5.** Configurez les 2 replica sets de données (sur le port 27018) de la même manière en vérifiant qu’un membre est PRIMARY et les autres SECONDARY pour chacun.

**Q6.** Connectez-vous au routeur mongos et ajoutez les 2 shards de données. Par exemple :

```javascript
sh.addShard('shard1/node11:27018,node12:27018,node13:27018');
sh.addShard('shard2/node21:27018,node22:27018,node23:27018');
```

_Explication :_ Cette commande indique à mongos où se trouvent les données.

**Q7.** Vérifiez l’état du sharding via :

```javascript
sh.status();
```

Vous devriez voir apparaître vos shards et la liste de leurs nœuds.

---

## Exercice 3 : Distribuer des données

**Q1.** Connectez-vous sur mongos et, dans la base `test`, créez une collection `users` en définissant une clé de sharding basée sur `username`.

```javascript
use test
db.createCollection("users")
sh.enableSharding("test")
sh.shardCollection("test.users", { username: 1 })
```

**Q2.** Passez la taille des chunks à la valeur minimale (1 Mo) :

```bash
use config
db.settings.updateOne(
{ \_id: "chunksize" },
{ $set: { \_id: "chunksize", value: 1 } },
{ upsert: true }
)
```

**Q3.** Retournez dans la base `test` et insérez 10 000 documents dans la collection `users` avec les champs `username`, `age`, `groupe`, `created`. Par exemple, avec une boucle JavaScript dans mongosh :

```javascript
for (let i = 0; i < 10000; i++) {
db.users.insertOne({
username: "user" + i,
age: Math.floor(Math.random() \* 50) + 20,
groupe: ["A", "B", "C"][i % 3],
created: new Date()
});
}
```

**Q4.** Vérifiez la répartition des données sur les shards avec :

```javascript
sh.getShardedDataDistribution();
```

**Q5.** Connectez-vous sur un nœud d’un shard (par exemple, node21 pour shard2) et utilisez `countDocuments()` pour vérifier le nombre de documents sur chaque shard.

**Q6.** Ré-exécutez l’insertion de 10 000 nouveaux documents en boucle jusqu’à observer une répartition effective des chunks. Vérifiez à chaque étape le volume de données sur chaque shard.

**Q7.** À l’aide d’un `find()` et `count()`, effectuez une requête sur mongos pour compter le nombre de documents sur chaque shard et vérifiez que la somme atteint 40 000.

---

## Exercice 4 : Écrire un programme

**Q1.** Écrivez un programme Java qui se connecte à mongos et affiche le nombre de documents dans une collection.
_Conseils :_

-   Utilisez les dépendances : `bson`, `mongodb-driver-core`, `mongodb-driver-sync`.
-   Le programme doit établir une connexion à l’instance mongos et exécuter une requête simple, par exemple :

```java
    import com.mongodb.client.\*;
    import org.bson.Document;

public class CountDocuments {
public static void main(String[] args) {
MongoClient client = MongoClients.create("mongodb://mongos:27017");
MongoDatabase db = client.getDatabase("test");
MongoCollection<Document> coll = db.getCollection("users");
System.out.println("Nombre de documents: " + coll.countDocuments());
client.close();
}
}
```

**Q2.** Testez ce programme et lancez-le dans une boucle shell infinie (par exemple via un script Bash) pour qu’il affiche chaque seconde le résultat.

---

## Exercice 5 : Tolérance aux pannes

Pendant que le programme Java tourne en boucle :

**Q1.** Tuez un nœud secondaire du shard1 (par exemple, `docker kill node12`).
_Observation :_ Le replica set bascule sur un autre nœud secondaire et le programme continue de fonctionner.

**Q2.** Tuez un nœud primaire du shard2 (par exemple, `docker kill node21`).
_Observation :_ Une réélection s’effectue; après quelques secondes, un nouveau primaire est élu et le programme continue.

**Q3.** Tuez un nœud de configuration.
_Observation :_ Le cluster reste opérationnel.

**Q4.** Tuez un autre nœud du shard1.
_Observation :_ Le shard1 devient inactif et les données qu’il gère sont perdues, ce qui fait échouer le programme.

**Q5.** Redémarrez le nœud défaillant du shard1 (`docker start ...`) et relancez le programme.
_Observation :_ Le système retrouve son fonctionnement normal.

---

## Nettoyage final

Une fois l’exercice terminé, arrêtez la stack Docker avec :

```bash
docker compose down -v
```

Vérifiez ensuite avec :

```bash
docker ps --all
docker volume ls
```

_Explication :_ Ces commandes garantissent que toutes les ressources (conteneurs et volumes) créées pour le TP sont supprimées.

---

## Conclusion

Ce TP vous a permis de déployer un cluster MongoDB complet en configurant la réplication et le sharding, et d’observer la tolérance aux pannes dans un environnement distribué. Vous avez également appris à distribuer les données sur plusieurs shards et à développer une application capable de se connecter au cluster via mongos. Pour plus d’informations, consultez la [documentation officielle de MongoDB sur le sharding](https://www.mongodb.com/docs/manual/sharding/) et sur [la réplication](https://www.mongodb.com/docs/manual/replication/).
