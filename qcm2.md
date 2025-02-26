# QCM NoSQL Databases – Part 2: Redis Advanced & Scaling

This quiz covers Redis features such as data types, persistence, replication, monitoring, and advanced scaling options like Sentinel.
Answer each question by selecting the best option.

---

**Q1.** What best describes Redis?
A) A disk-based relational database
B) An in-memory data structure store
C) A document-oriented database
D) A search engine

**Q2.** Which command returns the number of keys in the current Redis database?
A) DBSIZE
B) KEYS \*
C) COUNT
D) INFO keys

**Q3.** When you type `PING` in redis-cli, what is the expected reply?
A) PING
B) HELLO
C) PONG
D) OK

**Q4.** Which command clears all keys from the current database in Redis?
A) DELALL
B) FLUSHDB
C) CLEAR
D) RESET

**Q5.** How can you set a Time-To-Live (TTL) on a key in Redis?
A) EXPIRE
B) TTLSET
C) TIMEOUT
D) SETTTL

**Q6.** What is the default port number for Redis?
A) 27017
B) 9042
C) 6379
D) 8080

**Q7.** In Redis, what type of data is stored using the SET command?
A) Lists
B) Strings
C) Hashes
D) Sets

**Q8.** Which command is used to add an element to the end of a list in Redis?
A) LPUSH
B) RPUSH
C) APPEND
D) ADDLIST

**Q9.** How do you retrieve a range of elements from a Redis list?
A) LRANGE
B) LSLICE
C) LISTGET
D) RANGE

**Q10.** How does Redis handle duplicate members in a set?
A) Duplicates are stored multiple times
B) Duplicates are ignored
C) Duplicates cause an error
D) Duplicates replace the original value

**Q11.** What is a sorted set (zset) in Redis?
A) A set that maintains a unique order based on scores
B) A set that allows duplicate elements
C) A list with random ordering
D) A hash with sorted keys

**Q12.** Which command increments a numeric value stored as a string in Redis?
A) INCR
B) ADD
C) PLUS
D) INCREASE

**Q13.** What does the `CONFIG GET` command do in Redis?
A) Modifies configuration parameters
B) Retrieves configuration parameters
C) Resets configuration to default
D) Deletes configuration settings

**Q14.** Which persistence mode in Redis saves data based on periodic snapshots?
A) AOF
B) RDB
C) CACHE
D) LOGGING

**Q15.** Which persistence mode logs every write operation in Redis?
A) RDB
B) AOF
C) DUMP
D) SNAPSHOT

**Q16.** What is the purpose of replication in Redis?
A) To improve query performance
B) To ensure high availability by duplicating data
C) To index keys for faster search
D) To backup data to disk

**Q17.** Which command shows the role of a Redis server?
A) ROLE
B) INFO
C) STATUS
D) SERVER

**Q18.** What is Redis Sentinel used for?
A) Caching data
B) Monitoring Redis instances and managing failover
C) Sharding data
D) Indexing keys

**Q19.** What does the `PUBLISH` command do in Redis?
A) Subscribes to a channel
B) Sends a message to all subscribers of a channel
C) Deletes a channel
D) Creates a new channel

**Q20.** Which command is used to subscribe to a channel in Redis?
A) SUBSCRIBE
B) LISTEN
C) JOIN
D) WATCH

---

# Answer Key for Part 2

1. **B** – Redis is an in-memory data structure store.
2. **A** – `DBSIZE` returns the number of keys in the current database.
3. **C** – The expected reply is `PONG`.
4. **B** – `FLUSHDB` clears all keys from the current database.
5. **A** – `EXPIRE` sets a TTL on a key.
6. **C** – Redis uses port 6379 by default.
7. **B** – SET stores strings.
8. **B** – `RPUSH` adds an element to the end of a list.
9. **A** – `LRANGE` retrieves a range of list elements.
10. **B** – Duplicates in a set are ignored.
11. **A** – A sorted set maintains unique order based on scores.
12. **A** – `INCR` increments a numeric value.
13. **B** – `CONFIG GET` retrieves configuration parameters.
14. **B** – RDB saves data via periodic snapshots.
15. **B** – AOF logs every write operation.
16. **B** – Replication ensures high availability by duplicating data.
17. **A** – `ROLE` shows the server’s role.
18. **B** – Redis Sentinel monitors instances and manages failover.
19. **B** – `PUBLISH` sends messages to subscribers.
20. **A** – `SUBSCRIBE` is used to subscribe to a channel.

---

_Review these questions and answers thoroughly. Use this guide to test your understanding of Redis and prepare for your exam tomorrow._

Good luck!
