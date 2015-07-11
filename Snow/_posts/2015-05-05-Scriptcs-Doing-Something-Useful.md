---
layout: post
title: Scriptcs : Doing something Useful
category: scriptcs, open source
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
    name: Scriptcs
    current: 5
    part: Part 1 - Getting Started
    part: Part 2 - Up & Running
    part: Part 3 - Becoming more productive with Scriptcs
    part: Part 4 - Raising the bar with better abstractions
    part: Part 5 - Doing something Useful
    part: Part 6 - Tools to make you productive
---

We now have a fair grasp of how to swing with ScriptCS. Let's try and do something more useful with it.

Let's start by talking to a redis instance. Talking to a running instance interactively is a great a capability to have.

    scriptcs -install StackExchange.Redis
    
    using StackExchange.Redis;
    
    var redis = ConnectionMultiplexer.Connect("localhost");
    var db = redis.GetDatabase();
    db.StringSet("foo","bar");
    var result = db.StringGet("foo");
    Console.WriteLine(result);

<!--excerpt-->

Github is another important part of out daily workflow. I usually want to look at my notifications on Github when I come in the morning.
With the Ocktokit script-pack we can accomplish a lot of automation. We will make use of a few concepts we have seen in the previous posts.


First, a simple utility method to print messages in color to the console.

    //ConsoleUtil.csx
    void WriteToConsoleWithColor(string message, ConsoleColor color)
    {
	   var previousColor = Console.ForegroundColor;
	   Console.ForegroundColor = color;
	   Console.WriteLine(message);
	   Console.ForegroundColor = previousColor;
    }
 

To make the scripts interactive we need to take in parameters from the user. Here, we will use the concept of ScriptArgs. 
The arguments passed are available in Env.ScriptArgs and are indexed.
    
    //GithubAutomation.csx
    #load ConsoleUtil.csx
    var octokit = Require<OctokitPack>();

    string userName = Env.ScriptArgs[0];
    var client = octokit.CreateWithOAuth("GitHubClient", "ashutoshraina", "yourlovelytokengoeshere");

    void PrintNotifications(string userName)
    {
      var notifications = client.Notification.GetAllForCurrent().Result;
      if(!notifications.Any())
      {
      	WriteToConsoleWithColor("Hurray No notifications !!!", ConsoleColor.Green);
      }
      var groupedByRepository = notifications.GroupBy(n => n.Repository.Name);
      foreach (var groupedNotification in groupedByRepository) {
        WriteToConsoleWithColor(groupedNotification.Key, ConsoleColor.Blue);
        int i = 0;
        foreach (var notification in groupedNotification) {
            WriteToConsoleWithColor(i++ + "--> " + notification.Subject.Title, ConsoleColor.Green);		    
        }
      }
    }
 
 To run this script we will need to pass in the username parameter. This can be done as shown below.
   
    scriptcs .\CLIAutomation.csx -- ashutoshraina
    Hurray No notifications !!!

Hopefully, the idea that ScriptCS can form part of your daily developer workflow is starting to set in. We can easily extend this print our release notes and then hook that script up to the CI.

Interactively doing automation using selnium is difficult since we have to go figure out which selectors etc..to click.
With ScriptCS and FluentAutomation scriptpack we can do this from the REPL.

    
    using FluentAutomation;
    var test = new FluentTest();
    SeleniumWebDriver.Bootstrap(SeleniumWebDriver.Browser.Chrome);
    
    var I = test.I;
    
    I.Open("http://somewebsite.com.com");
    I.Click(".header__menu>.menu-item>.menu-item__link");
    I.Click(".section-links .grid__cell a");
    
    //Running this is going to open your browser and start the automation.
    
You can attach yourself to a process and diagnose what is going on. This is powered by ClrMd ScriptPack.

    var clrmd = Require<ClrMdPack>();
    var clrRuntime = clrmd.Attach(2772);
    clrmd.DumpHeapStatsByType();
    clrmd.Detach();
    
ScriptCS being extensible has good abstraction written on top of it. One such is the ConfigR project. 
It allows us to get rid of XML based config files and use pure C# instead to write configuration.

* Create a New Console Project and the ConfigR Nuget package. Let's call it *** ConfigRDemo ***.
* Create a File called ConfigRDemo.exe.csx    
    
Now, let't load the exe in the script file.
       
    #r "ConfigRDemo.exe"    
    using ConfigRDemo;
    
    //We can simply start adding the config values like this.
    Add("Name", "ChristosMatskas");  
    Add("Age", 25);
    
How, do we consume these values ?

    var name = Config.Global.Get<string>("Name");
    var age = Config.Global.Get<int>("Age");
    
    Notice, how the casting went away and we started to have real types.
    
    
We can now simply have custom configuration as well. In your code create a class

      public class MyCustomConfigurationStyle
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }
    
In the csx file then it is simply a matter of putting it in the config.    
    var config = new MyCustomConfigurationStyle{Id = 1, Name = "Awesome"};

    Add("config",config);
    var config = Config.Global.Get<MyCustomConfigurationStyle>("config");

Loading it is just the same.

    var config = Config.Global.Get<MyCustomConfigurationStyle>("config");

We can continue to leverage the full capabilities of the language like call methods, load another configuration from the network..make async await calls and do strange things after that.    


    var x = 5;
    var y = x * 5;
    
    void AwesomeMethod()
    {
    	x = 50 * x ;
    }
    AwesomeMethod();
    
    Add("x",x)
    
That really does bring a lot of C# into our config files.