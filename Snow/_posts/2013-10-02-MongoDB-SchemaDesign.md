---
layout: post
title: MongoDB-Schema Design
category: .net, csharp, mongodb, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---

This is a short post on Schema Design.

Schema Design in MongoDB

*   Application Driven Schema is the best way to store data in MongoDB
*   MongoDB stores rich documents i.e. store embedded documents , arrays etc..
*   Joins happen at the application level not at the database level.
*   There are no constraints in the database.
*   No support for transactions.
*   Support for Atomic present operations is present.
*   Tip : Embed as much as you can.

<!--excerpt-->
Relational Normalisation

*   Frees the database of modification anomalies
*   Achieve extensibility.
*   Removes bias towards a particular access pattern.
*   Constraints are supposed to be handled at the application level
*   Transactions are also not present in the database.

Mapping Strategies

Considerations 
	Query access pattern.
	Which collections will grow.
**1:1**

Two documents related to each other can be kept in separate collections :
	To reduce the working set size of your application.
	Because the combined size of the documents would be larger than 16MB

**1: M**

We should have true linking in this case.
i.e. have collection for each type.

If it is 1 to few then we can embed it in the 1 collection.

**M: M**

e.g. Student and Teacher

	Generally make both of them separate collections. 
	Link in one direction.
	At the risk of duplication we can link both ways but that should be done only when 
		we have performance considerations.

When to De-Normalise ?

	1: 1 Embed
	1 : M Embed from the many to one
	M : M Link