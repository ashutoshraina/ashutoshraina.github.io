---
layout: post
title: Up and Running with Asp.net MVC Mono and Monodevelop
category: monodevelop, mono, opensource, asp.net mvc, tips
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---
After 3 hours worth of work, I was finally able to run Asp.net MVC4 using monodevelop on mono. I will talk about installaing monodevelop later (that was a painful exercise). So, assuming you have monodevelop installed on windows and latest version of mono installed (mono-3.2.3)as well. Now, go file new solution Asp.net MVC project with razor. Run it and wait for the world to fall apart.
<!--excerpt-->

![Mono MVC Error](/stylesheets/images/posts/mono-mvc-error.png)

Now, here is the long recipe of fixing it one step at a time. 
    
    Go to Project Options -> General -> Change the Target Framework to Mono/.net4.5

Install the Asp.net MVC Nuget package.

![Nuget Install](/stylesheets/images/posts/nuget-install.png)

Now, compile and go yayy!!
Press F5 and wait for your happiness to go away.

    "Could not launch web server. Make sure that XSP4 web server is installed".

Don't worry it is installed the mono guys just wanted to have fun by poking us with a stick. Copy the **winhack** folder from 
    
    C:\Program Files (x86)\Mono-3.2.3\lib\mono\4.0
to 
    
    C:\Program Files (x86)\Mono-3.2.3\lib\mono\4.5

I was only able to arrive at this hack by looking at the monosoftdebugger source code and a bit of hit and miss. In between I asked around if there was a way to attache IIS Express to monodevelop, I couldn't find anything that was simple and obvious (ended up with a useful IISExpress manager utility, more on that later). If you change the target framework to .net 4 and monitor the application output tab then you will the following and it will make sense as to how I reached winhack folder.

    Loaded assembly: C:\Program Files (x86)\Mono-3.2.3\lib\mono\4.5\winhack\xsp4.exe

Now, press F5 again and the misery continues, but thankfully this is something we understand.

    System.InvalidOperationException
    Conflicting versions of ASP.NET Web Pages detected: specified version is "1.0.0.0", but the version in bin is "3.0.0.0". To continue, remove files from the application's bin directory or remove the version specification in web.config.

In project's web.config change the version.

    <add key="webpages:Version" value="3.0.0.0" />

Let's press F5 again.

    System.InvalidOperationException
    Could not locate Razor Host Factory type: System.Web.Mvc.MvcWebRazorHostFactory, System.Web.Mvc, Version=3.0.0.0, Culture=neutral, PublicKeyToken=31BF3856AD364E35

In the web.config located in the views folder change the version to the appropriate mvc version.

    <host factoryType="System.Web.Mvc.MvcWebRazorHostFactory, System.Web.Mvc, Version=5.0.0.0, Culture=neutral, PublicKeyToken=31BF3856AD364E35" />

Side Note : Better, change the version wherever it needs to be updated.

Now, Press F5.

![Success](/stylesheets/images/posts/mvc-mono-success.png)

