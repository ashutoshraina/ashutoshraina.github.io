---
layout: post
title: Entity Framework Complex Types
category: entity-framework, .net, csharp 
author: ashutosh raina
email: ashutoshraina1989@gmail.com
---

In complicated models we need complex types to satisfy complex business logic .The most simple example would be having address as a complex type. Complex types are objects with no identity of their own. They are dependent on the parent for their existence. Consider the case where address has no real significance if it is not associated with a customer. This also implies that lazy loading of the complex type is not possible.

<!--excerpt-->

    public class Address
        {
            public String Street { get; set; }
            public String City { get; set; }
            public String State { get; set; }
            public int Zip { get; set; }
        }

Add the new property to the UserDetail class and associate the instance with a new address.

    public class UserDetail
      {
          public int UserDetailId { get; set; }
          public String Name { get; set; }
          public String Password { get; set; }
          public String UserRole { get; set; }
          public DateTime DateOfCreation { get; set; }
          public Address UserAddress { get; set; }
      }
    context.UserDetails.Add(new UserDetail{
                                         Name = "TestName",
                                         Password = "TestPassword",
                                         UserRole = "Administrator",
                                         DateOfCreation = DateTime.Now,
                                         UserAddress = new Address{
                                                     City = "Vashi",
                                                     State = "Maharashtra",
                                                     Street = "High Street",
                                                     Zip = 400705
                                                     }
                                                   });

•   A good practice is to always explicitly register a complex type.
•   Always initialize them; otherwise we will get a null reference exception.

So, in the mapping configuration we call explicitly register our complex type. There are two ways:-
1) Use the DataAnnotations to mark the Address class as a ComplexType.
2) Use the FluentApi to register the complex type.

    modelBuilder.ComplexType<Address>();

We can further override the default naming conventions of the EntityFramework to have more readable column names in the database. This again can be done using both DataAnnotations and FluentApi.

    modelBuilder.Entity<UserDetail>().
                    Property(p => p.UserAddress.State)
                    .HasColumnName("State");

    modelBuilder.Entity<UserDetail>()
                    .Property(p => p.UserAddress.City)
                    .HasColumnName("City");

![Complex Type](/stylesheets/images/posts/complex-types.jpg "Complex Type")

Figure 1 Users Table with a Complex Type before and after configuration
