---
layout: post
title: Implicit and Explicit Operator in C#
category: .net, csharp, C# 
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---
One of the features that I have never used in C# is the implicit and Explicit keywords. 

The definitive word from MSDN on explicit is "The explicit keyword declares a user-defined type conversion operator that must be invoked with a cast." Omitting the cast will result in a compile time warning.

The definitive word from MSDN on implicit is "The implicit keyword is used to declare an implicit user-defined type conversion operator." Implicit doesn't require an explicit cast and makes the syntax a lot easier.

<!--excerpt-->

    public class Program
    {
        public static void Main()
        {
            
        Email test = "alice@test.com";
        System.Console.WriteLine("Test: " + test);        
        }
    }

    class Email
        {
            private string user;
            private string domain;
            public Email(string user, string domain)
            {
                this.user = user;
                this.domain = domain;
            }
            static public implicit operator Email(string value)
            {
                var parts = value.Split('@');
                if (parts.Length != 2)
                    return null;
                return new Email(parts[0], parts[1]);
            }
            static public implicit operator string(Email value)
            {
                return "User = " + value.user + ", Domain = " + value.domain;
            }
        }

I think it would quite useful in providing conversion operations from one type to another. The simplest candidate would when you have multiple addresses and you need them in a certain format (read Type) to send it to a shipping agency.
