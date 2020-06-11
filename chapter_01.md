# I've been reading about DDD, where should I go next? 

You’ve been coding Java for more than eight years now, you’ve read about Domain-Driven Design and you want to apply that to a real-life project. What does that look like? What does it actually mean to apply DDD in today’s ecosystem? Is it really worth the time? 

DDD provides us with a very good theoretical background and patterns to build robust software that brings real business value. To do that, DDD helps you by providing a framework to organize how the software is built and how to optimize for independent teams that can work together by consuming well-defined APIs. At the end of the day, DDD gives you principles that you will need to translate to Java or any other language of your choice. But when you do that, you will need to make some hard decisions on how these Java components/services/applications will run and interact. 

This chapter covers the practical side of how these concepts can be mapped into an existing example that runs on top of Kubernetes,  giving you practical tips about how DDD concepts will map to a concrete technology stack. Of course, there are tons of different options to choose from today, but you can take this as an example of what kind of things you will need to pay attention to when going through this journey. 

It is important to highlight that this chapter is not about DDD, so if you are new to DDD, the following books are recommended: 
- [Implementing DDD](https://www.amazon.co.uk/Implementing-Domain-Driven-Design-Vaughn-Vernon/dp/0321834577)
- [DDD Distilled](https://www.amazon.co.uk/Domain-Driven-Design-Distilled-Vaughn-Vernon/dp/0134434420/ref=pd_lpo_14_t_1/262-0200870-8496500?_encoding=UTF8&pd_rd_i=0134434420&pd_rd_r=c7957a5b-3f2f-4008-8c93-8a9b5792c448&pd_rd_w=JKKyX&pd_rd_wg=dFALp&pf_rd_p=7b8e3b03-1439-4489-abd4-4a138cf4eca6&pf_rd_r=W41G9RPNEBHEF8Y5DXG8&psc=1&refRID=W41G9RPNEBHEF8Y5DXG8)

This chapter is divided into two main sections:
- [Introduction to Java-related topics and Cloud](#java-in-the-cloud) 
- [From Monolith to K8s using DDD](#from-monolith-to-k8s-using-ddd)


## Java in the Cloud

There are a lot of frameworks out there now aiming to provide an easier experience for developers creating microservices. Examples of this are Spring Boot, Quarkus, Micronaut, Helidon, etc. In general, what these frameworks aim for is to create a standalone JAR file that can be executed, providing you a dependency-free executable that only requires the Java Virtual Machine to run. 

This pretty much goes in contrast with what we (as the Java Community) were doing five years ago, and some of us still do, which was to deploy our Java applications inside an Application Server or a Servlet Container such as Tomcat. 

While we used to have a monolith with all the features of our large applications built inside, now we aim to have a set of services with well-defined functionality.  These new (micro)services share the following characteristics:
- Tend to colocate and version all the artifacts that are needed to go from source code to a running service in an environment.
- Each service is built, maintained, evolved and deployed by a different team
- Each service has its own release cycle 
- Each service expose a well-defined set of APIs

Building a Service today with REST endpoints is a fairly easy task if you are using one of these frameworks previously mentioned. You have an annotation-based programming model that allows you to map Java Methods to REST endpoints and advanced Serialization/Deserialization mechanisms that will deal with all the boilerplate of parsing HTTP requests. 

The real problem arises when you start having more than a handful of services. Running each service in its own JVM will push you to run each service on a different port and take care of issues when these JVMs crash. For that reason, the industry quickly jumped to containers around 2015. 


### Containers & Containers Orchestration

Following the same line of going dependency-free, like the Java frameworks mentioned in the previous section, Containers help us to run our software anywhere. This means that we go one step further and now we don’t even want to depend on the Java Virtual Machine being installed in our host machine where the services will run. 

Docker (a container runtime) helped us with that. We can encapsulate our standalone JAR along with the JVM and configuration files to make it a container that can be run anywhere where the Docker Container Runtime is installed. 

Each Docker container has its own isolated runtime. Enabling us to isolate failures at the Docker container boundaries. 

When you have one Bounded Context and a few Services, you are probably fine just running these Java Microservices using a script (or docker-compose). However, even for everyday development practices, you will notice that each of these services will require a dedicated port and you will need to keep track of these ports to make sure that they are available for each of the services. 

When the number of services grows, this becomes unmanageable. 
For that reason, Container Orchestrators have become popular in the last few years, and  Kubernetes is leading the way. Kubernetes is in charge of dealing with how to create these container runtimes, how to scale them when there is load, and how to deal with containers that are misbehaving or crashing. 

<Reference to Karina’s list of containers orchestrators>

Kubernetes success is based on the fact that each major cloud provider provides a managed Kubernetes Service, making it the defacto standard for multi-cloud support. In other words, no matter which Cloud Provider you choose, you can always trust that there will be a Kubernetes API exposed for you to interact and provision your services. 


### Capitalizing on DDD benefits

If you follow the DDD path, you need to capitalize on some of the promises that DDD gave you to start with and make sure that you are ripping the benefits. If we cannot continuously deliver new versions of our services without stopping the entire application, we are just making our life more complicated for nothing. If we are not delivering concrete business value as a result of following DDD, all the changes suggested in this chapter are not worth the effort or the time. 

I recommend the following article: [“The Business Value of using DDD”](https://www.informit.com/articles/article.aspx?p=1944876&seqNum=4), which gives a high-level overview of the benefits of adopting DDD, not for you as a developer, but for your Business. 

The next section covers an example that I’ve created based on my experience while rearchitecting Java Monolithic applications to a more distributed approach. The example is fictional, any similarity with reality is just a coincidence :) I encourage you to abstract the concepts and patterns away from the example scenario and map them to your own domain. At the end of the day, this is just an example, albeit a complex and fully functional one.


## From Monolith to K8s using DDD

This section covers an example scenario that helps us to explain some of the concepts in action. You can map these concepts to your business domain and copy the actual technical solution from the example for some of the challenges presented. 

As expected, creating a full-blown application is hard work and usually requires a lot of time investment. For that reason, the following example is provided as a set of Open Source repositories where you can contribute to making it better. As a real application, the example will evolve in time, adding more tools and best practices, so I invite you to participate in this journey where we can all learn and share valuable information together. 

[From Monolith to K8s Github Repository](https://github.com/salaboy/from-monolith-to-k8s)

We begin our journey with a Monolith Java Application. The scenario that we will cover belongs to a company that is in charge of providing a platform to create Conference websites. Imagine that each of our customers requires us to host and scale their Conference website. 
We all have seen big Java Web Applications and for this scenario, the application will look like this: 

![chapter_01_01](images/chapter_01_01.png)

The “Customer Management” Facade is in charge of isolating different customers from each other. In some companies, this is defined as a multi-tenant platform or application. Unfortunately, this is quite common in the Java space. For historical reasons, implementations ended up growing into big, scary monoliths that came with a lot of scalability issues as well as data and traffic isolation challenges. No matter how fancy our platform looks, it is just running in a single JVM. You have no way of scaling each individual customer - you scale all or nothing. 

As you can see in the red box, each Conference Site will contain a bunch of modules depending on each customer selection, but in reality, at runtime, all the code will be there for every single conference. 

> Please note that if you have a platform like this and it does the work that it is supposed to do,you **should NOT** change it. Unless you are having problems managing this “Platform” or scaling it, you shouldn’t rearchitect the whole thing.

Now this Monolith architecture has some clear drawbacks, and for this scenario, we can consider the following reason to rearchitect into a proper Cloud-Native Platform:

- Customers cannot be scaled independently
- Customer traffic is all handled by the same application
- Single Point of Failure in the JVM
- If data is stored in a database, data is shared across customers. The application code needs to deal with isolating each customer data. The database becomes a bottleneck as well, as all customer data is in the same DB. 
- Every change into the platform requires the entire application to be restarted
- Every developer involved with the application works against the same code base, making a release and merging features a major task with a lot of risks involved. This usually can be done by someone who understands the entire application.

If you already have this application up and running and you have customers using the platform, you will have a good understanding of which features are essential and how you can start re-architecting it. 

As Martin Fowler describes in the linked blog post [Monolith First](https://martinfowler.com/bliki/MonolithFirst.html) is the way to go. By having a monolith you already understand the solution that you need to build, making it easy to estimate how the new architecture will tackle the problems of the existing version. In other words, if you don’t have an existing Monolith, do not start with a distributed architecture from scratch. Create a monolith first and then split if needed. 

The next step in our journey is to decide where to start. In my experience I’ve seen three common patterns repeating:

- **Start new functionalities as separated services**: this is usually recommended if you can afford to maintain the Monolith as it is. New services will not solve the already existing problems, but it will help your developer teams to get used to working with a microservice mindset.
- **Split existing functionality out of the monolith** (and slowly deprecate the old code): if you have pressing issues with the Monolith you can evaluate branching off some of the functionality outside into a new service. This might solve some of your existing problems, but it will not bring any business value immediately. It also adds to the complexity of the day-to-day operations as you might end up running two solutions for the same problem over a long period of time. This can also be used to understand how complex and costly a core rearchitecture can be. 
- **Rearchitect the core of the platform as microservices** (to tackle existing problems): Sooner or later, if you are experiencing problems maintaining and scaling your Monolith, you will need to rethink and redesign the core bits of your platform, making sure that you focus on solving the current scalability and maintenance problems. This can be a costly initiative, but it can be done in complete isolation from your production environments. Several times I’ve seen how this is done as a Proof of Concept to demonstrate that is actually possible and also to make sure that your team members understand the implications from a business (advantages) and technical point of view (new tech stack, new tools).

In this chapter, I will cover the last of these options (**Rearchitect the core of the platform as microservices**) to highlight the solution for our existing problems with the monolith application, but you can explore the other two if they are more appropriate for your situation. 

This is where DDD concepts and patterns become really handy to define how to split the functionalities of the monolith and how to organize our teams around the new services. In the following sections, we will explore some of these concepts in action.


### Assumptions and Simplifications

Platforms are complex beasts, trying to re-architect something big will give you more headaches than solutions. For that reason, you should try to simplify the scope of the problem to tackle different challenges in a controlled way. 

For our scenario, this might mean that instead of trying to go for the re-architecture of the entire platform, first, we try to solve the architecture and shape of a simple Conference Site. This means that instead of focusing on the platform, we first focus on what the Customer will expect for their Conference Site. By understanding the shape of a single Conference Site you and your teams have a well-defined set of challenges to solve that can bring immediate business value. Later we automate how each of these Conference Sites can be provisioned. 

From a more architectural point of view, this might mean a Monolith Conference Site or a Conference Site that is built with different distributed services. If Conference Sites are complex enough and we want to reuse modules for all of them we might consider a distributed approach. Based on my experience, this kind of platform does leverage shared services most of the time so it makes sense to architect them with reusability in mind. This might lead to having multiple services from day one, which is something that you and your teams will need to get used to.

Our independent Conference Sites will look like this: 
![chapter_01_02](images/chapter_01_02.png)

As you can see in the previous diagram, it is quite clear that there are important architectural changes. It is quite common to have the User Interface, in this case, the “Conference Site” box separated from core services. Most of the time, this user-facing component will act as a router, routing requests from the Conference  Site to services that are not directly exposed to users. 

But before digging into technicalities, how do we prioritize and make sure that we start on the right foot? How do we scope these services correctly (not too macro, not too micro)? DDD concepts can help us, providing some answers and guidelines. 


### Bounded Contexts to start splitting your Monolith 

#### One Repository / One Service + Continuous Delivery

#### Open APIs

### Context Map to understand Team and Technical interactions

### Focus on Business Value





## Sum up

