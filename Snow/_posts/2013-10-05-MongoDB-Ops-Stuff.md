---
layout: post
title: MongoDB-Ops-Stuff
category: .net, csharp, mongodb, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
	name: MongoDB-Ops
	current: 1
	part: Part 1 - Ops Stuff
	part: Part 2 - Indexing
	part: Part 3 - Explain Plan
	part: Part 4 - Replication
	part: Part 5 - Sharding
---

In the previous two posts we have seen some basic querying and how to leverage the querying mechanism to get up and running. Now, we are off in the wild world and we also need to some more complicated stuff.

**Creating Indexes**
The api surface is really smooth with this, allowing us to specify the sort order of the indexes and the manner of building them foreground or background.

	public void CreateIndex()
	{
	var QuestionConnectionHandler = new MongoConnectionHandler<Question>("MongoDBDemo");
	QuestionConnectionHandler.MongoCollection.EnsureIndex( 
	                      IndexKeys.Ascending("Difficulty"), IndexOptions.SetBackground(true));
	}

<!--excerpt-->

Dropping indexes is also easy with 

	QuestionConnectionHandler.MongoCollection.DropAllIndexes();


What happens when you want to see what is going on under the hood ? You let the database <strong>Explain</strong> it's<strong> Plan</strong>.


	QuestionConnectionHandler.MongoCollection.AsQueryable()
	                  .Where(q => q.Difficulty >= 3).Explain();
	//or if you went the other way 
	var query = Query<Question>.GTE(q => q.Difficulty, 3);
	var explainPlan = QuestionConnectionHandler.MongoCollection
	                          .FindAs<Question>(query).Explain();


Now, if only we could have some stats about our database and the indexes. All wrapped in a nice syntax.


	var stats = QuestionConnectionHandler.MongoCollection.GetStats();
	Console.WriteLine("Namespace : {0}", stats.Namespace);
	Console.WriteLine("DataSize : {0}", stats.DataSize);
	Console.WriteLine("Index Count : {0}", stats.IndexCount);
	stats.IndexSizes.Keys.ForEach(Console.WriteLine);
	var size = QuestionConnectionHandler.MongoCollection.GetTotalDataSize();
	Console.WriteLine("The total datasize for this collection is {0}", size);


A routine task is to get all the collections in a database and all the databases on the server itself. Easy peasy!!


	var collections = QuestionConnectionHandler.MongoCollection.Database.GetCollectionNames();
	Console.WriteLine("\nThe following collections are present in the database");
	collections.ForEach(Console.WriteLine);
	var client = new MongoClient(@"mongodb://localhost");
	var server = client.GetServer();
	var databases = server.GetDatabaseNames().ToList();
	Console.WriteLine("\nAll the databases in the server");
	databases.ForEach(Console.WriteLine);
