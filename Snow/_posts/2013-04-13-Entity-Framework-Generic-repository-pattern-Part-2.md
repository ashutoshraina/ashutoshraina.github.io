---
layout: post
title: Entity Framework - Generic repository Pattern - Part 2
category: entity-framework, pattern, .net, csharp 
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
    name: Entity Framework Generic Repository Pattern
    current: 1
    part: Part 1 - Generic Repositry Pattern
    part: Part 2 - Generic Repositry Pattern Contd.
---
In the previous post I wrote about an implementation of the Generic Repository pattern for EF. There is so much data on the web that it is difficult to understand what might actually work for you. All of this can drive you 
[Crazy](http://codereview.stackexchange.com/questions/11785/ef-code-first-with-repository-unitofwork-and-dbcontextfactory?rq=1 "Crazy")

Throw in my version of crazy as well.
[Crazy2](http://codereview.stackexchange.com/questions/19037/entity-framework-generic-repository-pattern "Crazy2")

<!--excerpt-->

Now that you have checked out the links and gone crazy reading how we all wanted a code review for this, it tells you the developer about the levels of confusion this pattern can create. One of my first thoughts was something like this 
[repository pattern](https://gist.github.com/ashutoshraina/4175430 "Repository Pattern")
Honestly, I don't even remember what my exact thoughts were when I was prototyping the code.

In  Part1 I did say that I will highlight some of the issues that I faced (remember YMMV). I didn't do TDD for this ( sue me  :) ).
<ul>
<li>
Testing is not as pain-free as it seems. What layers to test and how to test them ?
After all of this code is for separating components and making life easier in the long run by increasing our confidence in the code. 
If EF is already a UoW and Repository then what are doing building a layer on top of this ?
</li>
<li>
We end up having a service layer anyway since our application logic is often non trivial and needs to be kept separate. Then the real fun begins with tests.
</li>

<li>
Mocking EF is a pain since you don't have an <em>IDbContext </em> available. Get your fake data in there , then mock the repository and then mock the repository methods, then if you want to mock something like <em>Includes()</em> you will have some real fun. The idea is not mock EF, but even checking anything that sits on top of it can become an exercise in itself.
After writing the tests, I can confidently say that my confidence in my code had not increased the manner I had expected (you bet this confidence thing is funny business) . I often had the feeling of buyer's remorse.
</li>

<li>
It felt like I was on the wrong path. What do we really care about ? We need to get our data in and out of our database. That is it. How does making an abstraction help? It does not help that much. We are working exactly with the IQueryable that EF gives us back. Some implementations also have methods like FindById(..) , SortBy(..) etc...
We need to concentrate on queries in our application. Make queries first class in your application.
</li>

<li>
Was all of this useless ? No, not quite. Implementing the Repository Pattern this way is, the pattern is not useless.
We leak our data access technology Entity Framework into the Application Layer. A UnitOfWork driven implementation is actually very useful.  
I have not given that a go in code but here is something to start with <a href="http://www.planetgeek.ch/2012/05/05/what-is-that-all-about-the-repository-anti-pattern/" title="Repository Pattern" target="_blank">Repository Pattern</a>
</li>

</ul>
In conclusion, it was both fun and enriching to hit roadblocks and see why the developers are moving to and from the Repository Pattern. I was working with EF6 and that came with its fair share of headaches. I will blog about that soon. Stay hungry, stay awesome.

