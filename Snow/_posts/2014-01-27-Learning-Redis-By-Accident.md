---
layout: post
title: Learning Redis by accident
category: .net, CSharp, redis, opensource
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---

While exploring the pub-sub mechanism in MongoDB, I came across Redis. Redis has this built in. This gave me the incentive to scratch a long time itch and try redis. Redis is eseentially an in-memory database. It is supposed to be fast (like really fast). So, I thought I would take it for a spin. Considering, I am on windows and it seems like there is no direct support for windows. Windows remains the poor second, but we will let it be for now.
Microsoft provides the executable for 2.6 and we can download it from there.

<!--excerpt-->

Now, how do we talk to redis ? First, as expected there is a CLI which is quite simple and easy. Second, get some packages from nuget and start writing C#. I tried the CLI for an hour or so and then moved on to C#. (CLI is no slouch, you can do a pub-sub there). The things you can do with redis are simple and few. That is not a such a bad thing because it keeps the clutter out. It does a few things and does them really well and really fast.
Now, for talking to redis there are two primary options BookSleeve (by Marc Garvel) and ServiceStack.Redis. I chose the former just for the name(no kidding naming is still a hard problem).

Let's get to the coding bit.
Open up a connection, insert something and get something back.

    RedisConnection connection = new RedisConnection("localhost");
    connection.Open();
    //0 is the database number, you can set it whatever you want
    connection.Strings.Set(0, "foo", "bar");
    var bar = connection.Strings.GetString(0, "foo");

Now, before we move further just for clarity Redis supports the following data structures.

    Strings
    Hashes
    Lists
    Sets
    Sorted-Sets

The api surface in booksleeve brings the simplicity of redis to the surface.

    connection.Hashes.Set(0, "duck", new Dictionary<string,byte[]>{ { "quack", Encoding.UTF8.GetBytes("I am a quack") } });
    var quack = connection.Hashes.GetString(0, "duck", "quack");
    Console.WriteLine(quack.Result);

    connection.Lists.AddFirst(0, "baz", "bazz");
    var bazz = connection.Lists.GetString(0, "baz", 0);
    Console.WriteLine(bazz.Result);

Another, interesting feature of redis is the ability to expire data. This can be quite handy especially when cache needs to be invalidated.

    connection.Strings.Set(0, "temp", "aboutToExpire", 4);
    var temp = connection.Strings.GetString(0, "temp");
    Console.WriteLine(temp.Result);
    Thread.Sleep(4000);
    temp = connection.Strings.GetString(0, "temp");
    Console.WriteLine(string.IsNullOrEmpty(temp.Result));

Publish and subscribe was surprisingly terse.

    connection.Publish("topic", "Hello From Pub");
    
    var channel = connection.GetOpenSubscriberChannel();
    channel.Subscribe("topic", SubscritionCallback);
    
    private static void SubscritionCallback (string key,byte[] message) {
    Console.WriteLine(key);
    Console.WriteLine(Encoding.UTF8.GetString((message)));
    }

This wasn't so hard and can be quite useful in situation where you simply need to hold data and not pay the penalty of going back to a full database again and again. I enjoyed writing about redis, hope you like reading it.