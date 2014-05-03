---
layout: post
title: MongoDB-Understanding queries through explain plan
category: .net, csharp, mongodb, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
	name: MongoDB-Ops
	current: 3
	part: Part 1 - Ops Stuff
	part: Part 2 - Indexing
	part: Part 3 - Explain Plan
	part: Part 4 - Replication
	part: Part 5 - Sharding
---

Understanding the queries we write is very critical and MongoDB does a good job here. Developers will find it easy to understand what the queries are doing and where to look for bottlenecks. Well defined parameters and also well documented ones make life a lot easier.
The details have been taken from the mongodb website and presented here for continuity of series.

<!--excerpt-->

**Explain Output Fields**

*explain.cursor*
cursor is a string that reports the type of cursor used by the query operation:

BasicCursor indicates a full collection scan.
BtreeCursor indicates that the query used an index. The cursor includes name of the index. When a query uses an index, the output of explain() includes indexBounds details.
GeoSearchCursor indicates that the query used a geospatial index.
explain.isMultiKey
isMultiKey is a boolean. When true, the query uses a multikey index, where one of the fields in the index holds an array.

*explain.n*
n is a number that reflects the number of documents that match the query selection criteria.

*explain.nscannedObjects*
Specifies the total number of documents scanned during the query. The nscannedObjects may be lower than nscanned, such as if the index covers a query. See indexOnly. Additionally, the nscannedObjects may be lower than nscanned in the case of multikey index on an array field with duplicate documents.

*explain.nscanned*
Specifies the total number of documents or index entries scanned during the database operation. You want n and nscanned to be close in value as possible. The nscanned value may be higher than the nscannedObjects value, such as if the index covers a query. See indexOnly.

*explain.nscannedObjectsAllPlans*
nscannedObjectsAllPlans is a number that reflects the total number of documents scanned for all query plans during the database operation.

*explain.nscannedAllPlans*
nscannedAllPlans is a number that reflects the total number of documents or index entries scanned for all query plans during the database operation.

*explain.scanAndOrder*
scanAndOrder is a boolean that is true when the query cannot use the index for returning sorted results.

When true, MongoDB must sort the documents after it retrieves them from either an index cursor or a cursor that scans the entire collection.

*explain.indexOnly*
indexOnly is a boolean value that returns true when the query is covered by the index indicated in the cursor field. When an index covers a query, MongoDB can both match the query conditions and return the results using only the index because:

all the fields in the query are part of that index, and
all the fields returned in the results set are in the same index.
explain.nYields
nYields is a number that reflects the number of times this query yielded the read lock to allow waiting writes execute.

*explain.nChunkSkips*
nChunkSkips is a number that reflects the number of documents skipped because of active chunk migrations in a sharded system. Typically this will be zero. A number greater than zero is ok, but indicates a little bit of inefficiency.

*explain.millis*
millis is a number that reflects the time in milliseconds to complete the query.

*explain.indexBounds*
indexBounds is a document that contains the lower and upper index key bounds. This field resembles one of the following:

    "indexBounds" : {
    "start" : {  : , ...  },
    "end" : {  : , ... }
    },
    "indexBounds" : { "" : [ [ ,  ] ],
      ...
    }
*explain.allPlans*
allPlans is an array that holds the list of plans the query optimizer runs in order to select the index for the query. Displays only when the  parameter to explain() is true or 1.

*explain.oldPlan*
oldPlan is a document value that contains the previous plan selected by the query optimizer for the query. Displays only when the  parameter to explain() is true or 1.

*explain.server*
server is a string that reports the MongoDB server.

$or Query Output Fields
explain.clauses
clauses is an array that holds the Core Explain Output Fields information for each clause of the $or expression. clauses is only included when the clauses in the $or expression use indexes.

Sharded Collections Output Fields
*explain.clusteredType*
clusteredType is a string that reports the access pattern for shards. The value is:

ParallelSort, if the mongos queries shards in parallel.
SerialServer, if the mongos queries shards sequentially.
*explain.shards*
shards contains fields for each shard in the cluster accessed during the query. Each field holds the Core Explain Output Fields for that shard.

*explain.millisShardTotal*
millisShardTotal is a number that reports the total time in milliseconds for the query to run on the shards.

*explain.millisShardAvg*
millisShardAvg is a number that reports the average time in millisecond for the query to run on each shard.

*explain.numQueries*
numQueries is a number that reports the total number of queries executed.

*explain.numShards*
numShards is a number that reports the total number of shards queried.

Lastly, some more profiling tips that can be pretty useful.

**Logging Slow Queries**

- There are 3 levels for the profiler 0 OFF , 1Slow, 2 ALL
- Enable this using mongod -profile 1 --slowms 2
- `Db.getProfilingLevel()` to get the current profiling level.
- If you want to get all queries that took longer than 3 second, ordered by timestamp descending
- `db.system.profile.find({millis:{$gt:3000}}).sort({ts:-1})`

Mongotop can be run set to a specific time interval to determine where is the majority of the time spent. This utility tracks the time that mongod spends on reads and writes. The information is on a per collection basis.
I prefer to increase the time for reporting from the default 1 sec to somewhere close to 5 sec or more (this is just a random number that I feel comfortable with..otherwise the data is to verbose to make any sense).

This should be a good start. We will go into some more concepts about monitoring mongodb before going into sharding etc..