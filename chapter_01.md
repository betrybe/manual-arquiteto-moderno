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

### Assumptions and Simplifications

### Bounded Contexts to start splitting your Monolith 

#### One Repository / One Service + Continuous Delivery

#### Open APIs

### Context Map to understand Team and Technical interactions

### Focus on Business Value





## Sum up

