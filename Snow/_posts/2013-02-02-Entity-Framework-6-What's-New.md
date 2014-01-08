---
layout: post
title: Entity Framework 6 What's new ?
category: entity-framework, .net, csharp 
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---

EF6 is still in alpha stages but you can go grab it now via nuget.
What is new ?
A whole lot find about it here [EF6](http://entityframework.codeplex.com/wikipage?title=Updating%20Applications%20to%20use%20EF6 "EF6")

Also, do read this post about migrating to EF6 if you are migrating an existing application.

[EF6 Migration](http://entityframework.codeplex.com/wikipage?title=Updating%20Applications%20to%20use%20EF6 "EF6 Migration")

I am basically using the same model to see if life is any easier with V6. The model is fairly simple. There are users, users can questions and questions are of different types. A user creates Test which is composed of many questions.

Earlier, there was no easy way of doing Enum in EF Code First. That limitation is no longer there.
Now, the questions can be of varying difficulty levels so now we can do the following.
<!--excerpt-->

    public enum Difficulty
    {
        Trivial = 1,
        Easy = 2,
        Medium = 3,
        Hard = 4,
        Difficult = 5
    }

    /// <summary>
    /// Question is the base type which describes a Question. Other types derive from Question.
    /// </summary>
    public class Question
    {
        /// <summary>
        /// Gets or sets the Difficulty
        /// </summary>
        public Difficulty Difficulty { get; set; }

....more properties 
    }
Similarly we can have UserRole as well.

EF now has the option to specify the default schema is also available now. It can be done as shown below.

    modelBuilder.HasDefaultSchema("ParikshaDev");

EF now has the ability to provide custom conventions as well. The following conventions are available to us.
<ul>
     <li>Lightweight Conventions</li>
     <li>Configuration Conventions</li>
     <li>Custom Attributes</li>
     <li>Model-based Conventions</li>
</ul>

Read more about it here [EF6 Conventions](http://msdn.microsoft.com/en-us/data/jj680699 "EF Conventions").
I will cross-post an important point about the order of execution. If you have many conventions then the order will become important. I haven't written a lot of conventions but my guess is that this could get tricky if not done with care.

<ol>
    <li>IConfigurationConvention-based and lightweight conventions</li>
    <li>IEdmConvention-based conventions</li>
    <li>IDbConvention-based conventions</li>
    <li>IDbMappingConvention-based conventions</li>
</ol>

A conventions that I have used is Lightweight conventions. We sometimes want to append custom names to our table names as per the convention of our customers.

    modelBuilder.Entities().Configure(_ => _.ToTable("CustomTable"+ _.ClrType.Name));

Similarly, other conventions are also available. The most common ones that I see being used will be Lightweight and Configuration Conventions. Next, we will have a look at enabling migrations and an irritant that made me go nuts for a few months.