---
layout: NONE
title: MongoDB Associate Developer Certification Study Guide
tags: [mongodb, python]
comments: true
---
# Introduction
After attending a MongoDB conference at work, I decided to persue one of their two primary certifications: MongoDB Associate Developer. Unlike the AWS certifications and other popular exams, there wasn't as much information about the Mongo certifications online. I decide to create this as a study guide for other prospective exam-takers. The certification is language specific, and while I chose python, many of the required units are language agnostic which means this study guide should be useful for anyone regardless of the language you choose. That being said, for the sections that are langauge dependent, my notes will be in python.

# Unit 1: Intro to MongoDB [TODO]
# Unit 2: Getting Started with MongoDB Atlas [TODO]
# Unit 3: MongoDB and the Document Model [DONE]
* A **document** is the basic unit of data in MongoDB
  * The values in a document can be of any data type (string, array, boolean, object, nulls, date, ObjectId, etc)
  * ```
    # Syntax
    {
      "key": value,
      "key": value,
      ...
    }

    # Example
    {
      "_id": 1,
      "name": "AC3 Phone",
      "colors" : ["black", "silver"],
      "price" : 200,
      "available" : true
    }
    ```
* A **collection** is a grouping of documents
* A **database** is a container for collections
* MongoDB has a flexible schema which means that documents in the same collection are not required to share a common structure of fields and value types (by default)
  * There is optional schema validation which can enforce constraints on the structure of documents in a collection
* The MongoDB Database is a core element of **MongoDB Atlas** which offers additional features such as text-search, analytics, and data visualization
* While documents are displayed in JSON, they are stored internally as BSON
  * BSON adds support for data types not available in JSON (Dates, ObjectID, etc.)
* ObjectID is a special data type to create to unique identifiers
  * Every document requires an `_id` field which serves as a primary key
  * If you don't specify an `_id` field when creating a document, MongoDB will automatically generate and insert one


# Unit 4: MongoDB Data Modeling Intro [DONE]
* Data Modeling is the process of defining the relationships that exist among different entities in your data and understanding how data is stored
* There are two ways to model relationships: **embedding** and **referencing**
  * Embedding is when we take related data and insert it into our document
    * Used for one-to-many or many-to-many
    * Avoids joins which results in faster read operations
    * This can create large documents over time (called unbounded documents)
  * Referencing is when we refer to documents in another collection in our document
    * Reference with respective ObjectID's
    * No duplication and smaller documents but needs to join multiple documents together
* Data that is accessed together should be stored together
* **One-to-One** a data entity in one set is connected to exactly one data entity in another set
* **One-to-Many** a data entity in one set is connected to any number of data entities in another set
  * Nested arrays could model this relationship
* **Many-to-Many** a relationship where any number of data entities in one set are connected to any number of data entities in another set

# Unit 5: Connecting to a MongoDB Database [TODO]
# Unit 6: Connecting to MongoDB in Python
* Drivers are responsible for establishing secure connections to Mongo clusters and for running database operations on behalf of client applications
* An application should use a single MongoClient instance for all database requests
* Atlas can generate a connection string but you must replace the username and password
* ```
  from pymongo import MongoClient
  MONGODB_URI = "...from Atlas..."
  client = MongoClient(MONGODB_URI)
  for db_name in client.list_database_names():
    print(db_name)
  ```
* Connection string takes the format `mongodb+srv://[username:password@]host[:port]`
* Must whitelist your IP address or you'll get a `ServerSelectionTimeoutError`
* Incorrect credential will yield a `OperationFailure: Authentication Failed`

# Unit 7: MongoDB CRUD Opeartions: Insert and Find Documents [DONE]

* Use `db.collection.insertOne({..document data..})` to insert a single document into a collection
  * If collection does not already exist, mongo will create it for you
* Use `db.collection.insertMany([{A},{B},..])` to insert multiple documents at once
* Use `db.collection.find(query, projection, options)` to get a cursor to the documents matching the query criteria
  * Use `db.collection.findOne(query, projection, options)` if you are only interested in one (i.e. the first) result
    * This also returns a document, not a cursor
  * All three of the parameters are optional, so `db.collection.find()` can be used instead of `db.collection.find({})`
  * How to match equality on the `_id` field: `db.zips.find({_id: ObjectId("sefsefse")})`
    * Make note of the ObjectId() call and that you can't use a string
  * **$in** operator lets you match the value of a field to one of multiple options in a given array
    * `db.zips.find({city: {$in: ['RALEIGH', 'CHARLOTTE']}})`
    * `{ field: { $in: [<value1>, <value2>, ..., <valueN> ] } }`
    * If field is an array, matches documents where the array contains at least one element that matches the right-hand side
  * **$nin** operator lets you match the value of a field not being any of the given options
    * `{ field: { $nin: [<value1>, <value2>, ..., <valueN> ] } }`
    * If the field is an array, matches documents whose array contains none of the specified items
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
  * To query on subdocuments, wrap the name in quotations: "a.b"
    * For example `{customer.age: {$gt: 50}}` finds all the documents where the customer age field is greater than 50

# Unit 8: MongoDB CRUD Operations: Replace and Delete Documents [DONE]
* `database.collection.replaceOne(filter, replacement, options)` lets you replace a document in MongoDB
  * *Filter*: A query that matches the document to replace
  * *Replacement*: The new document to replace the old one with
  * *Options*: An object specifying parameters for the update
  * Keeps the ID the same
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
* `database.collection.findOneAndDelete(filter, options)` deletes a single document matching the criteria and returns it

# Unit 9: MongoDB CRUD Operations: Modifying Query Results [DONE]
* `cursor.sort()` lets you return query results in a specific order
  * Full Syntax: `db.collection.find(<query>).sort(<sort>)`
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
* `cursor.count()` will also get you the number of documents in the result set

# Unit 10: MongoDB CRUD Operations in Python 
* PyMongo lets you represent BSON documents as Python dictionaries
* You can work with native python types and it will handle the conversion
* Every document requires an `_id` field, if we don't include it, it will automatically be added, this is stored as an instance of the ObjectId class
  * Must import the BSON library to work with this 
  * Must also use BSON for some other types including Int64, Decimal128, RegEx, ObjectId
  * We have to use methods for these types, but the majority work fine with native python types (strings, floats, lists, dictionaries)
* In general, once you have a `client` instance, you can access a collection with `client.<database name>.<collection name>`
  * Remember to call `client.close()` at the end of your file
* Create
  * `collection.insert_one(<document>)` to add one item to the collection
    *  You can store this return in a variable and access the objects ID with `result.inserted_id`
  * `collection.insert_many([<d1>,<d2>,...])`
    * You cans ave the return and access the list of inserted IDs with `result.inserted_ids`
* Read
  * `collection.find_one(<query>)` returns the first document that matches the query, or returns None if there are no matches
    * Example: `accounts.find({"_id": ObjectId("w3f32f2f")})`
  * `collection.find(<query>)` returns a Cursor instane that lets you iterate over all matching doucments
    * Can iterate over cursor with a for loop: `for document in cursor`
* Update
  * `collection.update_one(<filter>, <update>)`
    * Can capture the result and see if a change was made with `result.modified_count`
  * `collection.update_many(<filter>, <update>)`
    * Can capture result and access `result.matched_count`, `result.modified_count`
* Delete
  * `collection.delete_one(<filter>)`
    * If called with an empty document, deletes the first document in the collection
    * Can access result like `result.deleted_count`
  * `collection.delete_many(<filter>)` deletes all documents matching a query
    * If called with an empty filter, all documents in the collection get deleted
    * `result.deleted_count` to see how many documents were removed
* Transactions
  1. Define a callback that specifies the operations to perform as part of the transaction
      * `session` is a required parameters, you can add optional ones too
      * In the callback, get references to the collections needed to be opearated on (`session.client.db.collection`)
      * For each operation, you must pass the session as a named parameter (`session=session`)
  2.  Start a client session by calling the `start_session` method on a client (Use a context manager)
  3. Execute the transaction by calling `with_transaction(callback)` on the session object
  * Example 

    ```
    def callback(session):
      some_coll = session.client.db.some_coll
      some_coll.update_one({...}, {...}, session=session)

    with client.start_session() as session:
      session.with_transaction(callback)
    ```

# Unit 11: MongoDB Aggregation 
* An aggregation is a collection and summary of data which is created through a pipeline with is a series of stages (built in method that operates on the data without modifying it)
* The syntax is as follows:
  ```
  db.collection.aggregate([
    {
      $stage1: {
        {expression1},
        {expression2}
      },
      $stage2: {
        {expression1},
        {expression2}
      }
    }
  ])
  ```
* **$match** stage filters for documents that match specified conditions
  * Should be placed early in a pipeline
  ```
  {
    $match: {
      'field_name': 'value'
    }
  }
  ```
* **$group** stage groups documents by a group key
  * The group key must be wrapped in quotations and preceded by a `$`
  ```
  {
    $group: {
      _id: <expression>, // Key, wrap in quotes with a $ TODO
      <field>: { <accumulator> : <expression> }
    }
  }
  ```
* **$sort** sorts all the input documents and returns them to the pipeline in sorted order
  * Use 1 to represent ascending order
  * Use -1 to represent descending order
  ```
  {
    $sort: {
      'field_name': 1
    }
  }
  ```
* **$limit** returns a speciifed number of records
  ```
  {
    $limit: 5
  }
  ```
* **$project** specifies the fields of the output document
  * 1 means a field should be included, 0 should be excluded
  * Can also be used to assign a new value
    * To add a new field by using *$project*, specify the field name and set its value to an expression like this: <field>: <expression>.
  * Example:
    ```
    {
      $project: {
        state: 1,
        _id: 0,
        population: "$pop", // TODO figure out quote and $ rules
      }
    }
    ```
* **$set** creates new fields or changes the values of existing fields
  ```
  {
    $set: {
      place: {
        $concat: ['$city', ',', '$state']
      },
      pop: 10000
    }
  }
  ```
  * $set and $project can both be used to create new fields
* **$count** Creates one document with the number of documents in that stage of the aggregation pipeline, assigned to the given field name
  ```
  {
    $count: 'target_name' // Must not have a $
  }
  ```
* **$out** lets you write all the documents in the current aggregation to a collection
  * If a collection with the specified name already exists, it will be overwritten
  ```
  {
    $out: 'collection-name'
  }
  {
    $out: {
      'db': "<db>",
      'coll': "<coll>"
    }
  }
  ```

# Unit 12: MongoDB Aggregation in Python
  ```
  # Example: Select accounts with balances of less than $1000.
  stage1 = {
    "$match": {
      "balance": {
        "$lt": 1000
      }
    }
  }

  # Example: Separate documents by account type and calculate the average balance for each account type.
  stage2 = {
    "$group": {
      "_id": "$account_type",
      "average_balance": {
        "$avg": "$balance"
      }
    }
  }

  # Example: Organize documents in order from highest balance to lowest.
  stage3 = {
    "$sort": {
      "balance": -1
    }
  }

  # Example: Return only the account type & balance fields, plus a new field containing balance in Great British Pounds (GBP).
  stage4 = {
    "$project": {
      "account_type": 1,
      "balance": 1,
      "balance_gbp": {
        "$divide": ["$balance", 3.44]
      },
      "_id": 0
    }
  }


  pipepine = [stage1, stage2, ...]
  res = collection.aggregate(pipeline)
  for doc in res:
    print(doc)
  ```


# Unit 13: MongoDB Indexes 
* Single-Field Indexes
  * Index that supporst efficient querying against a single field
  * A single-field index is also a multikey index of the value of the field is an array
  * By default, all collections have a single-field index on `_id`
  * Use `createIndex()` with an object parameter specifying the field and the sort order
  * Syntax: `db.collection.createIndex({fieldName: 1})`
  * You can also enforce uniqueness with a second parameter to createIndex of the form `{unique: true}`
    * Will not create index if that field is not already unique in the collection
    * For all future insertions, uniqueness will be enforced
* Multi-Key Indexex
  * If a single-field or compound-field index has an array field, then the index is a multi-key index
  * There is a limit of 1 array field per index
  * There is no special syntax for creating these, its just a matter of one of the field(s) in your index being an array type
* Compound Indexes
  * To create, in the first parameter of `createIndex()` specify 2 or more fields along with their sort orders
  * The order of fields **does** matter! The [reccomended order](https://www.mongodb.com/docs/manual/tutorial/equality-sort-range-rule/) is as follows:
    * **Equality**: Fields that match on a single field value in a query
    * **Sort**: Fields that order the results of a query
    * **Range**: Fields that don't match an exact value but can take on a range
    * The two indexes are equivalent (1) `{a: 1, b: 1}` and (2) `{a: -1, b: -1}`
* Use `db.collection.getIndexes()` to see all indexes in a collection
* Use `explain()` to check if an index is being used in a query
  * Syntax: `db.collection.explain().find({...})`
  * **IXSCAN**: Indicates that the query is using an index
  * **COLLSCAN**: Indicating no index usage and therefore scanning the entire collection
  * **FETCH**: Indicates documents are being read from the collection
  * **SORT**: Indicates documents are being sorted in memory
* Use `db.collection.deleteIndex()` to remove an index
  * Either pass in the string representing the index name or an object representing the index
* Use `db.collection.dropIndexes()` to delete all indexes from a collection
  * Excludes the default index on the `_id` field
  * You can also pass an array of string index names if you want to delete a subset of indexes
  * You can use `hideIndex` to analyze behavior of removing an index without actually having to drop it

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


* To access subdocuments, you must use the syntax "field.nestedfield" (Including the quotation marks) (Scroll to section on dot notation: MongoDB uses the dot notation to access the elements of an array and to access the fields of an embedded document.)
* What is the difference between `findAndModify()` and `updateOne()`
* Unit 8 Summary: Added a new field to a document by using the upsert option in updateOne()
* ID field is immutable and cannot be overwritten
* Use `cursor.toArray()` to get all documents returned by the cursor
* Valid BSON Types: double, string, object, array, objectId, bool, date, null, int, long, decimal
* Boolean values should be lowercase