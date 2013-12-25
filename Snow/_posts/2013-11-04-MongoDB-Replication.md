---
layout: post
title: MongoDB-Replication
category: .net, CSharp, mongodb, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
	name: MongoDB-Ops
	current: 4
	part: Part 1 - Ops Stuff
	part: Part 2 - Indexing
	part: Part 3 - Explain Plan
	part: Part 4 - Replication
	part: Part 5 - Sharding
---

**REPLICATION**

Replication helps us achieve availability and fault-tolerance. A replica set is a set of mongo nodes that replicate data amongst each other asynchronously. One of the replica sets is primary while the rest of them will be secondary.
Writes only happen to the primary. If the primary goes down then an election happens and the new primary comes up.
Minimum number of nodes will be 3, since the election requires a majority of the original set.
If there were only 2 sets then the remaining one is not a majority and you would not be able to write.

<!--excerpt-->
Replica Set Elections

    Regular Node : It has the data and can become primary or secondary.
    Arbiter : It is just there for voting purposes. We need it if we want an even number of nodes. It has no data on it.
    Delayed/Regular : It can be set to a few hours after the nodes. It cannot become primary. It's priority is set to 0.
    Hidden Node : It cannot become a primary node. It's priority is set to 0.

By default the reads and writes go to the primary. You can go to secondary for reading. This means that you might read stale data. The lag between nodes is not guaranteed since the process is async. If you read from secondary then what we have is *"eventual consistency"*.


	rs.slaveOk() -- ok to read from the secondary
	rs.isMaster() -- checks whether the node is master
	rs.status() -- gives the current status of the replica set.


In the database locals , the collection oplogs.rs has all the operational logs. Replication happens when secondary nodes query the primary for the changes from a given timestamp. OpLog is the statement based replication log.
Replication happens in a statement driven manner?
e.g If a statement deletes 100 documents on the primary then there will 100 statements that are sent to the secondary to execute. There is no binary replication. This allows us to run different version of mongodb on different machines.

-     Try to keep the OpLog small on 64 bit machine since it defaults to a large value on 64 bit systems.
-     For replica sets don't use localhost or the ip address of the machine.
-     Use a logical name, that is the best practice.
-     Use DNS.</li>
-     Pick appropriate TTL as well.
