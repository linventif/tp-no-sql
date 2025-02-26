# QCM NoSQL Databases – Part 3: Cassandra

This quiz focuses on Apache Cassandra, covering its architecture, data model, CQL operations, cluster setup, and high availability features.
Select the correct answer for each question.

---

**Q1.** What type of database is Cassandra?
A) Document-oriented
B) Graph database
C) Column-family (column-oriented) NoSQL database
D) Relational database

**Q2.** Which organization develops Cassandra?
A) Microsoft
B) Apache Software Foundation
C) Oracle
D) Google

**Q3.** Which language is used to interact with Cassandra?
A) SQL
B) CQL (Cassandra Query Language)
C) MongoDB Shell
D) Redis CLI

**Q4.** What is a keyspace in Cassandra?
A) A table in Cassandra
B) A container for tables (similar to a database schema)
C) A replication mechanism
D) A client driver

**Q5.** In Cassandra, what is the purpose of the partition key?
A) It defines the sort order within a partition
B) It determines how data is distributed across the cluster
C) It specifies the replication factor
D) It is used for backup only

**Q6.** Which command creates a keyspace in Cassandra?
A) CREATE DATABASE
B) CREATE KEYSPACE
C) CREATE SCHEMA
D) CREATE NAMESPACE

**Q7.** Which port is typically used by Cassandra’s CQL interface?
A) 6379
B) 9042
C) 27017
D) 8080

**Q8.** How are tables created in Cassandra?
A) Using CREATE TABLE in CQL
B) Using SQL commands
C) Through a web interface only
D) They are auto-generated

**Q9.** What is a clustering key in Cassandra?
A) It is the primary key for a table
B) It defines the order of rows within a partition
C) It is used to partition data across nodes
D) It is only used for indexing

**Q10.** How do you insert data into a Cassandra table?
A) INSERT INTO
B) ADD ROW
C) PUSH DATA
D) UPDATE

**Q11.** Which command lists all tables in the current keyspace?
A) DESCRIBE TABLES
B) SHOW TABLES
C) SELECT \* FROM system.schema_tables
D) LIST TABLES

**Q12.** What does the `token()` function in Cassandra do?
A) Computes a hash token for a partition key
B) Encrypts data
C) Increments a counter
D) Returns the replication factor

**Q13.** What does the replication factor indicate in Cassandra?
A) The number of copies of data stored in the cluster
B) The number of clusters
C) The size of the dataset
D) The number of tables in a keyspace

**Q14.** Which consistency level ensures data is read from a majority of replicas?
A) ONE
B) QUORUM
C) ALL
D) LOCAL_ONE

**Q15.** What is the function of `nodetool` in Cassandra?
A) To execute CQL queries
B) To manage and monitor the Cassandra cluster
C) To export data
D) To back up the database

**Q16.** How can you view the distribution of tokens in a Cassandra cluster?
A) SELECT token_distribution()
B) nodetool ring
C) DESCRIBE TOKEN
D) SHOW DISTRIBUTION

**Q17.** What is a seed node in Cassandra?
A) A node that holds all the data
B) A node used for bootstrapping and gossiping in the cluster
C) A backup node
D) A node with a different replication factor

**Q18.** Which command is used to query data in Cassandra?
A) SELECT
B) QUERY
C) GET
D) FIND

**Q19.** What is the significance of clustering order in Cassandra?
A) It determines the replication strategy
B) It orders rows within a partition for efficient retrieval
C) It controls the number of tokens
D) It defines the keyspace name

**Q20.** How does Cassandra achieve high availability?
A) Through sharding data into multiple documents
B) By using master-slave replication only
C) Through replication and a peer-to-peer architecture
D) By storing data exclusively in memory

---

# Answer Key for Part 3

1. **C** – Cassandra is a column-family (column-oriented) NoSQL database.
2. **B** – It is developed by the Apache Software Foundation.
3. **B** – CQL (Cassandra Query Language) is used to interact with Cassandra.
4. **B** – A keyspace is a container for tables (similar to a schema).
5. **B** – The partition key determines how data is distributed across the cluster.
6. **B** – Use the `CREATE KEYSPACE` command.
7. **B** – Cassandra’s CQL interface typically runs on port 9042.
8. **A** – Tables are created using `CREATE TABLE` in CQL.
9. **B** – A clustering key defines the order of rows within a partition.
10. **A** – Data is inserted using the `INSERT INTO` statement.
11. **A** – `DESCRIBE TABLES` lists all tables in the current keyspace.
12. **A** – `token()` computes a hash token for a partition key.
13. **A** – The replication factor indicates how many copies of data exist.
14. **B** – QUORUM reads from a majority of replicas.
15. **B** – `nodetool` manages and monitors the Cassandra cluster.
16. **B** – Use `nodetool ring` to view token distribution.
17. **B** – A seed node is used for bootstrapping and gossiping.
18. **A** – Data is queried using the `SELECT` statement.
19. **B** – Clustering order orders rows within a partition for efficient retrieval.
20. **C** – Cassandra uses replication and a peer-to-peer architecture for high availability.

---

_Review these questions and answers carefully. They cover Cassandra’s architecture, CQL syntax, and cluster management. Study the answer keys and explanations to ensure you are well-prepared for your exam tomorrow._

Good luck with your QCM!
