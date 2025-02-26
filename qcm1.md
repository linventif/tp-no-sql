# QCM NoSQL Databases – Part 1: General Concepts & MongoDB

This quiz tests your knowledge on general NoSQL concepts and MongoDB features including CRUD operations, aggregation, MapReduce, and more.
Read each question carefully and choose the best answer.

---

**Q1.** Which of the following best describes a NoSQL database?
A) A relational database with fixed schema
B) A database designed for scalable, schema-less data storage
C) A file system for unstructured files
D) An in-memory cache only

**Q2.** Which type of NoSQL database is MongoDB?
A) Key-Value
B) Document-oriented
C) Column-oriented
D) Graph

**Q3.** Which command in MongoDB lists all databases?
A) show collections
B) show dbs
C) db.list()
D) list databases

**Q4.** To update a document in MongoDB, which operation is typically used?
A) insertOne
B) updateOne
C) findOneAndDelete
D) removeOne

**Q5.** In the MongoDB aggregation pipeline, which stage is used to filter documents?
A) $group
B) $match
C) $project
D) $sort

**Q6.** What does the `$group` stage in MongoDB do?
A) Filters documents based on criteria
B) Groups documents by a specified key and computes aggregates
C) Projects specific fields from documents
D) Sorts documents in ascending order

**Q7.** Which method inserts multiple documents at once in MongoDB?
A) insertMany
B) bulkInsert
C) multiInsert
D) insertBatch

**Q8.** What is the default port for a MongoDB server?
A) 27017
B) 27018
C) 6379
D) 9042

**Q9.** Which of the following is a valid MongoDB data type?
A) ObjectId
B) String
C) Number
D) All of the above

**Q10.** What is the main purpose of MapReduce in MongoDB?
A) To replicate data across clusters
B) To perform complex aggregation by mapping and reducing data
C) To index documents automatically
D) To backup the database

**Q11.** How do you exit the MongoDB shell (mongosh)?
A) exit
B) quit()
C) close
D) end

**Q12.** MongoDB is known for being:
A) Schema-less
B) Strictly structured
C) Only for key-value storage
D) Not scalable

**Q13.** What is mongosh?
A) A GUI tool for MongoDB
B) The MongoDB shell for executing JavaScript commands
C) A driver for connecting applications to MongoDB
D) A replication tool

**Q14.** Which operator in MongoDB is used to increment a field's value?
A) $add
B) $inc
C) $plus
D) $increase

**Q15.** The Aggregation Pipeline in MongoDB is best described as:
A) A sequence of stages that process documents sequentially
B) A tool to import JSON data
C) A backup mechanism
D) A replication protocol

**Q16.** What does the `$project` stage do in an aggregation pipeline?
A) Groups documents by key
B) Filters documents
C) Selects and reshapes the fields of documents
D) Sorts documents

**Q17.** Which method is used to remove a single document in MongoDB?
A) deleteOne
B) remove
C) dropOne
D) erase

**Q18.** In these TPs, what is Docker used for?
A) Creating virtual machines
B) Containerizing services for consistent environments
C) Running SQL queries
D) Editing source code

**Q19.** Which command is used to import JSON data into MongoDB?
A) mongoimport
B) mongodump
C) mongoexport
D) mongorestore

**Q20.** In a MapReduce job in MongoDB, which function is used by the map phase to emit key-value pairs?
A) print()
B) emit()
C) group()
D) map()

---

# Answer Key for Part 1

1. **B** – NoSQL databases are designed for scalable, schema-less storage.
2. **B** – MongoDB is a document-oriented database.
3. **B** – The command `show dbs` lists all databases.
4. **B** – `updateOne` is used to update a document.
5. **B** – The `$match` stage is used to filter documents.
6. **B** – `$group` groups documents by key and computes aggregates.
7. **A** – `insertMany` inserts multiple documents.
8. **A** – MongoDB’s default port is 27017.
9. **D** – MongoDB supports ObjectId, String, Number, and more.
10. **B** – MapReduce is used for complex aggregation tasks.
11. **A** – You exit mongosh with the command `exit`.
12. **A** – MongoDB is known for being schema-less.
13. **B** – mongosh is the MongoDB shell for executing JavaScript commands.
14. **B** – The `$inc` operator increments a field’s value.
15. **A** – The Aggregation Pipeline processes documents through sequential stages.
16. **C** – `$project` selects and reshapes document fields.
17. **A** – `deleteOne` removes a single document.
18. **B** – Docker is used for containerizing services.
19. **A** – `mongoimport` is used to import JSON data.
20. **B** – The map function uses `emit()` to emit key-value pairs.

---

_Take your time reviewing these questions and answers. Make sure you understand each concept as you prepare for your exam tomorrow._

Good luck!
