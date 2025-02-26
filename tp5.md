# TP 5.5 REDIS - CRUD : Réponses et Explications

Ce TP a pour objectif de découvrir Redis, une base de données NoSQL clé-valeur à haute disponibilité, et d'explorer ses principales fonctionnalités : installation, manipulation des différents types de données (chaînes, listes, sets, sorted sets, hashes), gestion du TTL, Pub/Sub et utilisation dans un programme en Java.

---

## Table des Matières

-   [TP 5.5 REDIS - CRUD : Réponses et Explications](#tp-55-redis---crud--réponses-et-explications)
    -   [Table des Matières](#table-des-matières)
    -   [Exercice 1 : Installation du serveur Redis](#exercice-1--installation-du-serveur-redis)
    -   [Exercice 2 : Bien démarrer](#exercice-2--bien-démarrer)
    -   [Exercice 3 : Les chaînes](#exercice-3--les-chaînes)
    -   [Exercice 4 : Le TTL](#exercice-4--le-ttl)
    -   [Exercice 5 : Les listes](#exercice-5--les-listes)
    -   [Exercice 6 : Les sets](#exercice-6--les-sets)
    -   [Exercice 7 : Les sets avec score](#exercice-7--les-sets-avec-score)
    -   [Exercice 8 : Les hashes](#exercice-8--les-hashes)
    -   [Exercice 9 : Pub/Sub](#exercice-9--pubsub)
    -   [Exercice 10 : Utiliser Redis dans un langage de programmation](#exercice-10--utiliser-redis-dans-un-langage-de-programmation)

---

## Exercice 1 : Installation du serveur Redis

**Q1.** Créez le service Docker avec la dernière version de Redis :

```bash
docker run --name monredis -v mes_data_redis:/data -p 6379:6379 -d redis:latest
```

**Q2.** Connectez-vous à ce service via le client standard redis-cli :

```bash
docker exec -it monredis redis-cli
```

ou en ouvrant une session bash dans le conteneur :

```bash
docker exec -it monredis bash
```

---

## Exercice 2 : Bien démarrer

**Q1.** Tapez la commande `PING`
_Attendu :_ Réponse `PONG`

**Q2.** Tapez la commande `DBSIZE`
_Attendu :_ Affichage du nombre de clés dans la base courante (initialement 0)

**Q3.** Tapez la commande `SELECT 5`
_Explication :_ Cette commande change la base active vers la base numéro 5.

**Q4.** Tapez la commande `CONFIG GET *`
_Explication :_ Affiche tous les paramètres de configuration du serveur.

**Q5.** Tapez `CONFIG GET dbfilename`
_Explication :_ Affiche la valeur de la configuration associée à la clé `dbfilename`.

**Q6.** Tapez `CONFIG GET active*`
_Explication :_ Utilise un joker pour rechercher toutes les clés dont le nom commence par "active".

**Q7.** Tapez `QUIT` pour quitter la session redis-cli.

---

## Exercice 3 : Les chaînes

**Q1.** Affectez la clé `nom` à la valeur `paul` :

```bash
SET nom paul
```

**Q2.** Affichez la valeur de la clé `nom` :

```bash
GET nom
```

**Q3.** Affectez la clé `compteur` à la valeur `2` :

```bash
SET compteur 2
```

**Q4.** Incrémentez `compteur` de 1 :

```bash
INCR compteur
```

**Q5.** Affichez la valeur de `compteur` :

```bash
GET compteur
```

**Q6.** Incrémentez `compteur` de 10 :

```bash
INCRBY compteur 10
```

**Q7.** Affichez la valeur de `compteur` :

```bash
GET compteur
```

**Q8.** Affichez toutes les clés présentes dans la base :

```bash
KEYS \*
```

---

## Exercice 4 : Le TTL

**Q1.** Affectez la clé `compteur` à la valeur `20` :

```bash
SET compteur 20
```

**Q2.** Donnez à cette clé une validité de 10 secondes :

```bash
EXPIRE compteur 10
```

**Q3.** Affichez plusieurs fois la validité de la clé jusqu’à obtenir -2 (indiquant que la clé est expirée) :

```bash
TTL compteur
```

**Q4.** Tentez de lire la valeur de `compteur` après expiration :

```bash
GET compteur
```

**Q5.** Affectez une date d’expiration à l’aide d’un timestamp UNIX (exemple) :

```bash
SET compteur 20 EXAT 1672531199
```

**Q6.** Affichez les clés de la base :

```bash
KEYS \*
```

---

## Exercice 5 : Les listes

**Q1.** Créez une liste `chats` avec plusieurs races de chats :

```bash
RPUSH chats birman mainecoon ragdol somali abyssin bengal
```

**Q2.** Affichez la longueur de la liste `chats` :

```bash
LLEN chats
```

**Q3.** Affichez les 3 premiers éléments de la liste :

```bash
LRANGE chats 0 2
```

**Q4.** Rajoutez 3 races à la fin de la liste :

```bash
RPUSH chats burmese siamois persan
```

**Q5.** Affichez la nouvelle longueur de la liste :

```bash
LLEN chats
```

**Q6.** Affichez toute la liste :

```bash
LRANGE chats 0 -1
```

---

## Exercice 6 : Les sets

**Q1.** Créez un set `mult3` contenant tous les multiples de 3 jusqu’à 20 (3, 6, 9, 12, 15, 18) :

```bash
SADD mult3 3 6 9 12 15 18
```

**Q2.** Créez un set `mult5` contenant tous les multiples de 5 jusqu’à 20 (5, 10, 15, 20) ; les doublons seront ignorés :

```bash
SADD mult5 5 5 10 10 15 15 20 20
```

**Q3.** Affichez toutes les valeurs de `mult5` :

```bash
SMEMBERS mult5
```

**Q4.** Combien de valeurs ?
_Réponse :_ 4 (les doublons sont ignorés)

**Q5.** Affichez l’intersection des sets `mult3` et `mult5` :

```bash
SINTER mult3 mult5
```

**Q6.** Rangez dans la clé `res` la différence entre `mult3` et `mult5` :

```bash
SDIFFSTORE res mult3 mult5
```

Pour afficher le résultat :

```bash
SMEMBERS res
```

---

## Exercice 7 : Les sets avec score

**Q1.** Ajoutez dans une clé sorted set `classement` le nom `paul` avec un score de 10 :

```bash
ZADD classement 10 paul
```

**Q2.** Ajoutez le nom `marie` avec un score de 15 :

```bash
ZADD classement 15 marie
```

**Q3.** Ajoutez le nom `louis` avec un score de 3 :

```bash
ZADD classement 3 louis
```

**Q4.** Affichez tout le contenu de `classement` avec leurs scores :

```bash
ZRANGE classement 0 -1 WITHSCORES
```

**Q5.** Affichez le rang de `paul` :

```bash
ZRANK classement paul
```

**Q6.** Effacez `louis` du sorted set :

```bash
ZREM classement louis
```

**Q7.** Affichez à nouveau le rang de `paul` :

```bash
ZRANK classement paul
```

---

## Exercice 8 : Les hashes

**Q1.** Créez un hash `user:1` avec la propriété `nom` à `paul` :

```bash
HSET user:1 nom paul
```

**Q2.** Ajoutez la propriété `age` à `23` :

```bash
HSET user:1 age 23
```

**Q3.** Ajoutez la propriété `email` avec la valeur `paul@gmail.com` :

```bash
HSET user:1 email paul@gmail.com
```

**Q4.** Affichez tous les éléments du hash :

```bash
HGETALL user:1
```

**Q5.** Créez en une seule instruction un hash `user:2` avec les propriétés `nom`, `prenom`, `age` et `email` aux valeurs `virginie`, `virginie`, `25`, `virginie@gmail.com` :

```bash
HMSET user:2 nom virginie prenom virginie age 25 email virginie@gmail.com
```

**Q6.** Affichez toutes les valeurs de `user:2` :

```bash
HGETALL user:2
```

**Q7.** Affichez uniquement la valeur de `nom` de `user:2` :

```bash
HGET user:2 nom
```

---

## Exercice 9 : Pub/Sub

_Configuration :_ Ouvrez trois sessions redis-cli sur le même serveur.

**Q1.** Affectez un nom à chaque client :

-   Dans le client Diffuseur :

```bash
    CLIENT SETNAME Diffuseur
```

-   Dans le client Paul :

```bash
    CLIENT SETNAME Paul
```

-   Dans le client Virginie :

```bash
    CLIENT SETNAME Virginie
```

**Q2.** Affichez la liste des clients connectés :

```bash
CLIENT LIST
```

_Observez l'âge et la durée d'inactivité de chaque client._

**Q3.** Dans le client Paul, tapez :

```bash
SUBSCRIBE sports
```

**Q4.** Dans le client Virginie, tapez :

```bash
SUBSCRIBE news
```

_Les clients seront bloqués en attente de messages._

**Q5.** Dans le client Diffuseur, publiez des messages :

```bash
PUBLISH sports "PSG va à nouveau rencontrer Marseille"
PUBLISH news "Les élections présidentielles auront lieu le 10 avril"
```

_Pour sortir du mode SUBSCRIBE, utilisez Ctrl+C dans le client abonné._

---

## Exercice 10 : Utiliser Redis dans un langage de programmation

Redis est souvent utilisé comme système de cache pour éviter des calculs ou requêtes répétitifs.

**Q1.** Écrivez un programme Java (TP55RestSansCache.java) qui exécute une requête REST GET sur :
