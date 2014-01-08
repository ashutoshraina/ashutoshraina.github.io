---
layout: post
title: Entity Framework - Migrations
category: entity-framework, gotcha, tips,.net, csharp 
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---

Migrations are needed in our day to day work and EF migrations story has become a lot better over the last year. When your database changes rapidly then migrations are needed to keep pace. Ofcourse this is nothing new, but it wasn't available in EF before.

**Enable Migrations***

To enable the migrations go to Tools -&gt; Library Package Manager -&gt; Package Manager Console.
Type Enable-Migrations in the console.
-Force parameter is optionally available if you want to overwrite an existing migrations.
<!--excerpt-->
**Creating a migration***

Once the migrations are enabled, use the Add-Migrations command to add a migration. Give a logical name to the migration, attach a issue number if needed to the migration name like myawesomemigration-issue#1234. It really helps when you want to go up and down the migrations. When you choose to enable migrations, do it in the correct project if you have multiple projects in the solution.
Once the migration has been added, then you will see two new files within a new folder Migrations pop in. One of these is the migrations file which will have your nice name and the other one is Configuration.cs which will have your configuration. Your awesome migrations file will have all your create table syntax, this seems to be a DSL. You can do your funny bits here, add an Index tweak mania can begin here. Do read the file carefully though for the first few times. It should not be too dense and it should all make sense. If something looks off highly likely that your configuration in the model is fishy. It has two methods which allow you to move up and down between the migrations.

Configuration.cs is a useful class. It has the seed method with which you can seed you database. You can seed your db with test data which you will need for running the system tests (which you should with EF).


**Updating your database***

All said and done, time to update our database to the latest version. In the package manager console, write 
Update-Database -Verbose. The verbose flag gives you back the sql as well for you to see. Options are available to script it out to a file as well if you want to hand it over to someone else.

As of this writing EF 6 beta has broken this bit 
[Bug](https://entityframework.codeplex.com/workitem/1128 "Bug")

You should be safe on previous bits ( till alpha or EF 5 and below ).
Have a look at your database when the command finishes, your database should be updated.

Irritant
Next, I will write about a gotcha that made me think long and hard if I was very dumb.