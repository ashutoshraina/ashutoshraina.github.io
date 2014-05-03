---
layout: post
title: Tasks With Timeout On .net 4.5 Contd.
category: .net, csharp, Asynchrony
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
    name: Tasks
    current: 3
    part: Part 1 - Tasks With Timeouts
    part: Part 2 - Tasks With Timeouts contd.
    part: Part 3 - Tasks With Timeout on .net4.5
---

The previous two posts looked at creating tasks with timeouts on .net4. The code as expected took some heavy lifting to get going. Understanding the extension methods themselves took some time. Today, using .net 4.5 this can be made a lot easier. Can we do better ? Yes we can !!

        public static Task WithTimeout(this Task task, TimeSpan timeout)
        {
            var delay = task.ContinueWith(t => { }, new CancellationTokenSource(timeout).Token);
            return Task.WhenAny(task, delay).Unwrap();
        }
        public static Task<T> WithTimeout<T>(this Task<T> task, TimeSpan timeout)
        {            
            var delay = task.ContinueWith(t => t.Result, new CancellationTokenSource(timeout).Token);
            return Task.WhenAny(task, delay).Unwrap();
        }

<!--excerpt-->
OK, so that really trimmed the code. Usage still remains the same with one difference. The task will now be really cancelled and not faulted which will force us to change the TaskContinuationOptions to NotOnCanceled.

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
            TimeoutonNet45();
            Console.ReadLine();
        }

        public static void TimeoutonNet45()
        {
            Console.WriteLine("Managed thread Id " + Thread.CurrentThread.ManagedThreadId);
            var tasks = new List<Task>();
            try
            {
                var t1 = Task.Factory.StartNew(_ => LongRunningTask("Entering task1"),TaskCreationOptions.AttachedToParent)
                                     .WithTimeout(TimeSpan.FromMilliseconds(1000))                
                                     .ContinueWith(t => Output.Add(t.Result), TaskContinuationOptions.NotOnCanceled);
                var t2 = Task.Factory.StartNew(_ => LongRunningTask("Entering task2"), TaskCreationOptions.AttachedToParent)
                                     .ContinueWith(_ => Output.Add(_.Result));
                tasks.Add(t1);
                tasks.Add(t2);
                Task.WaitAll(tasks.ToArray());
            }
            catch (Exception ex)
            {
                Console.WriteLine("There was an exception");
                Console.WriteLine(ex.InnerException.Message);
            }
            Console.WriteLine("Output :");
            Output.ForEach(_ => Console.WriteLine(_));
        }

        public static void TimeoutOnNet4()
        {
            Console.WriteLine("Managed thread Id " + Thread.CurrentThread.ManagedThreadId);
            var tasks = new List<Task>();
            try
            {
                var t1 = Task.Factory.StartNew(_ => LongRunningTask("Entering task1"), TaskCreationOptions.AttachedToParent)
                                     .TimeoutAfter(1000)
                                     .ContinueWith(t => Output.Add(t.Result), TaskContinuationOptions.NotOnFaulted);
                var t2 = Task.Factory.StartNew(_ => LongRunningTask("Entering task2"), TaskCreationOptions.AttachedToParent)
                                     .ContinueWith(_ => Output.Add(_.Result));
                var t3 = Task.Factory.StartNew(_ => LongRunningTask("Entering task3"), TaskCreationOptions.AttachedToParent)
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
        }
    }

The output will look something like this :

    In Main
    Managed thread Id 10
    Entering task1
    Managed thread Id 11
    Entering task2
    Managed thread Id 12
    Adding 856463453 From thread  - 12
    Adding 1826416296 From thread  - 11
    There was an exception
    A task was canceled.
    Output :
    856463453

A more robust means will always be to check for both faults and cancellation within the continuation. So, this makes creating tasks with timouts a lot easier.