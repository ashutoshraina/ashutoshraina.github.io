---
layout: post
title: Everyday utilities for a Windows developer
category: windows, tips
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
    name: Becoming Productive on Windows
    current: 1
    part: Part 1 - Everyday utilities for a Windows Developer
    part: Part 2 - Everyday utilities for a Windows developer - Contd.
---

As I have learned to use the command line more and more on windows I often get stuck due to lack of commands. This can be quite frustrating and if you switch between the *nix and windows then the gap can seem to quite alarming. In this post I will share some tidbits on how to overcome some of these pains. 

First, do yourself and get a better console like Console2 or Mini Commander(or anything else that suits your style). Things are a bit better in Windows 10 (copy paste will be allowed on the command line) but that is still sometime away. If you are willing then I do suggest making the permanent shift to Powershell for all practical purposes, I don't see the windows command line (CMD) ever improving enough to make it my default console. Next, I describe some of the common things I do all the time.
<!--excerpt-->

* **Get CLS to works as well as CLEAR as well Ctrl + L**

Hopefully you got a decent console and it comes with these things. This does help make the switch between *nix and windows slightly less painful.

For everything below we need Sysinternal Tools. It is a powerful set of tools that provides the missing functionality that developes usually get by default in other worlds. When you do use some if these for the first time then you will be asked to accept a license (swallow the pill and move on !!).

* **View Traces or Logs**

DbgView.exe will help you do this. I have used this in the past in production when one of the servers decides to play funny and you can live stream the logs here.

* **Dump Event Logs**

PsLogList dumps event logs on a local or remote NT system.
You can filter the dump by event, for the last n minutes or n days ..Lots of options and very handy in production.

* **Mutliple Desktops**

How is it even possible that Microsoft forgot to ship this by default? Desktops.exe allows you have mutliple desktops on windows and have hotkeys for them. 

![Multiple Desktops](/stylesheets/images/posts/MultipleDesktops.png)

* **dlls loaded by a Process**

Let's see what Sublime Text has to offer.

*Listdlls.exe Sublime Text*

    sublime_text.exe pid: 9984
    Command line: "C:\Program Files\Sublime Text 3\sublime_text.exe"
    Base                Size      Path
    0x00000000a0f80000  0x556000  C:\Program Files\Sublime Text 3\sublime_text.exe
    0x000000004cd60000  0x1a6000  C:\WINDOWS\SYSTEM32\ntdll.dll
    0x000000004c860000  0x13a000  C:\WINDOWS\system32\KERNEL32.DLL
    0x000000004a1d0000  0x10f000  C:\WINDOWS\system32\KERNELBASE.dll
    0x000000004cbe0000  0x171000  C:\WINDOWS\system32\USER32.dll
    0x000000004ada0000  0x144000  C:\WINDOWS\system32\GDI32.dll
    0x000000004c7b0000  0xa5000   C:\WINDOWS\system32\ADVAPI32.dll
    0x000000004b330000  0x140f000  C:\WINDOWS\system32\SHELL32.dll
    0x000000004af10000  0x178000  C:\WINDOWS\system32\ole32.dll


    There were a gazillion more (including MS office dlls !!) 
    

* **RunAs** a different User

Sometimes it is necessary to run a software as a different user (e.g. trying to connect to a database server as a sys account).

The ShellRunas utility allows us to register this feature in the context menu of programs. You can ofcourse run the program as a different program from the command line as well. You can register it using the following command.

    C:\Users\Ashutosh\Tools\SysInternals
    λ ShellRunas.exe /reg 

Once done, you can light up this feature using Shift + Right Click on any program.

* **Zoom**

Another handy utility ZoomIt allows you to zoom in on fly using hotkeys (Ctrl+4 should do it). It is awesome during presentation since you don't have to permanently increase your font. Minimize this to tray and stay happy.

* **CtrlToCap**

Ofcourse we need this. Permanently disabling CAPS Lock is needed.
    
    ctrl2cap.exe /reg 
    
and in the true widows traditon a boot later you are golden.

* **Getting File Handles**

Handles.exe tells you who is holdiong on to which file.

* **View All the Processes**

Option 1 : Curse your existence and go to Task Manager

Option 2 : Use PsList.exe

    C:\Users\Ashutosh\Tools\SysInternals                                        
    λ pslist.exe                                                          
                                                                                                                   
    Process information for ARTHINKPAD:                            
                                                                            

    Name         Pid Pri Thd  Hnd   Priv        CPU Time    Elapsed Time 
    Idle           0   0   4    0      0     0:40:49.468     0:00:00.000 
    System         4   8 189 1562    116     0:00:27.531     0:11:19.227 
    smss         416  11   2   44    308     0:00:00.203     0:11:19.117 
    csrss        660  13  10  627   1896     0:00:00.656     0:11:07.766 
    wininit      724  13   1   75    772     0:00:00.203     0:11:06.762 

* **Kill a Process**

Option 1 : Go the task manager and do it.

Option 2 : Use PsKill.exe 

    C:\Users\Ashutosh\Tools\SysInternals
    λ pskill.exe chrome
    8 processes named chrome killed.

You can also kill a process running on another machine, so quite useful when you want to tame a runaway process in production.

* **Shutdown**

After all this works the computer needs to rest so let's shut it down.
    
   psshutdown.exe

Now, before you really go shutdown everything, put the sysinternal in your path. Next time we will go look for a few other utilities that make life as a developer much easier from the point of view of debugging.