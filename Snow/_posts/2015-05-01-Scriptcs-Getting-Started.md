---
layout: post
title: Scriptcs : Getting Started
category: scriptcs, open source
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
    name: Scriptcs
    current: 1
    part: Part 1 - Getting Started
    part: Part 2 - Up & Running
    part: Part 3 - Becoming more productive with Scriptcs
    part: Part 4 - Raising the bar with better abstractions
    part: Part 5 - Doing something Useful
    part: Part 6 - Tools to make you productive
---

C# even though a really nice language has always been tied to Visual Studio. 
With mono we had the ability to write it on non-windows platforms as well. 
One other thing that mono had was the repl and the ability to script. 

What is Scriptcs ?

* Use C# as a scripting language powered by Roslyn
* Uses the Rosyln nuget package
* No IDE, No dlls
* Xplat, CLI
* Lighter experience with faster feedback

<!--excerpt-->


How many times have we done this ?

![HelloWorld](/stylesheets/images/posts/VSFlow.gif)

* Everything is still typed and you get to try things out iteratively without using VS.
* Roslyn is available as nuget package which means that we can get in there and do useful things with it.

* Open Source, XPlat ...things that make us believe that it is going to solve a lot of problems.
* Really nice CLI experience and what more there is a REPL there as well.
* Significantly lighter experience which is a welcome change.


## Getting Started

ScriptCS on Windows is easy to get started using Chocolatey.

    cinst scriptcs
    
For Mac, Homebrew is the simplest way to go.
    
    brew install scriptcs
   
I will interchangeably use mac and windows, but the things should be the same (leaving out the differences in runtime between mono and .net).

    $ scriptcs
    scriptcs (ctrl-c to exit or :help for help)

    > Console.WriteLine("Hello World");
    Hello World
    >

That's a good start, we are able to write Hello World without doing the File -> New Project dance.

