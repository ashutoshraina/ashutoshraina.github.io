---
layout: post
title: Tasks With Timeout
category: .net, CSharp, Asynchrony
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
	name: Tasks
	current: 1
	part: Part 1 - Tasks With Timeouts
	part: Part 2 - Tasks With Timeouts contd.
---

So the task is to timeout a task. now, I never thought it would take me as long as it did. Turns out it is a really tricky problem problem. I was expecting something within the framework to make life easier, there isn't anything by default but msdn to the rescue 
[Tasks With Timeouts](http://blogs.msdn.com/b/pfxteam/archive/2011/11/10/10235834.aspx "Tasks With Timeout")

<!--excerpt-->

So, the extension method is 

	public static class TaskWithTimeout
	{
	    internal struct VoidTypeStruct
	    { }
	    internal static void MarshalTaskResults<TResult>(Task source, TaskCompletionSource<TResult> proxy)
	    {
	        switch (source.Status)
	        {
	            case TaskStatus.Faulted:
	                proxy.TrySetException(source.Exception);
	                break;
	            case TaskStatus.Canceled:
	                proxy.TrySetCanceled();
	                break;
	            case TaskStatus.RanToCompletion:
	                Task<TResult> castedSource = source as Task<TResult>;
	                proxy.TrySetResult(
	                    castedSource == null ? default(TResult) : // source is a Task
	                        castedSource.Result); // source is a Task<TResult>
	                break;
	        }
	    }
	    
	    public static Task TimeoutAfter(this Task task, int millisecondsTimeout)
	    {
	        // Short-circuit #1: infinite timeout or task already completed
	        if (task.IsCompleted || (millisecondsTimeout == Timeout.Infinite))
	        {
	            // Either the task has already completed or timeout will never occur.
	            // No proxy necessary.
	            return task;
	        }

	        // tcs.Task will be returned as a proxy to the caller
	        TaskCompletionSource<VoidTypeStruct> tcs = new TaskCompletionSource<VoidTypeStruct>();

	        // Short-circuit #2: zero timeout
	        if (millisecondsTimeout == 0)
	        {
	            // We've already timed out.
	            tcs.SetException(new TimeoutException());
	            return tcs.Task;
	        }

	        // Set up a timer to complete after the specified timeout period
	        Timer timer = new Timer(state =>
	        {
	            // Recover your state information
	            var myTcs = (TaskCompletionSource<VoidTypeStruct>)state;
	            // Fault our proxy with a TimeoutException
	            myTcs.TrySetException(new TimeoutException());
	        }, tcs, millisecondsTimeout, Timeout.Infinite);

	        // Wire up the logic for what happens when source task completes
	        task.ContinueWith(antecedent =>
	                            {
	                                timer.Dispose(); // Cancel the timer
	                                MarshalTaskResults(antecedent, tcs); // Marshal results to proxy
	                            },
	                            CancellationToken.None, TaskContinuationOptions.ExecuteSynchronously, TaskScheduler.Default);

	        return tcs.Task;
	    }

	    public static Task<TResult> TimeoutAfter<TResult>(this Task<TResult> task, int millisecondsTimeout)
	    {
	        // Short-circuit #1: infinite timeout or task already completed
	        if (task.IsCompleted || (millisecondsTimeout == Timeout.Infinite))
	        {
	            // Either the task has already completed or timeout will never occur.
	            // No proxy necessary.
	            return task;
	        }

	        // tcs.Task will be returned as a proxy to the caller
	        TaskCompletionSource<TResult> tcs = new TaskCompletionSource<TResult>();

	        // Short-circuit #2: zero timeout
	        if (millisecondsTimeout == 0)
	        {
	            // We've already timed out.
	            tcs.SetException(new TimeoutException());
	            return tcs.Task;
	        }

	        // Set up a timer to complete after the specified timeout period
	        Timer timer = new Timer(state =>
	                                {
	                                    // Recover your state information
	                                    var myTcs = (TaskCompletionSource<TResult>)state;
	                                    // Fault our proxy with a TimeoutException
	                                    myTcs.TrySetException(new TimeoutException());
	                                }, tcs, millisecondsTimeout, Timeout.Infinite);

	        // Wire up the logic for what happens when source task completes
	        task.ContinueWith(antecedent =>
	                            {
	                                timer.Dispose(); // Cancel the timer
	                                MarshalTaskResults(antecedent, tcs); // Marshal results to proxy
	                            }, 
	                            CancellationToken.None, TaskContinuationOptions.ExecuteSynchronously,TaskScheduler.Default);

	        return tcs.Task;
	    }
	}

A lot of code for doing this, and the msdn article remains the better source of explanation.

Now using this.


	public class Program
		{
			private static List<int> Output = new List<int>();
			private static Random _random = new Random();
			
			public static int LongRunningTask(string message)
			{
				Console.WriteLine(message);
				Console.WriteLine("Managed thread Id " + Thread.CurrentThread.ManagedThreadId);
				//Simulate a long running task
	            Thread.Sleep(TimeSpan.FromSeconds(2));
				var number = _random.Next();
				Console.WriteLine("Adding " + number + " From thread  - " + Thread.CurrentThread.ManagedThreadId);
				return number;
			}

			public static void Main(string[] args)
			{
				Console.WriteLine("In Main");
				Console.WriteLine("Managed thread Id " + Thread.CurrentThread.ManagedThreadId);
				var cts = new CancellationTokenSource();
				var tasks = new List<Task>();
				try
				{
	//In the continuation check for the condition of fault (or something more if you so need) and perform the //continuation
					var t1 = Task.Factory.StartNew(_ =>	LongRunningTask("Entering task1"),
													    TaskCreationOptions.AttachedToParent)
	                                     .TimeoutAfter(1000)
	                                     .ContinueWith(antecedent => {
							if(!(antecedent.IsCanceled || antecedent.IsFaulted))
	                                                         Output.Add(antecedent.Result);
									}
									, cts.Token);
					var t2 = Task.Factory.StartNew(_ => LongRunningTask("Entering task2"),
														TaskCreationOptions.AttachedToParent)
	                                     .ContinueWith(_ => Output.Add(_.Result));
	                var t3 = Task.Factory.StartNew(_ => LongRunningTask("Entering task3"), 
														TaskCreationOptions.AttachedToParent)
	                                     .ContinueWith(_ => Output.Add(_.Result));
	            
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

	            Console.WriteLine("Output :");
	            Output.ForEach(_ => Console.WriteLine(_));

	            Console.ReadLine();
	        }
	    }


The important part being that the continuation is applied after the timeout and it won't work with the other way around.
The output therefore basically looks like this : 


	In Main
	Managed thread Id 9
	Entering task1
	Managed thread Id 10
	Entering task2
	Managed thread Id 11
	Entering task3
	Managed thread Id 14
	Adding 194443354 From thread  - 10
	Adding 792426557 From thread  - 11
	Adding 230130793 From thread  - 14
	Output :
	792426557
	230130793


Next, time I will try and write about some other things on Flirting with Tasks.