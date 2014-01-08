---
layout: post
title: Entity Framework - Table Per Type Gotcha
category: entity-framework, gotcha, tips,.net, csharp 
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---
Entity Framework Table Per Type (TPT) implementation has a little gotcha ( little is really relative ). When we create table per type we will have orphan rows if the parent gets deleted and there will be orphan rows in the parent if the child gets deleted. This causes a funny picture to emerge with very bad consequences.

![Gotcha](/stylesheets/images/posts/fix-tpt.png)

Now, to get around this my simple workaround was to alter the relationships in my seed method.


    context.Database.ExecuteSqlCommand( @"ALTER TABLE [ParikshaDev].[Match] DROP CONSTRAINT [FK_ParikshaDev.Match_ParikshaDev.Questions_QuestionId]" );
    context.Database.ExecuteSqlCommand( @"ALTER TABLE [ParikshaDev].[Match] ADD CONSTRAINT [FK_ParikshaDev.Match_ParikshaDev.Questions_QuestionId] FOREIGN KEY (QuestionId) REFERENCES [ParikshaDev].[Questions] (QuestionId) ON DELETE CASCADE" );

    context.Database.ExecuteSqlCommand( @"ALTER TABLE [ParikshaDev].[Brief] DROP CONSTRAINT [FK_ParikshaDev.Brief_ParikshaDev.Questions_QuestionId]" );
    context.Database.ExecuteSqlCommand( @"ALTER TABLE [ParikshaDev].[Brief] ADD CONSTRAINT [FK_ParikshaDev.Brief_ParikshaDev.Questions_QuestionId] FOREIGN KEY (QuestionId) REFERENCES [ParikshaDev].[Questions] (QuestionId) ON DELETE CASCADE" );

    context.Database.ExecuteSqlCommand( @"ALTER TABLE [ParikshaDev].[Choice] DROP CONSTRAINT [FK_ParikshaDev.Choice_ParikshaDev.Questions_QuestionId]" );
    context.Database.ExecuteSqlCommand( @"ALTER TABLE [ParikshaDev].[Choice] ADD CONSTRAINT [FK_ParikshaDev.Choice_ParikshaDev.Questions_QuestionId] FOREIGN KEY (QuestionId) REFERENCES [ParikshaDev].[Questions] (QuestionId) ON DELETE CASCADE" );

    context.Database.ExecuteSqlCommand( @"ALTER TABLE [ParikshaDev].[Custom] DROP CONSTRAINT [FK_ParikshaDev.Custom_ParikshaDev.Questions_QuestionId]" );
    context.Database.ExecuteSqlCommand( @"ALTER TABLE [ParikshaDev].[Custom] ADD CONSTRAINT [FK_ParikshaDev.Custom_ParikshaDev.Questions_QuestionId] FOREIGN KEY (QuestionId) REFERENCES [ParikshaDev].[Questions] (QuestionId) ON DELETE CASCADE" );


![ForeignKey](/stylesheets/images/posts/foreign-key.png)

There are things that can be better in this code. We can have the Schema name and the Table name statically typed with the help of the context. The relationship name was arrived at by making an educated guess about the relationships and then confirming them by having a look at the db that was generated previously. I would consider this as a bug in EF. I didn't see any way to configure this behaviour with the fluent api. I expected this to be clearly stated in the documentation or a flag or a switch be made available in the api for configuration.