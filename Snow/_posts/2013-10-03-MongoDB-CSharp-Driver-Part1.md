---
layout: post
title: MongoDB-CSharp-Driver Getting started
category: .net, csharp, mongodb, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
	name: MongoDB-Csharp-Driver
	current: 1
	part: Part 1 - Using Linq
	part: Part 2 - Getting organised
	part: Part 3 - Getting Started
---

There are several drivers available for C#. I do not plan to go thorugh all of them here. Since, the official driver now has LINQ(although not complete yet) support, we will go with it.
Basic Setup..get the stuff of NuGet. It should put in two dll's in there 
1. MongoDB.Bson
2. MongoDB.Driver

We will get to what does what later. For now assume that we only want to get some data in and out of MongoDB.
Let's connect to MongoDB now(the code below is just quick and dirty, we will see a better version later).


	//MongoDB should be running by now, and assuming you have inserted some documents in there
	var client = new MongoClient(@"mongodb://localhost");
	var server = client.GetServer();
	var database = server.GetDatabase("YourDataBaseName");
	var mongoCollection = database.GetCollection("SomeCollectionName");
	//Getting all the documents
	var cursor = mongoCollection.AsQueryable();
	cursor.ForEach(Console.WriteLine);	

<!--excerpt-->

It is time for some explanataion.

What is MongoClient ?
MongoClient is the standard way of accessing the driver. I have dabbled with the python driver(pymongo) and it is the same there. I believe it was changed to keep the drivers for different languages in sync.
Reading up on this told me that SafeMode settings were dropped in favour of WriteConcern and instead of SlaveOK , ReadPreference should be used. The settings were present previously in MongoServerSettings, the new ones are on MongoClientSettings. IpV6 setting is also in MongoClientSettings. 

What is MongoServer ?
The server manages the life cycle of ServerProxies. Gives access to databases and some sort of connection management. More needs to be said about the server, I will stop short for now.

Notice that I did not use a genric GetCollection here (yet, will do so soon). The generic method is also available, which we will put to use soon.

All documents in MongoDB have an Id which has the type ObjectId (ObjectId resides in MongoDB.Bson).
So, we can have an interface which takes care of this and subsequently all our types can implement this.


	public interface IMongoEntity
	{
	    ObjectId Id { get; set; }
	}
	public class MongoEntity : IMongoEntity
	{
	   public ObjectId Id { get; set; }
	}


Refinement and obtaining a better MongoDBHandler(you can do much better than what I will show you here, but   that depends to large extent on your taste).


	public class MongoConnectionHandler<T> where T : IMongoEntity
	{
	    public MongoCollection<T> MongoCollection { get; private set; }
	    private const string ConnectionString = @"mongodb://localhost";

	    public MongoConnectionHandler(string databaseName)
	    {
	        var client = new MongoClient(ConnectionString);
	        var server = client.GetServer();
	        var database = server.GetDatabase(databaseName);
	        MongoCollection = database.GetCollection<T>(typeof (T).Name.ToLower() + "s");
	    }
	}


Having a the *databaseName* as a parameter is very subjective, if you only have a single database then you might want to just stick it directly in the method call, otherwise it just increases the burden of the caller and spreads the database name all over the code base. Another option that comes to my mind striaghtaway is having an Enum of DatabaseName or in some sort of configuration files. The same goes for ConnectionString, put it in some place configurable. The collection names are plural so you need to just stick an extra "s" in there(this took me quite a while to figure out).
So, we are setup nicely to go forward and do some more interesting work with MongoDB.
