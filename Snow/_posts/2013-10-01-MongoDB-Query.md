---
layout: post
title: MongoDB-Query
category: .net, CSharp, mongodb, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---

Hopefully, the MongoDB set up in the last post is still working.
Let's create a database and name it *Awesome*(just because we can).

	> use Awesome
	switched to db Awesome

<!--excerpt-->
Now, the database can have collection and those collections will have documents.
So, let's create a couple of collections and then use show collections to view them.

	db.createCollection("superAwesome")db.createCollection("superAwesome")
	{ "ok" : 1 }
	> show collections
	superAwesome
	system.indexes
	users


MongoDB is schemaless, which means the schema will be defined by the document structure you throw in there.
 So, if you insert nothing (i.e. no fields) it would still be a document with an ObjectID.

	db.superAwesome.insert({}) 

	db.superAwesome.find()
	{ "_id" : ObjectId("524dc34c1ad09caf6c994f8b") }

	db.superAwesome.insert({"name":"awesome","age":1})

	db.superAwesome.find()
	{ "_id" : ObjectId("524dc34c1ad09caf6c994f8b") }
	{ "_id" : ObjectId("524dc4141ad09caf6c994f8c"), "name" : "awesome", "age" : 1 }



I have mentioned ObjecId and find just now. I will elaborate a bit more in upcoming posts. What you have seen is the JS shell, so insertion, retrieval etc.. is all in Javascript. When we insert we send in JSON and we get back JSON when we are querying for documents.