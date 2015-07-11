---
layout: post
title: Scriptcs : Up & Running
category: scriptcs, open source
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
    name: Scriptcs
    current: 2
    part: Part 1 - Getting Started
    part: Part 2 - Up & Running
    part: Part 3 - Becoming more productive with Scriptcs
    part: Part 4 - Raising the bar with better abstractions
    part: Part 5 - Doing something Useful
    part: Part 6 - Tools to make you productive
---
Last time we saw how we could get Scriptcs up and running. Before we go further, it would be nice to know what is going on.

Roslyn enabled in-memory compilation which means we can finally take loose C# code and start running with it. 
In code terms it means we can get rid of 
    class Program 
        public static void ....  

We can valid C# statements and then move on to doing the real thing. 

<!--excerpt-->

The code will get wrapped in a class and then get called.
Since inner classes can't have namespaces we also don't get to use namespaces in scriptcs (not yet, but this restriction is likely to go away.)

    > scriptcs
    scriptcs (ctrl-c to exit or :help for help)
    > :help
    The following commands are available in the REPL:
    :alias          Allows you to alias a command with a custom name
    :cd             Changes the working directory to the path provided.
    :clear          Clears the console window.
    :cwd            Displays the current working directory.
    :exit           Exits the REPL
    :help           Shows this help.
    :install        Installs a Nuget package. I.e. :install <package> <version>
    :references     Displays a list of assemblies referenced from the REPL context.
    :reset          Resets the REPL state. All local variables and member definitions are cleared.
    :usings         Displays a list of namespaces imported into REPL context.
    :vars           Displays a list of variables defined within the REPL, along with their types and values.
    >
Scriptcs will by default load certain assemblies by default, we can check that out using :references. 
:usings will give us the currently loaded namespaces.
    
    > :references
    [
      "System",
      "System.Core",
      "System.Data",
      "System.Data.DataSetExtensions",
      "System.Xml",
      "System.Xml.Linq",
      "System.Net.Http",
      "/usr/local/Cellar/scriptcs/0.14.1/libexec/ScriptCs.Core.dll",
      "/usr/local/Cellar/scriptcs/0.14.1/libexec/ScriptCs.Contracts.dll"
    ]
    >
    
    > :usings
    [
      "System",
      "System.Collections.Generic",
      "System.Linq",
      "System.Text",
      "System.Threading.Tasks",
      "System.IO",
      "System.Net.Http"
    ]
    >
    
We can now get on with the job of playing with the REPL. Let's declare a few variables.

    > :clear
    > var x = 42;
    > var y =x ;
    > Console.WriteLine(x); 
    42
    
We can now look at the :vars and see what got stored.
    
    > :vars
    [
      "int x = 42",
      "int y = 42"
    ]
    >

Next, we become a bit more productive by using the full power of the .net ecosystem.