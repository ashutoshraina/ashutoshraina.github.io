---
layout: post
title: Scriptcs : Better Abstraction
category: scriptcs, open source
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
    name: Scriptcs
    current: 4
    part: Part 1 - Getting Started
    part: Part 2 - Up & Running
    part: Part 3 - Becoming more productive with Scriptcs
    part: Part 4 - Raising the bar with better abstractions
    part: Part 5 - Doing something Useful
    part: Part 6 - Tools to make you productive
---

Abstractions are what drive most of software development. 
ScriptCS also has a few abstractions which bridge the gap between the existing frameworks and the notion of scripting and REPL.

From the github wiki

> Script Packs allow you to bootstrap the environment for new scripts, 
> further reducing the amount of code necessary to take advantage of your favorite C# frameworks.

<!--excerpt-->

Script Packs get rid of the boiler plate code which is required to get up and running with a framework. For e.g. Web Api requires a lot assembly references and using statements just to get going.
That may be fine in VS but in the REPL that becomes a serious overhead.

To see what kind of effect do script packs have we need to see them in action. We will look at the NancyScript Pack.

    scriptcs install ScriptCs.Nancy
    
ScriptCS provides the Require construct to load the script pack. We can have more than one as well. The basic code then just boild down to 

    var r = Require<NancyPack>();
    
    public class IndexModule : NancyModule
    {
    	public IndexModule()
    	{
    		Get["/"] = _ => "Hello World";
    		Get["/view"] = _ => View["index"];//put a index.html in the same directory and it will be served
    	}
    }
    
    r.Host();
    
    Running it is scriptcs ./Server.csx
    
    Searching assembly: Anonymously Hosted DynamicMethods Assembly, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null 
    Searching assembly: R*109eaf48-8359-4ded-b57a-d3c89575f24a-#1UD, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null
    
    Searching assembly: WindowsBase, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
    Found Nancy module: Submission#0+IndexModule
    Hosting Nancy at: http://localhost:8888/

What did we gain ? We got rid of the usings, the code required to spin up the process and host. We could purely focus and our code.

Most of the frameworks do not have the notion of in-memory compilation that Roslyn supports and hence these script packs are needed.

Similarly, for Web API as well we can do the following (from the Github Wiki).
    
    using System.Dynamic;
    
    public class TestController : ApiController
    {
        public dynamic Get() 
        {
            dynamic obj = new ExpandoObject();
            obj.message = "Hello from Web Api";
            return obj;
        }
    }
    
    var webapi = Require<WebApi>();
    
    var server = webapi.
        Configure(typeof(TestController)).
        UseJsonOnly().
        Start("http://localhost:8080");
    
    Console.WriteLine("Listening...");
    Console.ReadLine();
    server.Dispose();

Next, we will look at using Scripts to start doing something useful.