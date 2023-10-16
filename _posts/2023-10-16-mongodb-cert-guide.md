---
layout: post
title: MongoDB Associate Developer Certification Study Guid
tags: [mongodb, python]
comments: true
---
# Introduction
After attending a MongoDB conference at work, I decided to persue one of their two primary certifications: MongoDB Associate Developer. Unlike the AWS certifications and other popular exams, there wasn't as much information about the Mongo certifications online. I decide to create this as a study guide for other prospective exam-takers. The certification is language specific, and while I chose python, many of the required units are language agnostic which means this study guide should be useful for anyone regardless of the language you choose. That being said, for the sections that are langauge dependent, my notes will be in python.

# Unit 1: Intro to MongoDB [TODO]
# Unit 2: Getting Started with MongoDB Atlas [TODO]
# Unit 3: MongoDB and the Document Model [TODO]
# Unit 4: MongoDB Data Modeling Intro [TODO]
# Unit 5: Connecting to a MongoDB Database [TODO]
# Unit 6: Connecting to MongoDB in Python [TODO]
# Unit 7: MongoDB CRUD Opeartions: Insert and Find Documents
* Use `db.collection.insertOne({..document data..})` to insert a single document into a collection
* Use `db.collection.insertMany([{A},{B},..])` to insert multiple documents at once
* Use `db.collection.find(query, projection, options)` to get a cursor to the documents matching the query criteria
  * How to match equality on the `_id` field: `db.zips.find({_id: ObjectId("sefsefse")})`
    * Make note of the ObjectId() call and that you can't use a string
  * **$in** operator lets you match the value of a field to one of multiple options in a given array
    * `db.zips.find({city: {$in: ['RALEIGH', 'CHARLOTTE']}})`
    * `{ field: { $in: [<value1>, <value2>, ... <valueN> ] } }`
  * **$gt** lets you match documents with a field greater than the given value
    * `{ field: { $gt: <value> } }`
  * **$lt** lets you match documents with a field less than the given value
    * `{ field: { $lt: <value> } }`
  * **$lte** lets you match documents with a field less than or equal to a given value
    * `{ field: { $lte: <value> } }`
  * **$gte** lets you match documents with a field greater than or equal to a given value
    * `{ field: { $gte: <value> } }`
  * Querying on Array Elements
    * `database.collection.find({arrField: ['A', 'B']})` matches all documents where the field arrField is an array containing just two elements A and B, in that order (exact array match)
    * `database.collection.find({arrField: { $all: ['A', 'B'] } } )` matches all documents where arrField is an array that contains at least the items A and B (order agnostic)
    * `database.collection.find({arrField: 'ExactValue'})` matches all documents where arrField is an array with one item being ExactValue or where the field is a scalar value with ExactValue value
    * `database.collection.find({ arrField: { $elemMatch:{<query1>,<query2>,...} } })` use *$elemMatch* to find all documents that contain an array field with atleast one item that matches all the specified query criteria
  * **$or** lets you select documents that match at least one of the included expressions
    * `{$or: [<query1>, <query2>, ...]}`
  * **$and** lets you use multiple *$or* expressions in your query
    * ```
      db.collections.find({
        $and: [
            {$or: [{key: value}, {key:value}, ...]},
            {$or: [{key: value}, {key:value}, ...]}
        ]
      })
      ```
# Unit 8: MongoDB CRUD Operations: Replace and Delete Documents
* `database.collection.replaceOne(filter, replacement, options)` lets you replace a document in MongoDB
  * *Filter*: A query that matches the document to replace
  * *Replacement*: The new document to replace the old one with
  * *Options*: An object specifying parameters for the update
* `database.collection.updateOne(filter, update, options)` can be used to update a single document in the collection
  * **upsert** option lets you createa new document if none match the filtered criteria
  * **$set** lets you replace the value of a field with a specified value
    * Example: `db.podcasts.updateOne({...}, { $set: {fieldA: 100}})`
    * If the field does not exist, it will be added assuming no type violations
  * **$unset** lets you delete a field from a document
    * `{ $unset: { <field1>: "", ... } }` The value doesn't matter
  * **$push** lets you append a new value to an array field
    * Example: `db.podcasts.updateOne({...}, { $push: {hosts: "Milen"}})`
  * **$pop** lets you remove the first or last element of an array
    * `{ $pop: { <field>: <-1 | 1>, ... } }`
    * -1 removes the first element, 1 removes the last element
  * **$inc** lets you increment a field by a specific value
    * `database.collection.updateOne({...}, { $inc: { <field1>: <amount1>, <field2>: <amount2>, ... } }`
  * **$max** lets you update the value of a field to a maximum of the specified value and the current value
    * `{ $max: { <field1>: <value1>, ... } }`
* `database.collection.findAndModify({ query: {}, update: {}, new: true)` modifies and returns a single document, by default the returned document doesn't include the changes, use new option if you want the updated document
  * Upsert works here
* `database.collection.updateMany(filter, update, objects)` used to update multiple documents
* `database.collection.deleteOne(filter, options)` and `database.collection.deleteMany(filter, options)` can be used to delete documents
  * Both of these return a document that contains properties `acknowledged` (i.e. was it successful) and `deletedCount` (how many documents were removed)
# Unit 9: MongoDB CRUD Operations: Modifying Query Results
* `cursor.sort()`/`db.collection.find(<query>).sort(<sort>)`lets you return query results in a specific order
  * The parameter is an object specifying the fields to sort and the order (1 is ascending, -1 is descending)
  * To ensure consistent results, include a field in `<sort>` that contains unique values of the sort. An easy way to do this is to include the `_id` field
* `cursor.limit(<number>)` lets you specify a maximum number of documents to return
* Projections
  * It was previously mentioned that the find syntax is `db.collection.find(<query>, <projection>)`
  * To include a field, set its value to 1 in the projection document (`{<field> : 1}`)
  * To exclude fields, set their values to 0 in the projection document (`{<fieldA> : 0 , <fieldB>: 0}`)
  * By default, the `_id` field is included, but it can be supressed in any projection by settings its value to 0 (can't mix 1's and 0's in any other way)
* `database.collection.countDocuments(<query>, <options>)` to count the number of documents matching the query
  * An empty object parameter will get you the number of documents in the collection
# Unit 10: MongoDB CRUD Operations in Python [TODO]
# Unit 11: MongoDB Aggregation [TODO]
# Unit 12: MongoDB Aggregation in Python [TODO]
# Unit 13: MongoDB Indexes [TODO]
# Unit 15: MongoDB Transactions
* Issue: Two friends are at dinner and decide to split the bill. One person picks up the tab and the other pays them back on a payment app. For the second step, we withdraw money from one account and add money to the other. But we need both of these to happen.
* Any time DB operations transfer value from one record to another, we need these operations to complete fully
* **ACID Transactions**: Group of database operations that must happen together or not at all, ensuring database consistency
  * *Atomicity*: All operatios will either succeed or fail together
  * *Consistency*: All changes made by operations are consistent with database contraints (i.e. balance cant drop below 0)
  * *Isolation*: Multiple transactions can happen at the same time without affecting the outcome of other transactions
  * *Durability*: All of the changes made by operations in a transaction will persist, even in the event of hardware failure
* All Single-Document operations are inherently atomic (i.e. updateOne - either all the specified fields will change or none of them will)
* Multi-Document operations are not necesarily atomic
  * Require extra steps to have ACID properties
  * MongoDB locks all the resources involved in a transaction so they have a performance cost
  * Should only be used when ACID properties are absolutely necessary
* **Session**: Used to group DB operations that are related to each other and should run together (similar to a transaction)
  * A transaction has a maximum run time of 1 minute
  * ```
    const session = db.getMongo().startSession()
    session.startTransaction()
    const accounts = session.getDatabase('<db>').getCollection('<collection>') 

    // Example operations given
    account.updateOne({id: 'a'}, {$inc: {balance: -30}})
    account.updateOne({id: 'b'}, {$inc: {balance:  30}})

    session.commitTransaction()
    ```
  * ```
    // Undoing Transactions
    session.startTransaction()
    // ...increase a balance by $5...
    session.abortTransaction()
    ```
    * startTransaction() and abortTransaction() are silent, commitTransaction() will message upon success
# Unit 16: MongoDB Compass
* Compass is the official GUI for MongoDB
* Can import JSON into a collection
* Can be used to understand/analyze the schema of a collection
* You can add validation rules to be applied to new/existing documents
  * Have to specify with JSON Schema Syntax, can specify field name and type
  * Will let you specify if rule should apply to existing documents or just new insertions
* Documents tab lets you execute find queries

# Exam Breakdown
1. MongoDB Overview and the Document Model (8% of Exam Questions)
   * Understand Database, Collections, and Documents
   * MongoDB stores data records as BSON documents
   * BSON is a binary representation of JSON documents
   * Know the set of value types that BSON supports
   * Example: Given a set of documents, identify what documents can co-exist in a collection
2. CRUD (51% of Exam Questions)
   * `insertOne()`, `insertMany()`, `bulkWrite()`
   * `find(query, projection, options)`
   * Use of comparison, logical, elelement, array query, projection operators
   * Cursor methods: `sort()`, `limit()`, `count()`, `skip()`
   * Example: Identify the projection in a given query
   * Example: Given a query with operators (*$in*, *$and*, etc.), you have to identify the documents that get matched
   * Example: Given a set of fields we want to display in the result document, you have to select the best query
   * Example: Given a query statement, how do you get the number of returned documents
   * `updateOne(filter, update, options)`, `updateMany(filter, update, options)`, `replaceOne(filter, update, options)`
   * Learn `findOneAndReplace()`, `findOneAndUpdate()`, `findAndModify()`
   * Update Operators: *$set*, *$inc*, *$unset*, *$max*
   * Array Operators
   * Upsert
   * Example: given a set of documents and an update statement, how would the existing documents change
   * Example: Given a before and after of documents, pick the correct upsert statement
   * Example: Updating a document and information about where it should be inserted if it doesn't already exist, you need to identify the correct upsert command
   * Example: Identify the correct update statement to modify multiple documents
   * `deleteOne()`, `deleteMany()`, `findOneAndDelete()`, `remove()`
   * Building Query filters
   * Example: Identify a delete expression to remove a given document from a collection
   * Some aggregation questions can appear here
   * Aggregation pipelines consist of one or more stages that process documents
   * `aggregate()` method and *$match*, *$group*, *$sort*, *$project*
   * Example: Identify the output of an aggregation expression
3. Indexes (17% of Exam Questions)
   * Why do we need indexes
   * Types of indexes in Mongo
   * Methods to manage indexes: `createIndex()`, `getIndexes()`, `dropIndex()`
   * Limitation of indexes and scenarios where we might not want them
   * Understand the Explain output
   * Example: Given a query on a collection, identify the most performant index
4. Data Modeling (4% of Exam Questions)
   * Understand Flexible Schema
   * Embedded Data Model vs Normalized Data Model
5. Tools and Tooling (2% of Exam Questions)
6. Drivers (18% of Exam Questions)
   * Purpose of driver and its advantages
   * Understand components of URI string
   * Know CRUD Syntax for Driver
   * Sample scenarios will be similar to what we've already seen, but language dependent


To access subdocuments, you must use the syntax "field.nestedfield" (Including the quotation marks) (Scroll to section on dot notation: MongoDB uses the dot notation to access the elements of an array and to access the fields of an embedded document.)
What is the difference between `findAndModify()` and `updateOne()`
Unit 8 Summary: Added a new field to a document by using the upsert option in updateOne()