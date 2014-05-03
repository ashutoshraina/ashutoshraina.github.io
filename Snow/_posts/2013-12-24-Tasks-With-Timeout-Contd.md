---
layout: post
title: Tasks With Timeout Contd.
category: .net, csharp, Asynchrony
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
	name: Tasks
	current: 2
	part: Part 1 - Tasks With Timeouts
    part: Part 2 - Tasks With Timeouts contd.
    part: Part 3 - Tasks With Timeout on .net4.5
---

As mentioned in the last post we can now have tasks with individual timeouts. The code looked a little heavy. Can we do better ? Yes we can !!


		var tasks = new List<Task>();
		try
		{
		//instead of checking for fault within the continutaion, 
		//we can just use a TaskContinuationOption to tell communicate the right semantics
		
		var t1 = Task.Factory.StartNew(_ => LongRunningTask(),TaskCreationOptions.AttachedToParent)
		.TimeoutAfter(1000)
		.ContinueWith(t => SomethingUsefulWithTheResult(), 
		 TaskContinuationOptions.NotOnFaulted);
		
		var t2 = Task.Factory.StartNew(_ => LongRunningTask(), TaskCreationOptions.AttachedToParent)
		  .ContinueWith(t => SomethingUsefulWithTheResult());
		
		var t3 = Task.Factory.StartNew(_ => LongRunningTask("Entering task3"),
		TaskCreationOptions.AttachedToParent)
		  .ContinueWith(t => SomethingUsefulWithTheResult());
		
		tasks.Add(t1);
		tasks.Add(t2);
		tasks.Add(t3);
		
		Task.WaitAll(tasks.ToArray());
		}
		catch (Exception ex)
		{
		Console.WriteLine("There was an exception");
		Console.WriteLine(ex.InnerException.Message);   
		}

I will show in the next post how this wasn't all that better since this leaves a few gaps.