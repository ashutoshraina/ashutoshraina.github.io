---
layout: post
title: TeamCity- Recover Admin Password
category: Tools, Productivity, Tips
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---

The trials and tribulations of this world are beyond me. Sometimes, a simple gesture can save your life.
I lost(forgot, forgive my small brain it has to keep up with a day job) my TeamCity login details.
Searching online will lead you to this place [Stackoverflow Post](http://stackoverflow.com/questions/506115/teamcity-forgotten-admin-password-where-to-look "StackOverflow Post").

The solution there is mighty correct barring two things. One, your TeamCity data directory path is likely to C:\ProgramData\JetBrains\TeamCity\ on a windows box and not C:\users\foo\TeamCity. Second, you need to run CMD as admin.
So,

    Step 1 Stop TeamCity service ( either through services.msc or the bat files ).
    
    Step 2  Open CMD as ADMINISTRATOR
    (don't forget the admin part you will go bonkers trying to figure out what is happening) and go to C:\TeamCity\webapps\ROOT\WEB-INF\lib
    
    Step 3 type -cp server.jar; hsqldb.jar ChangePassword USERNAME PASSWORD C:\ProgramData\JetBrain\TeamCity

If all is successful then you password has been reset and you can login. Here's hoping to a few hours saved for anyone who comes around to reading this.