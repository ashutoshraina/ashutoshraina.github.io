---
layout: post
title: MongoDB-Indexing
category: .net, CSharp, mongodb, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
	name: MongoDB-Ops
	current: 2
	part: Part 1 - Ops Stuff
	part: Part 2 - Indexing
	part: Part 3 - Explain Plan
	part: Part 4 - Replication
	part: Part 5 - Sharding
---

Indexing is one of the most important concepts for any database. Without indexes the mongod process would scan the entire collection and the all the documents it contains to obtain the result of the query. Indexes are defined for the collections and properties as well as sub-fields are supported. Briefly, MongoDB supports the following types of indexes : 

*   **Single Field Indexes** : Think about having an index on a column in RDBMS.
*   **Compound Indexes**: Think about an having an index on multiple columns in RDBMS.
*   **Multikey Indexes**: This is unique to MongodB, it references an array and succeeds if there is a match for any value in the array.
*   **Geospatial Indexes and Queries** : Allows you to index GeoData. I really don't know much about this to comment. MongoDB website is the best source.
*   **Text Indexes** : For full text search inside a document. Should we use lucene ? Not sure ?
*   **Hashed Index**: Index on hashed contents of the fields.

<!--excerpt-->

Indexes have properties associated with them :

*   TTL: This is a rather surprising feature. I never expected such a feature to be available on indexes, but after giving it some thought, it makes sense to expire the indexes.
*   Unique: Only documents with unique values on the field are permitted.
*   Sparse: Really useful if you are going to have sparse fields. It leaves out the documents that do not have the field.

**Some more tid-bits**
- The order of indexes matters.
e.g. if an index is created on (a,b,c) then index will be used only if the query is on
- A or a,b or a,b,c . 
- Index will not be called if we query on b,c or c.
- It needs to be a left subset of the index.

The command is below.

    Db.collection.ensureIndex('property');
    e.g. `db.students.ensureIndex({'class':1,'student_name':1});
- Note: 1 or -1 is for ascending v/s descending which becomes useful when we have a lot of sort queries.
- By default all the indexes are built in the foreground i.e. all the writers will be blocked while the index is being created. It is fast but the database is blocked.
- Background ones are slow, fit for production use.
- Creating the database index in the background also blocks the current shell while it is being created.
- All indexes are Btree indexes.

**Index Creation option , Unique , Removing Duplicates**

- Unique indexes ensure that the key is unique in the collection.
- e.g. db.students.ensureIndex({'student_id':1,'class_id':1},{'unique':true})
- To remove the duplicates while creating the index we can do the following :
- Provide the dropDups :true along with the unique attribute. This is dangerous, so handle with care. There is no way to control which documents it will remove. It will let live a single document and we can't predict or configure as to which one it will be.

_MultiKey Index_
- It will create an index for all the items in the array if the key.
- You can't have two multi keys in a single index. This cause a polynomial explosion of the indexes.
- We will only when we first insert something in the collection.

_Index Efficiency_
- $gt, $lt will use the index but the efficiency may not be there since the selectivity could be very low.
Similar for $ne etc.. 

_Index Size_
- Indexes must be kept in memory.
- Db.collection.stats() to get the stats on the collection.
Db.collection.getIndexSize() to get the total size of the index of the database.

**Final thoughts**
- Create any and all indexes that are required for your queries.
- Ensure that the indexes fit in memory, reading from the disk is bad.
- Sorting should also use Indexes.
- High selectivity should be the prime consideration when deciding about indexes.