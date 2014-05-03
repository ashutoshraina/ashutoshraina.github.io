---
layout: post
title: MongoDB-CSharp-Driver Getting organised
category: .net, csharp, mongodb, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
	name: MongoDB-Csharp-Driver
	current: 2
	part: Part 1 - Using Linq
	part: Part 2 - Getting organised
	part: Part 3 - Getting Started
---

Having done the inital work for talking to MongoDB we can now create some POCO classes and then do some querying on top of it. As usual, my model is Questions and Users.


	public class Question : MongoEntity
	{
	    public string Text { get; set; }
	    public string Answer { get; set; }
	    public DateTime CreatedOn { get; set; }
	    public int Difficulty { get; set; }
	}
	public class User : MongoEntity
	{
	    public string Name { get; set; }
	    public int Reputation { get; set; }
	}
 

<!--excerpt-->

Finally, we get down to some real stuff. 


	public class SimpleQueries
	{
	    protected readonly MongoConnectionHandler<User> UserConnectionHandler;
	    protected readonly MongoConnectionHandler<Question> QuestionConnectionHandler;

	    public SimpleQueries()
	    {
	        UserConnectionHandler = new MongoConnectionHandler<User>("MongoDBDemo");
	        QuestionConnectionHandler = new MongoConnectionHandler<Question>("MongoDBDemo");
	    }

	    public void CreateQuestion(Question question)
	    {
	        //// Save the entity with safe mode (WriteConcern.Acknowledged)
	        var result = QuestionConnectionHandler.MongoCollection.Save<Question>(question, 
	                             new MongoInsertOptions { WriteConcern = WriteConcern.Acknowledged});

	        if (!result.Ok)
	        {
	            Console.WriteLine(result.LastErrorMessage);
	        }
	        else if (result.Response["err"] != null)
	        {
	            Console.WriteLine("Insertion was successfull");
	        }
	    }

	    public void CreateUser(User user)
	    {
	        //// Save the entity with safe mode (WriteConcern.Acknowledged)
	        var result = UserConnectionHandler.MongoCollection.Save<User>(user, 
	                          new MongoInsertOptions { WriteConcern = WriteConcern.Acknowledged });

	        if (!result.Ok)
	        {
	            Console.WriteLine(result.LastErrorMessage);
	        }
	        else if (result.Response["err"] != null)
	        {
	            Console.WriteLine("Insertion was successfull");
	        }
	    }

	    public void GetAllQuestions()
	    {
	        var cursor = QuestionConnectionHandler.MongoCollection.AsQueryable();
	        var resultSet = cursor.ToList();

	        Console.WriteLine("Writing out all the questions");
	        foreach (var result in resultSet)
	        {
	            Console.WriteLine("Text : {0},  Answer : {1}", result.Text, result.Answer);
	        }
	    }

	    public ObjectId GetOneQuestion()
	    {
	        var cursor = QuestionConnectionHandler.MongoCollection.AsQueryable().FirstOrDefault();

	        Console.WriteLine(cursor.Id);
	        return cursor.Id;
	    }

	    public void DeleteQuestion(ObjectId id)
	    {
	        var result = QuestionConnectionHandler.MongoCollection.Remove(
	            Query<Question>.EQ(e => e.Id, id), RemoveFlags.None, WriteConcern.Acknowledged);

	        if (!result.Ok)
	        {
	            Console.WriteLine(result.ErrorMessage);
	        }
	        else
	        {
	            Console.WriteLine("Delete Operation OK : {0}", result.Ok);
	        }
	    }
	}


Now, that we have some capabilities in our application, we can query away.

	//Seed Data
	var question = new Question { Text = "Who are you ?", Answer = "I am MongoDB.",
	                          CreatedOn = DateTime.Now, Difficulty = 3 };
	var user = new User {Name = "Ashutosh", Reputation = 100};
	var queries = new SimpleQueries();
	queries.CreateQuestion(question);
	queries.CreateUser(user);



	var queries = new SimpleQueries();
	queries.GetAllQuestions();
	var id = queries.GetOneQuestion();
	queries.DeleteQuestion(id);


If all is well then you will see some output and the world will be a better place.
