---
layout: post
title: MongoDb: Querying the OpLog
category: .net, CSharp, mongodb, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---
Back to MongoDB after some time. OpLog has always been a curious case for me. To know more about it let's start to our detective work.

**What is the OpLog?**

The mongodb website gives a rather long definition. I will quote that 
> The oplog (operations log) is a special capped collection that keeps a rolling record of all operations that modify the data stored in your databases. MongoDB applies database operations on the primary and then records the operations on the primary’s oplog. The secondary members then copy and apply these operations in an asynchronous process. All replica set members contain a copy of the oplog, allowing them to maintain the current state of the database.

>To facilitate replication, all replica set members send heartbeats (pings) to all other members. Any member can import oplog entries from any other member.
Whether applied once or multiple times to the target dataset, each operation in the oplog produces the same results, i.e. each operation in the oplog is idempotent. For proper replication operations, entries in the oplog must be idempotent:

- initial sync
- post-rollback catch-up
- sharding chunk migrations

<!--excerpt-->
So, the oplog is applicable for a replica set but then do I really need a non-trivial process to start playing with it ? And to your heart's delight the answer is no.
Oplog is not turned on by default when you have a single node in your cluster (of course, who would want that to be way (read me)). OK, let's create a one node replica set then.

	    mongod.exe --replSet myreplSet --oplogSize 50
	    
We now have a one node repl set with the oplog of 50mb. One liners are always cool !!

Let's go on our querying streak. Start the monog.exe and let's go to the local database and find our oplog.

	rs0:PRIMARY> use local
	switched to db local
	rs0:PRIMARY> show collections
	oplog.rs
	startup_log
	system.replset
	rs0:PRIMARY> db.oplog.rs.findOne();
	{
	        "ts" : {
	                "t" : 1387363823,
	                "i" : 1
	        },
	        "h" : NumberLong(0),
	        "v" : 2,
	        "op" : "n",
	        "ns" : "",
	        "o" : {
	                "msg" : "initiating set"
	        }
	}
	rs0:PRIMARY>


We just don't want to query it simply we really do want to get it into our application in real-time. Let's move to the application code now.

Now, If you look at my previous posts you will recollect that we had a generic version of MongoConnectionHandler. For querying the oplog, I created a non-generic one for this since it makes our lives just a touch easier by not asking us to derive from an IMongoEntity.

    public class MongoConnectionHandler
        {
            public MongoCollection MongoCollection { get; private set; }
            private const string ConnectionString = @"mongodb://localhost";
            public MongoConnectionHandler(string databaseName, string collection)
            {
                var client = new MongoClient(ConnectionString);
                var server = client.GetServer();
                var database = server.GetDatabase(databaseName);
                MongoCollection = database.GetCollection(collection);
            }
        }
Now, we can query the oplog quite simply by switching to the local daatbase and reading the ***BsonDocument*** that is returned.

Note: The orignial version was written in the examples in java and is available on Github as part of examples of the java driver.

    public class QueryOpLog {
            protected readonly MongoConnectionHandler OpLogHandler;
            public QueryOpLog () {
                OpLogHandler = new MongoConnectionHandler("local", "oplog.rs");
            }
            public IEnumerable<BsonDocument> GetLastEntryInOpLog () {           
                BsonValue lastId = BsonMinKey.Value;
                while (true) {
                    var query = Query.GT("ts", lastId);
                    var cursor = OpLogHandler.MongoCollection.FindAs<BsonDocument>(query)
                                .SetFlags(QueryFlags.TailableCursor | QueryFlags.AwaitData)
                                .SetSortOrder(SortBy.Ascending("$natural"));
                    var count = 0;
                    foreach (var document in cursor) {
                        lastId = document["ts"];
                        Console.WriteLine("LastId is {0}", lastId);
                        yield return document;
                        count++;
                    }
                    if ( count == 0 ) {
                        Thread.Sleep(TimeSpan.FromMilliseconds(100));
                    }
                }           
            }
        }

There are a few things going on here. One, some flags are being set in the query to get a tailable cursor and we are basically querying the oplog only if we have a new document by using a count. ***yield*** makes for a nicer syntax for the consumer of this method.

    var oplog = new QueryOpLog();
    var resut = oplog.GetLastEntryInOpLog();
    resut.ForEach(r => Console.WriteLine(r));

Now, go on and make some operations through the shell and watch the oplog show up in a console window.