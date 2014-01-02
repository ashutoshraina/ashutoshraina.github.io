---
layout: post
title: Useful Extension Methods
category: .net, CSharp, Tips, Extension Methods
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---

Extension methods provide a very useful way of extending the functionality of a Type (value, reference or interface). The earlier way of extending or rather adding a functionality through inheritance or making an entirely new type from scratch were tedious to say the least. 
Extension methods are basically static methods with the calling syntax same as that of static methods. Over the course of the last few weeks i have come to realise they reduce a lot of repetitiveness from the code when used appropriately.<strong>MSDN</strong> specification also clearly states they must be used sparingly.
So,here are a few useful extension methods.
The syntax has only one new thing, the use of <em>this </em>keyword on the type that the extension method is meant for. 
  
    public static XElement ToXElement(this XmlNode node)
    {
       var xDoc = new XDocument();
       using (var xmlWriter = xDoc.CreateWriter())
              node.WriteTo(xmlWriter);
       return xDoc.Root;
    }

So, calling the code would be something like ,

    var xelement = node.ToXElement();

xelement is now an XElement. This might look very plain and simple at the outset but when you really start to have a lot of repetitive functionality over a type this could really bail you out and make the code more readable to say the least. For e.g inside a for loop if you have a lot of code that operates over a type then it could be useful to take it to an extension method and then call the extension method statically.Further, extension methods can also have overloads so that is another nifty feature.
The following extension method returns the XmlAttributeCollection for a XmlNode


    public static XmlAttributeCollection GetXmlAtrributeCollection(this XmlNode node)
    {
        var xmlDoc = new XmlDocument();
        var element = node.ToXElement();
        using (var reader = element.CreateReader())
        {
          xmlDoc.Load(reader);
          return xmlDoc.DocumentElement != null ? xmlDoc.DocumentElement.Attributes : null;
        }
    } 

Too much of XML ?? Lets get one really useful extension method. [Lookup](http://msdn.microsoft.com/en-us/library/bb460184.aspx "Lookup") is an immutable class in System.Linq. It works similar to a dictionary but does not enforce unique keys , so returns an IEnumerable.
Unfortunately, the creation of a Lookup is internal to .net. So we can only call ToLookup() from a Dictionary etc. So ,i needed some way of adding data to the Lookup.Here is what i came up with. 

    public class Pairs 
        {
            public String Key { get; set; }
            public Object Value { get; set; }
        }
    public static class LookupExtensionMethods
        {
            public static Lookup<String,Object> AddToLookup(this Lookup<String,Object> lookup,
                                                           List<Pairs> pairlist)
            {
                var temp = (Lookup<String, Object>)pairlist.ToLookup(p => p.Key, p => p.Value);
                lookup.Concat(temp);
                return lookup;
            }

        public static Lookup<String, Object> AddToLookup(this Lookup<String, Object> lookup, Pairs pair)
        {
            var pairlist = new List<Pairs> {pair};
            var temp = (Lookup<String, Object>)pairlist.ToLookup(p => p.Key, p => p.Value);
            lookup.Concat(temp);
            return lookup;
        }
    }


Lastly, there is no way to replace the first occurrence of a string in C# straight out of the box(I bet you did not notice that). 

     public static string ReplaceFirstOccurrance(this string original, string oldValue, string newValue)
     {
         if (String.IsNullOrEmpty(original))
                    return String.Empty;
         if (String.IsNullOrEmpty(oldValue))
                    return original;
         if (String.IsNullOrEmpty(newValue))
                    newValue = String.Empty;
         var loc = original.IndexOf(oldValue);
         return loc == -1 ? original : original.Remove(loc, oldValue.Length).Insert(loc, newValue);
     }

Suddenly, the world seems a lot better.