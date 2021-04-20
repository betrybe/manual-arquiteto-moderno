It is interesting how the concepts of Clean Architecture can be related in several aspects to Eric Evans’s book Domain-Driven Design. We can exemplify this relationship when the proposal to create a language close to the business is repeatedly mentioned in the DDD book: the ubiquitous language. Robert C. Martin’s book Clean Architecture proposes separating the business code, that is, not tying business rules with the chosen technology.

> **INFO**: Clean architecture is an excellent book and is part of a "trilogy" whose reading is recommended: Clean Code: A Handbook of Agile Software Craftsmanship (Robert C. Martin Series); The Clean Coder: A Code of Conduct for Professional Programmers (Robert C. Martin Series); and finally Clean Architecture: A Craftsman's Guide to Software Structure and Design (Robert C. Martin Series). 
>
> We assume the reader has previously read the book Clean Architecture. This chapter doesn’t aim to talk about the book but explain how we apply its concepts from a practical perspective.

We will start by talking about the strategy of dividing and conquering. That strategy has many advantages, and we will mention two of them:

* **Tests**: One of the significant advantages of this separation lies in the ease of testing, mainly unit tests. Greater separation of the layers makes it easier, for example, to mock the infrastructure layers and make the test cheap, i.e., easy to create and quick to run. As a result, these tests tend to be performed constantly, quickly, and with greater coverage. It is common to observe projects in which tests take hours to be carried out with negative consequences. Tests that fail constantly tend to be ignored and unreliable tests will make the team never know if there was a regression problem or just that “friendly mistake”.

> **TIP:** it is worth noting we are not criticizing other tests; however, unit tests have advantages. Considering that technologies like Hibernate, Java, and JPA have their own tests, remember that what you actually test are the rules of your business.

* **Low impact on changes**: greater separation from business to technology leads to fewer impacts in technology change cases, such as an exchange between database vendors.

> **TIP:** in architecture, pragmatism is crucial. An architect needs to solve a problem using technology as a means, not as an end. Therefore, the user does not need - and often does not want - to know which database or language is being used.

Look at the following image:

![](../images/chapter_04_01.jpg)

*Source: https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html*

We will use the image above to discuss attention points that are worth applying in your architecture:


* Have an effective testing strategy that follows the testing pyramid;
* The structures must be isolated in individual modules. When (not if) we change our minds, we will need to change in just one place;
* "Screaming Architecture" also known as intended use. When looking at the package structure, you can immediately see what the application does, and not its technical details. It is similar to when using package by layer instead of package by feature.

* All business logic must be in a use case, so it will be easy to find and not duplicate anywhere else;

* It will be a good monolith with clear use cases you can split into microservices later, after learning more about them.

## Layering granularity

Something widely discussed in architecture books is the granularity of layers. Over time, it is noticeable that the layers can help both in abstraction and separation of business responsibility and in increasing the complexity of the code.

> **TIP:** Always evaluate when placing more layers in an application and make sure they do not become a weapon of destruction instead of an aid item.

The strategy described by the Clean Architecture book is an “outside to inside” view, i.e., the framework layer accesses the adaptation layer according to the dependency principle. We will generally describe the layers below.

### Entities

If you come from DDD, you will not see much news in this layer and the concepts. It is responsible for encapsulating the business domain. The main point is that this layer is the core, that is, the reason for making the application itself. In other words, this is where the business rules are concentrated, and it should not change according to external items, such as modifying the database.

> **TIP:** in particular, we don’t think it’s a "federal crime" to have technologies that tend not to change, for example, utility libraries used by the whole company. Just interfaces and classes might be used in all layers in a smaller application, like a repository’s interface.

### Use cases

In this layer, the actions of the business rule are concentrated. One way of thinking is that this is a continuation of the entity layer. Many rules that involve entities are too big to fit only in the entity, not to mention the classic problem of single responsibility we talked about so much in [SOLID](https://en.wikipedia.org/wiki/SOLID).

### Adaptation interface

An interesting point of view is that this layer is a great implementation of the [Adapter](https://refactoring.guru/design-patterns/adapter) design pattern. Its main objective is to make the layers of entities and use cases more transparent. For example, a repository interface can have several implementations, whether it’s a relational or a non-relational database.

The main goal of this layer is to ensure that technology changes do not impact the other layers. After all, users don’t care if the database is Cassandra or PostgreSQL, but, at a technical level, it is important to think about the different modeling strategies.

### Frameworks


This is the layer that “doesn't matter” for the business. In other words, it is the layer for the "means" and not for the "end". It consists of tools and technologies such as a database. The central aspect of this layer’s strategy is to prevent it from moving to as few layers as possible. The less code in this layer, the better; it will have what is necessary to interconnect the technologies.

## Conclusion


The Clean Architecture book provides a good reference for applying mature architectures and a better strategy on how to use and communicate between layers. An important point: there is no silver bullet, and neither is the book in question. The material is quite rich and full of details; however, remember that layers tend to increase your code’s complexity; the more layers are created, the more layers are maintained. As the book [Fundamentals of Software Architecture](https://www.amazon.com/Fundamentals-Software-Architecture-Comprehensive-Characteristics/dp/1492043451) suggests: “everything is a trade-off”, and common sense is still the best tool for the architect to know *when* and *how* to apply concepts and practices.