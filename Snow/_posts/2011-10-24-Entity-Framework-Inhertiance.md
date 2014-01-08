---
layout: post
title: Entity Framework Inhertiance
category: entity-framework, .net, csharp 
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---

Inheritance is the basic requirement when we go about making a model for our business. Inheritance in SQL based systems is restricted to “has a “relationships. In real world applications which are modeled in object oriented mechanisms this is the real paint point for the developers. The essence of ORM systems is to bridge the gap between these two worlds and allow a seamless layer of abstraction to exist for the database access.
<!--excerpt-->

 Inheritance in general can be classified into 3 chief categories: - 
Table Per Type (TPT)
Table Per Hierarchy (TPH)
Table Per Concrete Type (TPC)

Consider that following structure of inheritance.

    public class QuestionDescriptor
        {
            public int QuestionDescriptorId { get; set; }
            public int Rating { get; set; }
            public int Difficulty { get; set; }
            public DateTime DateOfCreation { get; set; }
            public String QuestionText { get; set; }
            public String Answer { get; set; }        
        }
    public class QuestionBrief : QuestionDescriptor
        {
            public bool Short { get; set; }
        }
    public class QuestionImage : QuestionDescriptor
        {
           public String ImagePath { get; set; }
        }

Table Per Type represents the “IS A” relationship through Foreign Keys. This means that we need to map QuestionBrief and QuestionImage to separate tables in the database and have Foreign Key relations with the QuestionDescriptor table.
The mapping required is as follows: -

    modelBuilder.Entity<QuestionBrief>().ToTable("Brief");
    modelBuilder.Entity<QuestionImage>().ToTable("Image");

TPT strategy gives rise to a normalised database and the schema lends itself nicely to evolution. The primary drawback is that the queries formed are using Joins between table.

![Table Per Type](/stylesheets/images/posts/r1.jpg "Table Per Type")

Figure 1 Table Per Type

This strategy involves creating a single table and having a discriminator column. Since discriminator is not the key and yet determines the values of the columns that belong to the subclasses, this strategy violates the third normal form. It also requires the properties of the subclasses to be nullable. However, it is the best performing strategy for both polymorphic (queries instance of a class and all instances of the sub-class) and non-polymorphic queries (queries returning only instances of a particular subclass).
This can be achieved with the following mapping: -

    modelBuilder.Entity<QuestionDescriptor>()
                    .Map<QuestionBrief>(m => m.Requires("QuestionType").HasValue("Brief"))
                    .Map<QuestionImage>(m => m.Requires("QuestionType").HasValue("Image"));

The HasValue() method takes as a parameter an Object type so we can pass Boolean, integers etc. The type of parameter we pass to the HasValue() method will determine the datatype of the column in the database.  
        
    modelBuilder.Entity<QuestionDescriptor>()
                .Map<QuestionBrief>(m => m.Requires("QuestionType").HasValue(1))
                .Map<QuestionImage>(m => m.Requires("QuestionType").HasValue(2));

![R2](/stylesheets/images/posts/r21.jpg "R2")

Figure 2 Table Per Hierarchy

**Table Per Concrete Type***

Table per concrete type strategy pushes down the attributes of the base class to the sub-class. So, if we do not have many attributes in the base class and don’t care much for the polymorphism in our model we can also go for this strategy. 
•   TPC becomes tough when we have changes in the schema and the base class evolves to have more attributes and relationships. 
•   This approach also suffers with major ideological challenge in the sense that it discards polymorphism altogether and hence doesn’t support a dynamic environment with changing business requirements. 
•   This approach is recommended only when we are confident that the base class won’t change and we are not referring to the base class for multiple queries.
