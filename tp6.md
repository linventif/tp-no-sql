# TP 5.6 REDIS - Scaling horizontal : Réponses et Explications

Ce TP a pour objectif de comprendre les mécanismes avancés de Redis et notamment les outils de scaling horizontal. Vous explorerez la persistance, le monitoring, la réplication, l’utilisation des sentinelles et l’externalisation de la gestion des sessions dans Redis.

---

## Table des Matières

-   [TP 5.6 REDIS - Scaling horizontal : Réponses et Explications](#tp-56-redis---scaling-horizontal--réponses-et-explications)
    -   [Table des Matières](#table-des-matières)
    -   [Exercice 1 : La persistance](#exercice-1--la-persistance)
    -   [Exercice 2 : Moniteurs](#exercice-2--moniteurs)
    -   [Exercice 3 : La réplication](#exercice-3--la-réplication)
    -   [Exercice 4 : Les Sentinelles](#exercice-4--les-sentinelles)
    -   [Exercice 5 : Externaliser la gestion des sessions](#exercice-5--externaliser-la-gestion-des-sessions)
        -   [Version Redis avec Spring Boot](#version-redis-avec-spring-boot)
    -   [Conclusion](#conclusion)

---

## Exercice 1 : La persistance

1. **Assurez-vous qu’un serveur Redis est disponible**
   Lancer le service Redis via Docker :

```bash
   docker run --name monredis -v mes_data_redis:/data -p 6379:6379 -d redis:latest
```

2. **Ouvrez deux terminaux côte à côte.**

3. **Dans le premier terminal :**
   Lancez le client redis-cli :

```bash
   docker exec -it monredis redis-cli
```

Vous pouvez utiliser les commandes suivantes :

    - `INFO keyspace` pour vérifier le nombre de clés présentes.
    - `KEYS *` pour lister toutes les clés.
    - `FLUSHDB` pour détruire toutes les clés de la base courante.
    - `FLUSHALL` pour détruire toutes les clés de toutes les bases.

4. **Dans le second terminal :**
   Connectez-vous en bash sur le conteneur :

```bash
   docker exec -it monredis bash
```

5. **Vérifiez les paramètres de sauvegarde**
   Exécutez la commande suivante pour voir quand une clé sera sauvegardée :

```bash
   CONFIG GET save
```

6. **Créez une nouvelle clé `cpt` avec la valeur 5 :**
   Dans redis-cli :

```bash
   SET cpt 5
```

7. **Vérifiez la présence (et la date) du fichier `dump.rdb`**
   Dans le terminal bash, exécutez :

```bash
   ls -l /data
```

Vous constaterez qu'aucune sauvegarde n'a encore été effectuée.

8. **Forcez une sauvegarde sur disque avec la commande `SAVE` :**
   Dans redis-cli :

```bash
   SAVE
```

9. **Re-vérifiez la date du fichier `dump.rdb`**
   Dans le terminal bash, utilisez de nouveau :

```bash
   ls -l /data
```

10. **Changez le mode de sauvegarde pour qu'une sauvegarde soit effectuée toutes les 10 secondes si au moins 2 clés ont été modifiées**
    Par exemple, appliquez :

```bash
    CONFIG SET save "10 2"
```

11. **Testez le bon fonctionnement**
    Ajoutez deux clés et vérifiez que la date du fichier `dump.rdb` se met à jour.

12. **Passez en mode AOF**
    Dans redis-cli, activez le mode AOF :

```bash
    CONFIG SET appendonly yes
```

13. **Modifiez une clé pour observer la sauvegarde incrémentale**
    Par exemple :

```bash
    SET cpt 10
```

    Vérifiez ensuite la date du fichier AOF (`appendonly.aof`).

---

## Exercice 2 : Moniteurs

1. **Ouvrez deux terminaux côte à côte.**

2. **Dans le premier terminal :**
   Lancez un client redis-cli classique.

3. **Dans le second terminal :**
   Lancez le mode moniteur :

```bash
   redis-cli monitor
```

Le client se bloque en mode monitoring.

4. **Dans le premier terminal, effectuez des opérations**
   Par exemple :

    - `SET test "value"`
    - `GET test`
      Observez les messages correspondants dans le moniteur.

5. **Utilisez les options de répétition et d’intervalle**
   Exécutez la commande suivante pour incrémenter la clé `compteur` 10 fois avec un délai de 1 seconde :

```bash
   redis-cli -r 10 -i 1 INCR compteur
```

6. **Notez que plusieurs moniteurs peuvent être lancés simultanément**
   Ceci est utile pour surveiller l'activité de différents clients ou sur différentes machines.

---

## Exercice 3 : La réplication

1. **Supprimez l’ancienne instance Redis pour éviter les conflits :**

```bash
   docker rm -f monredis
```

2. **Téléchargez le fichier `tp56_exo3.zip`**
   Ce fichier contient la configuration docker-compose pour créer un maître et 2 réplicas.

3. **Lancez la configuration avec docker-compose :**

```bash
   docker compose up -d
```

4. **Ouvrez deux terminaux.**

5. **Dans le premier terminal :**
   Lancez redis-cli connecté au maître.

6. **Dans le second terminal :**
   Lancez redis-cli connecté à `replica1`.

7. **Vérifiez le rôle des serveurs**
   Exécutez la commande :

```bash
   ROLE
```

Le maître doit afficher "master" et le réplica "slave".

8. **Sur le maître, affectez une clé :**
   Par exemple :

```bash
   SET maCle "valeur"
```

9. **Sur le réplica, vérifiez que la clé est bien répliquée** :

```bash
   GET maCle
```

10. **Tentez d’affecter une clé sur le réplica**
    Cela doit échouer car le réplica est en lecture seule.

11. **Testez la synchronisation**
    Arrêtez le réplica :

```bash
    docker stop replica1
```

    Ajoutez quelques clés sur le maître, puis redémarrez le réplica :

```bash
    docker start replica1
```

    Vérifiez que toutes les clés sont bien présentes sur le réplica.

12. **Une fois terminé, arrêtez la stack avec :**

```bash
    docker compose down -v
```

---

## Exercice 4 : Les Sentinelles

1. **Récupérez le répertoire `tp56_exo4.zip`**
   Ce répertoire contient la configuration pour mettre en place 3 sentinelles ainsi que le maître et les réplicas.

2. **Examinez les fichiers de configuration et le Dockerfile fourni.**

3. **Lancez la configuration avec docker-compose :**

```bash
   docker-compose up -d
```

Vous aurez alors 6 machines : 1 maître, 2 réplicas et 3 sentinelles.

4. **Connectez-vous sur le maître et affectez une clé :**
   Par exemple :

```bash
   SET maitre "ok"
```

5. **Connectez-vous sur `replica1` et vérifiez :**

    - a) Qu’il est bien en mode slave (utilisez la commande `ROLE`).
    - b) Que la clé `maitre` a été répliquée.
    - c) Qu’il n’est pas possible d’affecter une clé en écriture.

6. **Arrêtez le maître :**
   Par exemple :

```bash
   docker stop maitre
```

7. **Attendez environ 10 secondes, puis reconnectez-vous sur `replica1` :**
   Vérifiez qu’il est passé en mode master et que la clé `maitre` est toujours présente.

8. **Affectez une nouvelle clé sur `replica1` :**
   Par exemple :

```bash
   SET replica1 "ok"
```

9. **Vérifiez sur `replica2` que toutes les clés (créées sur le maître et `replica1`) sont bien répliquées.**

10. **Une fois l’exercice terminé, arrêtez la stack avec :**

```bash
    docker-compose down -v
```

---

## Exercice 5 : Externaliser la gestion des sessions

Ce scénario illustre l’utilisation de Redis comme système de gestion de sessions pour des applications web.

### Version Redis avec Spring Boot

1. **Créez une application Spring Boot simple**
   Utilisez la commande suivante pour générer un projet :

```bash
   spring init --build=maven -d=web,devtools,session,redis -g=fr.but3 tp56
```

2. **Modifiez le fichier `pom.xml` pour activer la dépendance `spring-session-data-redis`**
   Si une dépendance `spring-session-jdbc` est présente, commentez-la.

3. **Configurez `application.properties` pour Redis :**

```properties
   spring.session.store-type=redis
   spring.session.redis.namespace=spring:session
   spring.session.timeout=900
```

4. **Créez un contrôleur REST simple** qui stocke des données en session et renvoie "OK".
   Exemple :

```java
   package fr.but3.tp56;

    import jakarta.servlet.http.HttpSession;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class SessionController {
    @GetMapping("/")
    public String home(HttpSession session) {
    session.setAttribute("timestamp", System.currentTimeMillis());
    return "OK!";
    }
    }
```

5. **Lancez l’application et utilisez plusieurs clients (ou navigation privée)**
   Cela créera plusieurs sessions.

6. **Vérifiez dans Redis**
   Par exemple, connectez-vous avec redis-cli et exécutez :

```bash
   KEYS "spring:session\*"
```

Vous devriez voir les clés de session stockées avec le préfixe `spring:session`.

---

## Conclusion

Ce TP vous a permis d’explorer des mécanismes avancés de Redis pour le scaling horizontal. Vous avez appris à configurer la persistance (en mode RDB et AOF), à surveiller l’activité avec le mode monitor, à mettre en place la réplication (maître et réplicas), à utiliser les sentinelles pour assurer une haute disponibilité, et à externaliser la gestion des sessions dans une application Spring Boot. Ces techniques sont essentielles pour garantir la résilience et la scalabilité d’une infrastructure Redis en production.
