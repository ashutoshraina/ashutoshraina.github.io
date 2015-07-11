---
layout: post
title: Scriptcs : Becoming Productive
category: scriptcs, open source
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
    name: Scriptcs
    current: 3
    part: Part 1 - Getting Started
    part: Part 2 - Up & Running
    part: Part 3 - Becoming more productive with Scriptcs
    part: Part 4 - Raising the bar with better abstractions
    part: Part 5 - Doing something Useful
    part: Part 6 - Tools to make you productive
---
Last time we got up and running with Scriptcs. We have done everything in the REPL so far.
When it comes to scripts we need a few more things to make everything easy.

Roslyn allows us to load assemblies using the #r directive.

    #r System.Data
    #r System.Net.Http

Roslyn does not give us the power to load multiple script files. Scriptcs helps us out here by giving the #load directive.

    HelloWorld.csx
    
    Console.WriteLine("Hello World");
    
    MyWorld.csx
    
    #load HelloWorld.csx
    Console.WriteLine("MyWorld");
    
    //output 
    Hello World
    MyWorld

The loading of scriptcs is recursive and the script that gets loaded first gets executed first. 
Scriptcs also ensures that only one version of the script is loaded at a time (e.g. File1 loads File2 & File3 and File2 also loads File3).

<!--excerpt-->
In .net things would be incomplete without Nuget support. Scriptcs allows us to include any package available on Nuget as we want.
Scriptcs relies on the nuget Api and not the nuget.exe to do it's job. It covers quite a few scenarios easily.


* How do I add a Nuget package ?

    > scriptcs install YourPackage
    
Scriptcs will create a scriptcs_packages.config in the directory. It will also make the dll available instantly in the app domain.
Adding the required usings to start using the package is upto us. What this also means is that scripts become reusable by default.
Put the script and the packages.config on Github and everyone can use it.

Show me the code 

    scriptcs install CsvHelper
    /*
    create a Data.csv like below
    Subject,Predicate,Object
    ScriptCS,is,useful
    I,am,awesome
    
    Inside Demo.csx
    */
    using CsvHelper;
    using System.IO;

	var reader = new StreamReader("Data.csv");
	var csvReader = new CsvReader(reader);
	var records = csvReader.GetRecords<Sentence>();

	foreach (var record in records) {
	    Console.WriteLine(record);
	}

	class Sentence
	{
		public string Subject { get; set; }
		public string Predicate { get; set; }
		public string Object { get; set; }

		public override string ToString()
		{
			return Subject + " ..." + Predicate + "..." + Object;
		}
	}

* How do i use a pcakges.config that I have from an existing project ?

Just copy and drop it with the scripts and ScriptCS will honour that. 
If the pckages aren't present in the directory it will go ahead and restore them.

Next, we look at ScriptPacks and how they make life easier.