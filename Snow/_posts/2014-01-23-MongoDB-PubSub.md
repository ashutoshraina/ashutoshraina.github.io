---
layout: post
title: Pub Sub with MongoDB
category: .net, csharp, mongodb, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---
In the spirit of trying out new thigns I wanted to create a pub0sub system with mongodb. Usually we would create a pub sub system with a message queue, but since we are a little crazy we will try and do it this way. Turns out it is quite simple. Before we begin we must understand somethings that are available uniquely in MongoDB.
<!--excerpt-->
First, Capped Collection. Again from the MongodB webstie 

> Capped Collections are circular, fixed-size collections that keep documents well-ordered, even without the use of an index. This means that capped collections can receive very high-speed writes and sequential reads.

So, isn't that what we want ? Fast sequential reads and fast writes within a fixed size. Now, an example of capped collection is the oplog. We already know how to query the oplog [previous post](/2014/01/querying-oplog-mongodb  "previous post") using tailable cursors. Let's get down to it now.

Modifying my MongoConnectionHandler that we had before to pass in the CollectionOptions for setting up the capped collections.

    public MongoConnectionHandler (string connectionString, string databaseName, CollectionOptionsBuilder options) {
                var client = new MongoClient(connectionString);
                _server = client.GetServer();
                _database = _server.GetDatabase(databaseName);
                var collectionName = typeof(T).Name.ToLower() + "s";
                if ( !_database.CollectionExists(collectionName) && options != null ) {
                    _database.CreateCollection(collectionName, options);
                }
                _collection = _database.GetCollection<T>(typeof(T).Name.ToLower() + "s");
            }

Now, setting up the collection with the options.

    var options = CollectionOptions.SetCapped(true).SetMaxSize(5000).SetMaxDocuments(100);
    var connectionHandler = new MongoConnectionHandler<Message>("MongoDBDemo", options);

Our Message is really simple.

    public class Message : MongoEntity
    {
        public string Topic { get; set; }
        public String Data { get; set; }
        public int Version { get; set; }
    }

A small helper for generating the messages

    internal static IEnumerable<Message> Generator (string topic) {
                for (int i = 0; i < 100; i++) {
                    Thread.Sleep(100);
                    yield return new Message{ Topic = topic, Data = "Message " + i, Version = 0 };
                }
            }

        public static void Publish (string topic) {
            var options = CollectionOptions.SetCapped(true).SetMaxSize(5000).SetMaxDocuments(100);
            var connectionHandler = new MongoConnectionHandler<Message>("MongoDBDemo", options);
            var messages = Generator(topic);
            foreach (var message in messages) {
                Console.WriteLine("Publishing message");
                connectionHandler.MongoCollection.Insert(message);
            }
        }

Now, subscribing isn't that hard with tailable cursors.

        public static void Subscribe (string topic) {
            var connectionHandler = new MongoConnectionHandler<Message>("MongoDBDemo");
            BsonValue lastId = BsonMinKey.Value;
            while (true) {
                var query = Query.EQ("Topic", topic);
                var cursor = connectionHandler.MongoCollection.FindAs<BsonDocument>(query)
                    .SetFlags(QueryFlags.TailableCursor | QueryFlags.AwaitData)
                    .SetSortOrder(SortBy.Ascending("$natural"));
                var count = 0;
                foreach (var document in cursor) {
                    Console.WriteLine("Message Recieved from topic {0} with data {1}",document["Topic"],document["Data"]);
                    //Console.WriteLine(document);
                    count++;
                }
                if ( count == 0 ) {
                    Thread.Sleep(TimeSpan.FromMilliseconds(1000));
                }
            }  
        }

Setting all of this in motion.

    var topic = "mytopic";
    Task.Factory.StartNew(t => Publish(topic), TaskCreationOptions.None);
    Task.Factory.StartNew(t => Subscribe(topic), TaskCreationOptions.None); 

It wasn't so hard after all!!