---
layout: post
title: MongoDB-CSharp-Driver-Using Linq
category: .net, CSharp, mongodb, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
	name: MongoDB-Csharp-Driver
	current: 3
	part: Part 1 - Using Linq
	part: Part 2 - Getting organised
	part: Part 3 - Getting Started
---



In the previous post we have seen some simple queries. It is time we move onto something more concrete and realistic. There are basically two ways of querying MongoDB with the driver. First, as I showed last time is using LINQ. To use LINQ we need to first move into the Queryable world and then proceed with actual querying.
Be careful about pulling all the documents locally and then performing operations on them. What we really want to do is offload all our querying to MongoDB and then only use the results. The driver implements the IQueryable interface and hence we should use it.


	var result = UserConnectionHandler.MongoCollection.AsQueryable()
	                                  .Where(u => u.Reputation > reputation);

<!--excerpt-->

The alternative to this form of querying is using a BsonDocument and a MongoQuery. The way to build up such a query is below. Note, that the first lambda is the property and the second parameter is the key for the filtering. The query builder is in the MongoDB.Drivers.Builders namespace.


	var query = Query<User>.GT(u => u.Reputation, reputation);
	var result = UserConnectionHandler.MongoCollection.FindAs<User>(query);


It is a bit more work to specify queries by hand so I would prefer LINQ, but both options are available.
Another, interesting querying mechanism is Regex. It was kind of hard to locate in the API ( or may be I just didn't know where to look). It is present in Bson namesapce.


	public void UserNameStartsWith(string searchKey)
	{
	var query = Query.Matches("Name", new BsonRegularExpression(string.Format("^{0}", searchKey)));
	var result = UserConnectionHandler.MongoCollection.Find(query);
	Console.WriteLine("We found {0} Users whose name starts with {1}", result.Count(), searchKey);
	}


 Select does not result in fewer fields being returned from the server. The entire document is pulled back and passed to the native Select method. Therefore, the projection is performed client side. We should use the IQueryable implementation from the MongoDB.Driver.Linq namespace. Alternatively, there is SetFields() that is available to selectively bring fields back from the database. 


	var query = Query.Matches("Name", new BsonRegularExpression(string.Format("^{0}", searchKey)));
	var result = UserConnectionHandler.MongoCollection.Find(query)
	     			.SetFields(Fields<User>.Include(u => u.Name, u => u.Reputation));
