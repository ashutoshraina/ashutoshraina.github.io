---
layout: post
title: Python Development in Visual Studio
category: Tools, Productivity, Tips, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---

**What ..you must be mad !!**

IronPython has been around for some time now. Python tools for Visual Studio have recently been given a facelift. It has been lifted to become a more complete tool which we can use for Python development with Visual Studio.
I have recently been flirting with Python since it is easier and quicker to just get some stuff done in Python. It can easily be a second language for a C# developer and I do intend to keep it in my toolbox. There are alternatives available including but not limited to PyDev plugin for eclipse and Aptana Studio which comes with all the bells bundled together.

<!--excerpt-->
**OK..So how do we get started ?**

If you don't have python then I suggest you grab python 3 and above. To save yourself hours and days I suggest follow this post here [Install Python on Windows](https://zignar.net/2012/06/17/install-python-on-windows/ "Install Python on Windows").

Go get all the needed mojo from here [Python Tools For Visual Studio](http://pytools.codeplex.com/ Python Tools For Visual Studio). Do remember to pick the correct msi as per your VS installation. Do watch some videos if you feel like, they will give you a sense of what to expect. It is excruciatingly painful to get the python set up done if you are a newbie ( python guys are you listening ?? the defaults should be easy ).

A simple HellWorld would be nice. Once you finish the installation Go File -&gt; New Project and wait awesomeness to pop up.

![HelloPython](/stylesheets/images/posts/hellopython.png)
Now, here comes the part where the defaults are stupid. Go to Tools -&gt; Options -&gt; Python Tools -&gt; Check the Wait for input when process exits normally.

Now create a new Project HelloPython and wait for it to spin up. Right Click on the file and say Debug As Script.

    Hello World
    Press any key to continue . . .


**Show me something more complicated**

In the solution you should see Virtual Environments where you can add the virtual environments. In VS 2013, I see that managing the python interpreter has also moved there and the whole thing is now called PythonEnvironments. That is a nice touch and I think going forward it makes life easy. In VS 2012 you can go to your project properties and pick the python interpreter of your choice. 
Creating virtual environments is easy. In VS 2012, First create a virtual environment and then add it to your project.
In VS 2013 this experience has come together nicely and the it looks more and more a native part of VS.

![Python Tools For Visual Studio](/stylesheets/images/posts/pytools-vs2013.png)

In VS 2013, as you can see I have pymongo installed ( using pip ). Right click on Python 64 bit ... and install package to choose from pip of easy-install. In VS 2012 you can right click on your environment to achieve the same goal.

Back to some code.

    from pymongo import MongoClient
    import pprint
        def hello():
            client = MongoClient("localhost",27017)
            database = client['blog']
            result = database.posts.find_one()
            pprint.pprint(result)

    hello();


Are there any obvious benefits to run Python in VS ?  I think the familiarity of the environment and the easy of use are compelling reasons. Refactoring and all the big IDE goodies are just nice add-ons.

This feels nice !!