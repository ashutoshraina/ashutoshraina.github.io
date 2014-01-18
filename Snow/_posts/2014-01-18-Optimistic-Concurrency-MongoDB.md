---
layout: post
title: MongoDb: Optimistic Concurrency
category: .net, CSharp, mongodb, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---

Optimistic concurrency is one something that most of us need in our applications. What follows is a simple example on how to achieve it in MongoDB.
First, a really simple document.

Note : This post uses a lot of code that I have shown in previous posts, please look at them if there is any confusion. The principles remain simple and can be used without using the code shown below.

<!--excerpt-->

    public class Person : MongoEntity {
            public string Name { get; set; }
            public long Version { get ; set ; }
        }

Next, when you want to edit a person it becomes a simple FindAndModify.

    public bool EditPerson (Person person) {
                //find the document with thr same id and version
                var query = Query.And(
                    Query<Person>.EQ(_ => _.Version, person.Version), 
                    Query<Person>.EQ(_ => _.Id, person.Id));
                    
                //bump the version
                
                var updatedPerson = person;
                updatedPerson.Version = person.Version + 1;
                var result = PersonConnectionHandler.MongoCollection.FindAndModify(query, null, 
                    Update.Replace<Person>(updatedPerson), true);
                    
                if ( result.ModifiedDocument != null ) {
                    Console.WriteLine("Document Modified successfully");
                    Console.WriteLine(result.ModifiedDocument);
                    return true;
                } else {
                    return false;
                }
            }