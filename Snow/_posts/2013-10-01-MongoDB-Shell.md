---
layout: post
title: MongoDB-Shell
category: .net, csharp, mongodb, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---

MongoDB stores the documents in BSON format. It is a binary serialisation format and a super set of JSON.
It has more types than JSON which enables better integration with various languages that support these types. 
Languages like perl and JS have a smaller type system which can cause problems.
ObjectId which uniquely identifies a document in mongodb is also part of the bson specification.
_id field is the primary key which is of type ObjectId. 
It is immutable.
	_Object Id = 4-byte timestamp + machine id + process id + counter_

<!--excerpt-->

**Shell**
Note : This is not comprehensive list of what mongodb can do. These are just bits that I find interesting.

MongoDb does not have a querying language like SQL. It has it's own wire protocol with codes for doing things. The shell is basically a JS interpreter.
Here is a run down of some operators and tid-bits about them.


	$regex is the operator for passing in perl lie regular expression.
	$exists is for existential check.
	$or is a prefix operator unlike most of the other operators.
	//For querying arrays we can directly write property: value in the search criteria.
	$all operator can help us match a property which should contain all of the supplied values 
	Property : {$all : [value1 , value2]}
	$in is the enumeration of the values for the given field.


Multiple filters on the same property must be in the same sub document or
The javascript parser will ensure that your last filter will win, since the last literal will override everything else.

	db.users.find({$or: [
					{
					},
					{
					}
				]
			})


Upsert : This is kind of unique and took me by surprise the first time ( a pleasant one mind you ).
	db.collection.update( , , ,  )

**Quips and Quirks **
Empty document selector {} matches every document in the collections. This has the effect of selecting all the documents in the collection. 
From  the docs 

> "Optional. If set to true, creates a new document when no document matches the query criteria. The default value is false, which does not insert a new document when no match is found. The syntax for this parameter depends on the MongoDB version. See Upsert Parameter."

This is a rather useful operator once which I would dearly love SQL Server to have.

When an empty document selector is passed as an argument to the update method then mongodb will only update the first document that it finds and not all of the collection. To affect all the documents specify multi : true in your Find based operations.


	db.superAwesome.update(
	                     { name: "awesome" },
	                     { $inc : { age: -1 } },
	                     { multi: true }
	                   )

If you want to remove all the documents in a collection then use drop() instead of remove(). It is just a little faster since remove() goes through all of the documents one at a time. Further, the metadata will remain if you use remove() ( like indexes etc..) , with drop all of that will go away.
Remove() is not thread safe. Each document is however atomically removed.

If you want to find out about the last error in the database, then the runCommand is available. It can also be used to find out information about the last write performed in the database. It has a property n which gives us the number of records affected. 


GetLastError
	Db.runCommand( { getLastError : 1 }  )
