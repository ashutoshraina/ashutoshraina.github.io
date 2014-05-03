---
layout: post
title: MongoDB-Getting Started
category: .net, csharp, mongodb, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---
MongoDB is a document database and I won't bore you with details about what it can do. MongoDB makes many claims, they are [here](http://www.mongodb.org/ "Here"). In short, MongoDB is a document database that stores data in a JSON format and then promises to make out lives easier. 
Installation instruction are varied depending on your OS etc..and the both varieties of bitness are also available. [Download](http://www.mongodb.org/downloads,"Download")

The shell for MongoDB is a Javascript based one, so if you know a bit of JS then you will fly in there. Start mongod from the command line, this can be done by navigating to the bin directory of installation. Provide the dbpath(the place where it stores the data) as well.

![Mongod](/stylesheets/images/posts/mongod.png)

If you look at the output when we start the daemon (mongod = mongo-daemon) then the last line talks about a rest interface. This can be enable by passing the --rest flag when starting the instance. The port for viewing the webpage is also there.
 
Let's get into the mix of things by connecting to this mongod using the shell(mongo.exe). Navigate to the mongo directory and go mongo.exe.
By default this connects  to the test database. The prompt will say something like 


	C:\Users\Ashutosh\mongodb\bin>mongo.exe
	MongoDB shell version: 2.4.1
	connecting to: test
	> 

At any moment you can go back to mongod and check how many connections are open. Run a couple of commands like 
<em>show dbs
show collections</em> 
to see if everything is in order. 

Create a new database by use yourdatabasename. The shell will switch it's context to that database(after creating it if it does not exist already.) 
Next, time we will do some stuff by creating some collections and then querying. This will be a longish series, hold on and let me smear you twitter timelines.