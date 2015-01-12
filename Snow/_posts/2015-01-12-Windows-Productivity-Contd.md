---
layout: post
title: Everyday utilities for a Windows developer - Contd.
category: windows, tips
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
    name: Becoming Productive on Windows
    current: 2
    part: Part 1 - Everyday utilities for a Windows Developer
    part: Part 2 - Everyday utilities for a Windows developer - Contd.
---

From the previous post if you do end up using [cmder mini](http://bliker.github.io/cmder/ "Cmder Mini") like me then you should also register it and let it show up in the explorer. 

        cmder.exe /REGISTER ALL

This time we look at some utility programs that are often required when doing deeper analysis in a windows environment.
<!--excerpt-->

* **Process Explorer**

I consider it to be an advanced version of Task Manager. Whenever I want to see what the process is doing or kill it for whatever reason this is my go to program. Infact, it does give you the option of replacing the Task Manager with Process Explorer. I have used this in the past to identify which process has held onto my file especially when I want to delete it. 


* **Process Monitor**

Process Monitor on the other hand, is a different tool altogether. It gives you the ability to see how the Read/Write is happening, and whether the operations succeeded or not. The ability to see these lower level operations is priceless.

    8:45:38.6874836 PM  chrome.exe  5992    TCP Receive ARThinkpad:57642 -> a23-211-203-17.deploy.static.akamaitechnologies.com:http    SUCCESS Length: 1442, seqnum: 0, connid: 0
    8:45:38.6876860 PM  chrome.exe  5992    TCP Disconnect  ARThinkpad:57689 -> ec2-54-169-42-120.ap-southeast-1.compute.amazonaws.com:http SUCCESS Length: 0, seqnum: 0, connid: 0
    8:45:38.6887398 PM  chrome.exe  5992    TCP Disconnect  ARThinkpad:57671 -> 119.81.205.68-static.reverse.softlayer.com:http SUCCESS Length: 0, seqnum: 0, connid: 0
    8:45:38.7076694 PM  chrome.exe  5992    TCP Receive ARThinkpad:57642 -> a23-211-203-17.deploy.static.akamaitechnologies.com:http    SUCCESS Length: 1442, seqnum: 0, connid: 0
    8:45:38.7081062 PM  chrome.exe  5992    TCP Disconnect  ARThinkpad:57682 



* **Tcp View**

Another very nice utility that you can use to see what is happening under the hood. I once used it to triage high network utilisation on the database server, due to a runaway query. Being able to narrow down communication between two hosts is important and this tool is just the right fit. Also, when you are working with tunnels (as I have done in the past) then it pays to know what your tcp connections are upto.


    mqsvc.exe   2872    TCPV6   arthinkpad  2107    arthinkpad  0   LISTENING                                       
    mqsvc.exe   2872    TCPV6   arthinkpad  49158   arthinkpad  0   LISTENING                                       
    QuickControlService.exe 12808   TCP ARThinkpad  5365    ARThinkpad  0   LISTENING                                       
    services.exe    780 TCP ARThinkpad  49160   ARThinkpad  0   LISTENING                                       

* **Debug View**
How this has saved my life !! Read debug logs like an ancient boss. Life saver when working with ancient systems which don't do centralised logging.

Apart from these, one more utility that I have recently added is Wox. 

* **Wox**

[Wox]("https://www.getwox.com/",Wox) is Alfred/Spotlight for Windows only a so much better than launchy. It has themes, it has plugins and is free. May be we should call it Batman!!

![Wox](/stylesheets/images/posts/wox.png)

Writing plugins is simple, and look they even have a documentation page.
[Docs]("http://doc.getwox.com/en/plugin/csharp_plugin.html",Docs)

Hope this does help you as it as helped me improve my workflow.