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

### Containers & Containers Orchestration

### Capitalizing on DDD benefits

## From Monolith to K8s using DDD

### Assumptions and Simplifications

### Bounded Contexts to start splitting your Monolith 

#### One Repository / One Service + Continuous Delivery

#### Open APIs

### Context Map to understand Team and Technical interactions

### Focus on Business Value





## Sum up

