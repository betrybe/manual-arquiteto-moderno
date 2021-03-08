EN

# I've been reading about DDD, where should I go next? 

You’ve been coding Java for many years now, you’ve read about Domain-Driven Design (DDD) and you want to apply that to a real-life project. What does that look like? What does it actually mean to apply DDD in today’s ecosystem? Is it really worth the time? 

DDD provides us with a very good theoretical background and patterns to build robust software that brings real business value. To do that, DDD helps you by providing a framework to organize how the software is built and how to optimize for independent teams that can work together by consuming well-defined APIs. At the end of the day, DDD gives you principles that you will need to translate to Java or any other language of your choice. But when you do that, you will need to make some hard decisions on how these Java components/services/applications will run and interact. 

This chapter covers the practical side of how these concepts can be mapped into an existing example that runs on top of Kubernetes, giving you practical tips about how DDD concepts can map to a concrete technology stack. Of course, there are tons of different options to choose from today, but you can take this as an example of what kind of things you will need to pay attention to when going through this journey. 

>  **INFO:** is important to highlight that this chapter is not about the basic concepts DDD, so if you are new to DDD, the following books are recommended: [Implementing DDD](https://www.amazon.co.uk/Implementing-Domain-Driven-Design-Vaughn-Vernon/dp/0321834577) and [DDD Distilled](https://www.amazon.co.uk/Domain-Driven-Design-Distilled-Vaughn-Vernon/dp/0134434420/ref=pd_lpo_14_t_1/262-0200870-8496500?_encoding=UTF8&pd_rd_i=0134434420&pd_rd_r=c7957a5b-3f2f-4008-8c93-8a9b5792c448&pd_rd_w=JKKyX&pd_rd_wg=dFALp&pf_rd_p=7b8e3b03-1439-4489-abd4-4a138cf4eca6&pf_rd_r=W41G9RPNEBHEF8Y5DXG8&psc=1&refRID=W41G9RPNEBHEF8Y5DXG8). 

This chapter is divided into two main sections:
- [Introduction to Java-related topics and Cloud](#java-in-the-cloud) 
- [From Monolith to K8s using DDD](#from-monolith-to-k8s-using-ddd)


## Java in the Cloud

There are a lot of frameworks out there now aiming to provide an easier experience for developers creating microservices. Examples of this are Spring Boot, Quarkus, Micronaut, Helidon, etc. In general, what these frameworks aim for is to create a standalone JAR file that can be executed, providing you a dependency-free executable that only requires the Java Virtual Machine (JVM) to run. 

This pretty much goes in contrast with what we (as the Java Community) were doing five years ago, and some of us still do, which was to deploy our Java applications inside an Application Server or a Servlet Container such as Tomcat. 

While we used to have a monolith with all the features of our large applications built inside, now we aim to have a set of services with well-defined functionality.  These new (micro)services share the following characteristics:
- Tend to colocate and version all the artifacts that are needed to go from source code to a running service in an environment.
- Each service is built, maintained, evolved and deployed by a different team
- Each service has its own release cycle 
- Each service expose a well-defined set of APIs

Building a Service today with REST endpoints is a fairly easy task if you are using one of these frameworks previously mentioned. You have an annotation-based programming model that allows you to map Java Methods to REST endpoints and advanced Serialization/Deserialization mechanisms that will deal with all the boilerplate of parsing HTTP requests. 

>  **TIP:** For more details regarding microservices architecture, refer to the [Microservices]() chapter #TODO on review phase: add reference to https://github.com/otaviojava/manual-arquiteto-moderno/blob/master/chapter_05.md

The real problem arises when you start having more than a handful of services. Running each service in its own JVM will push you to run each service on a different port and take care of issues when these JVMs crash. For that reason, the industry quickly jumped to containers around 2015. 


### Containers & Containers Orchestration

Following the same line of going dependency-free, like the Java frameworks mentioned in the previous section, Containers help us to run our software anywhere. This means that we go one step further and now we don’t even want to depend on the Java Virtual Machine being installed in our host machine where the services will run. 

Docker (a container runtime) helped us with that. We can encapsulate our standalone JAR along with the JVM and configuration files to make it a container that can be run anywhere where the Docker Container Runtime is installed. 

Each Docker container has its own isolated runtime. Enabling us to isolate failures at the Docker container boundaries. 

When you have one Bounded Context and a few Services, you are probably fine just running these Java Microservices using a script (or docker-compose). However, even for everyday development practices, you will notice that each of these services will require a dedicated port and you will need to keep track of these ports to make sure that they are available for each of the services. 

When the number of services grows, this becomes unmanageable. 
For that reason, Container Orchestrators have become popular in the last few years, and  Kubernetes is leading the way. Kubernetes is in charge of dealing with how to create these container runtimes, how to scale them when there is load, and how to deal with containers that are misbehaving or crashing. 

>  **TIP:** For more details regarding containers and orchestration tools, refer to [Cloud]() chapter #TODO on review phase: add reference to https://github.com/otaviojava/manual-arquiteto-moderno/blob/master/chapter_07.md

Kubernetes success is based on the fact that each major cloud provider provides a managed Kubernetes Service, making it the defacto standard for multi-cloud support. In other words, no matter which Cloud Provider you choose, you can always trust that there will be a Kubernetes API exposed for you to interact and provision your services. 


### Capitalizing on DDD benefits

If you follow the DDD path, you need to take advantage on some of the promises that DDD gave you to start with and make sure that you are ripping the benefits. If we cannot continuously deliver new versions of our services without stopping the entire application, we are just making our life more complicated for nothing. If we are not delivering concrete business value as a result of following DDD, all the changes suggested in this chapter are not worth the effort or the time. 

I recommend the following article: [“The Business Value of using DDD”](https://www.informit.com/articles/article.aspx?p=1944876&seqNum=4), which gives a high-level overview of the benefits of adopting DDD, not for you as a developer, but for your business. 

The next section covers an example that I’ve created based on my experience while rearchitecting Java monolithic applications to a more distributed approach. The example is fictional, any similarity with reality is just a coincidence :) We encourage you to abstract the concepts and patterns away from the example scenario and map them to your own domain. At the end of the day, this is just an example, albeit a complex and fully functional one.


## Evolving your monolith on practice, using DDD

This section covers an example scenario that helps us to explain some of the concepts in action. You can map these concepts to your business domain and copy the actual technical solution from the example for some of the challenges presented. 

As expected, creating a full-blown application is hard work and usually requires a lot of time investment. For that reason, the following example is provided as a set of Open Source repositories where you can contribute to making it better. 

- [From Monolith to K8s Github Repository](https://github.com/salaboy/from-monolith-to-k8s)

> **TIP:** As a real application, the example will evolve in time, adding more tools and best practices, so we invite you to participate in this journey where we can all learn and share valuable information together. 

We begin our journey with a monolith Java application. The scenario that we will cover belongs to a company that is in charge of providing a platform to create Conference websites. Imagine that each of our customers requires us to host and scale their Conference website. 
We all have seen big Java Web applications and in this scenario, the application looks like this: 

![chapter_02_01](images/chapter_02_01a.png)

The “Customer Management” Facade is in charge of isolating different customers from each other. In some companies, this is defined as a multi-tenant platform or application. Unfortunately, this is quite common in the Java space. For historical reasons, implementations ended up growing into big, scary monoliths that came with a lot of scalability issues as well as data and traffic isolation challenges. No matter how fancy our platform looks, it is just running in a single JVM. You have no way of scaling each individual customer - you scale all or nothing. 

As you can see in the red box, each Conference Site will contain a bunch of modules depending on each customer selection, but in reality, at runtime, all the code will be there for every single conference. 

> Please note that if you have a platform like this and it does the work that it is supposed to do,you **should NOT** change it. Unless you are having problems managing this “platform” or scaling it, you shouldn’t rearchitect the whole thing.

Now this monolith architecture has some clear drawbacks, and for this scenario, we can consider the following reason to rearchitect into a proper cloud-native platform:

- Customers cannot be scaled independently
- Customer traffic is all handled by the same application
- Single Point of Failure in the JVM
- If data is stored in a database, data is shared across customers. The application code needs to deal with isolating each customer data. The database becomes a bottleneck as well, as all customer data is in the same DB. 
- Every change into the platform requires the entire application to be restarted
- Every developer involved with the application works against the same code base, making a release and merging features a major task with a lot of risks involved. This usually can be done by someone who understands the entire application.

>  **TIP:**  If you already have this application up and running and you have customers using the platform, you will have a good understanding of which features are essential and how you can start re-architecting it. 

As Martin Fowler describes in the linked blog post [Monolith First](https://martinfowler.com/bliki/MonolithFirst.html) is the way to go. By having a monolith you already understand the solution that you need to build, making it easy to estimate how the new architecture will tackle the problems of the existing version. In other words, if you don’t have an existing monolith, do not start with a distributed architecture from scratch. Create a monolith first and then split if needed. 

The next step in our journey is to decide where to start. In my experience I’ve seen three common patterns repeating:

- **Start new functionalities as separated services**: this is usually recommended if you can afford to maintain the monolith as it is. New services will not solve the already existing problems, but it will help your developer teams to get used to working with a microservice mindset.
- **Split existing functionality out of the monolith** (and slowly deprecate the old code): if you have pressing issues with the monolith you can evaluate branching off some of the functionality outside into a new service. This might solve some of your existing problems, but it will not bring any business value immediately. It also adds to the complexity of the day-to-day operations as you might end up running two solutions for the same problem over a long period of time. This can also be used to understand how complex and costly a core rearchitecture can be. 
- **Rearchitect the core of the platform as microservices** (to tackle existing problems): Sooner or later, if you are experiencing problems maintaining and scaling your Monolith, you will need to rethink and redesign the core bits of your platform, making sure that you focus on solving the current scalability and maintenance problems. This can be a costly initiative, but it can be done in complete isolation from your production environments. Several times I’ve seen how this is done as a Proof of Concept to demonstrate that is actually possible and also to make sure that your team members understand the implications from a business (advantages) and technical point of view (new tech stack, new tools).

In this chapter, I will cover the last of these options (**Rearchitect the core of the platform as microservices**) to highlight the solution for our existing problems with the monolith application, but you can explore the other two if they are more appropriate for your situation. 

>  **TIP:** More information regarding strategies and practices on how to migrate an existing monolith to microservices architecture can be found in the [Microservices]() chapter #TODO on review phase: add reference to https://github.com/otaviojava/manual-arquiteto-moderno/blob/master/chapter_05.md

This is where DDD concepts and patterns become really handy to define how to split the functionalities of the monolith and how to organize our teams around the new services. In the following sections, we will explore some of these concepts in action.


### Assumptions and Simplifications

Platforms are complex beasts. Trying to re-architect something big will give you more headaches than solutions. For that reason, you should try to simplify the scope of the problem to tackle different challenges in a controlled way. 

For our scenario, this might mean that instead of trying to go for the re-architecture of the entire platform, first, we try to solve the architecture and shape of a simple Conference Site. This means that instead of focusing on the platform, we first focus on what the customer will expect for their Conference Site. By understanding the shape of a single Conference Site you and your teams have a well-defined set of challenges to solve that can bring immediate business value. Later we automate how each of these Conference Sites can be provisioned. 

From an architectural point of view, this might mean a monolith Conference Site or a Conference Site that is built with different distributed services. If Conference Sites are complex enough and we want to reuse modules for all of them we might consider a distributed approach. Based on my experience, this kind of platform does leverage shared services most of the time so it makes sense to architect them with reusability in mind. 

> **TIP**: This strategy leads to having multiple services from day one, so this is something that you and your teams should get used to.

Our independent Conference Sites will look like this: 
![chapter_02_02](images/chapter_02_02.png)

As you can see in the previous diagram, it is quite clear that there are important architectural changes. It is quite common to have the User Interface, in this case, the “Conference Site” box separated from core services. Most of the time, this user-facing component will act as a router, routing requests from the Conference  Site to services that are not directly exposed to users. 

But before digging into technicalities, how do we prioritize and make sure that we start on the right foot? How do we scope these services correctly (not too macro, not too micro)? DDD concepts can help us, providing some answers and guidelines. 


### Bounded Contexts to start splitting your Monolith 

If you are planning a rearchitecture for the core of your platform you need to be careful and you need to choose wisely which components will be re-architected first and why. 
In my experience, you should try to tackle core components first. Choose components you understand and that you know that provide value to the business where you are. 

In the case of our scenario, dealing with Call for Proposals is critical to bootstrap a conference. 
There is substantial work around accepting proposals, reviewing them and making sure that the conference has an interesting agenda. 

If we are confident that implementing the Call For Proposal functionality first will give us immediate wins, we need to estimate how big and how complex the effort is. 

DDD proposes the concept of Bounded Context as a well-scoped set of functionalities that belong together. These functionalities usually map how a Domain Expert (Subject Matter Expert) will do the work if there is no software available. In order to plan, design, and implement these functionalities, a team is assembled with Domain Experts that will work hand in hand with Software Engineers. Most of the time, a Domain Expert will know what Bounded Contexts already exist and what are they responsible for. 

> **INFO:** From a DDD perspective, it is extremely important to not make up these Bounded Contexts a technical boundary that is imposed on Domain Experts. 

A Bounded Context will expose a well-defined set of APIs so other Teams from different Bounded Contexts can consume and interact with the Call For Proposals functionality. 

The Call for Proposals Bounded Context will enable a team to implement all the needed functionality independently of other teams. This team will be in charge of the entire lifecycle of the project, from designing it, implementing it, running it for the rest of the company and for your customers to consume it. 

As soon as you start designing the Call for Proposal functionality you realize that you will need to consume and interact with other teams. Very early on the following Bounded Contexts are identified:

![chapter_02_03](images/chapter_02_03.png)

Each of these Bounded Contexts should be owned by different teams and we need to make sure that they have enough autonomy to make progress, create new versions with new features, and deploy concrete software components to our customer’s environments. 

On a practical side, each Bounded Context will be implemented as one or a set of services that implement the context features. There is no need to be strict about the number of services that will compose a Bounded Context, but usually, there is one that is in charge of exposing a set of APIs to the outside world. 

For the purpose of this example, a single service will implement all the Call For Proposals Bounded Context logic and the team behind this service will be responsible for designing its APIs, choosing the frameworks that they are going to use, and actually deploying it into a live environment. 

Going deep into practical details, there are a couple of best practices shared by many companies and tools: 
- One Repository /  One Service + Continuous Delivery
- Open APIs


#### One Repository / One Service + Continuous Delivery

It is usually recommended to keep all the technical resources needed to run a service close to the source code. This facilitates the maintenance and the cognitive load of understanding how to run our Services in an actual environment. 

Nowadays, it is quite common to have a service, let’s say written in Spring Boot versioned in a git provider such as GitHub, where we can find a Dockerfile to create a containerized version of our service that can be run anywhere where a Docker daemon is present. 

With the rise in popularity of Kubernetes, it is also common to find Kubernetes Manifest (YAML files) describing how our Service can be deployed to a Kubernetes Cluster. Finally, we tend to use Continuous Integration pipelines to actually build all these software components, so it is quite common to also find the pipeline definition close to the source that needs to be built. 

You, as a developer targeting Kubernetes as your deployment platform, are now responsible for a bunch of artifacts, not just your Java Service source code. 

![](images/chapter_02_04.png)

In order to deploy your code to Kubernetes you will need to: 
- Build and test your source code, if it is Java you can use, for example, Maven or Gradle to do that;

- That will result in a JAR file that you might want to push to a repository such as Nexus or Artifactory. This JAR file will already have a version in it and if you are using Maven or Gradle this JAR will be identified by its GAV (Group/Artifact/Version).

- From that JAR you create a Docker Image by defining a Dockerfile that understands how to run your Java application with a provided JVM.

- Finally, if you want to deploy your container to a Kubernetes Cluster you will need some YAML manifests

- Optionally, if you are building a lot of services you might want to use Helm to package and release these YAML files. All these artifacts need to be versioned accordingly, meaning that when you build a new version of your JAR file, a new container needs to be built and a new Helm Chart needs to be released. 

  > **INFO:** Helm provides the idea of Charts (Packages) that map one to one with how we deal with our Maven artifacts. If you are working with Helm Charts, these charts are usually also pushed/released to a chart repository such as Chart Museum.  

At this point if you are thinking ‘that is a lot of work,’ you are 100% right. If you are thinking, ‘I don’t want to do all of that,’ you are absolutely right. I don’t want to do that either. If you want this to work, you need to use specialized tools that already deliver all this functionality in an automated way. You should aim for automating every step, and the industry uses Continuous Integration pipelines to achieve this. Let's talk about delivering pipelines with Jenkins X.

> **INFO:** In this example we are targeting Kubernetes, and we opt to use Jenkins X in this toolchain. Jenkins X, brings CI/CD to Kubernetes and it is part of the Continuous Delivery Foundation.

As you might notice, Jenkins X is not only about Continuous Integration but also about Continuous Delivery. By covering Continuous Delivery, the pipeline doesn’t stop when these components are built. The pipeline is in charge of building, testing, and also deploying our artifacts into a live environment where they will run to serve our customers. The “continuous” part makes reference to the fact that you want to make sure deploying a new version of your service is frictionless and you will aim to deploy new versions in a short period of time. 

>  **INFO:** In order to achieve Continuous Delivery, Jenkins X uses a set of conventions to enable developers to focus on building business value. These conventions are not exclusive to Jenkins X and are part of best practices gathered from different industries and practitioners. Projects like Jenkins X are the catalysts for thousands of community members, who are experts in the CI/CD space, which creates best practices and tools that apply them.

One of the conventions used by Jenkins X is called “Trunk Based Development”. It basically means that every change applied (merged) to the master branch will generate a new release of our artifacts. Most of the time, this is not comfortable for developers, as most of these practices are commonly defined in each company and they tend to vary quite a lot. The key motivation to use something like Trunk Based Development is to make sure that teams don’t spend time defining these practices.  When working with this convention you are enabled to focus on writing code and, when your code is done and merged in master, a new release is created and deployed to some kind of staging environment for further validations. 

> **TIP:** I strongly recommend if you are starting a new project, to check the advantages of Trunk Based Development as well as the book Accelerate as it was used as the basis to create tools like Jenkins X. https://jenkins-x.io/about/overview/accelerate/

At the end of the day, Jenkins X uses both conventions, “One Repository / One Service” plus “Trunk Based Development”, to take your service from source code to a running instance inside a Kubernetes Cluster. 

![chapter_02_05](images/chapter_02_05.png)

In our example, the following links demonstrate all these concepts in action 

- Pipeline: https://github.com/salaboy/fmtok8s-email/blob/master/jenkins-x.yml 
- DockerFile: https://github.com/salaboy/fmtok8s-email/blob/master/Dockerfile
- Helm Charts: https://github.com/salaboy/fmtok8s-email/tree/master/charts/fmtok8s-email
- Continuous Releases: https://github.com/salaboy/fmtok8s-email/releases

You can find the same setup for all the projects inside the Conference Site Demo. 


#### Open APIs
If you are already implementing a Bounded Context, very early one you will need to design and specify what kind of interface are you going to expose to other Bounded Context and third party services that might be interested in the functionality that your context provides. A popular way of implementing these APIs are REST endpoints. 
(errinhos)

> **INFO:** Since you are probably familiar with REST endpoints already this section is focused on the [Open API Specification](https://github.com/OAI/OpenAPI-Specification )

As defined in the Spec text “*the OpenAPI Specification removes the guesswork in calling a service.*” Nowadays popular frameworks such as Spring Boot comes with out of the box integration with Open API and Open API tooling. 

By just adding a Spring Boot extension/starter, you enable your application to expose a user interface that serves as live documentation and browser of your APIs.

If you are using standard (Tomcat) Spring Boot you need to add to your `pom.xml` file: 

```xml
<dependency>
   <groupId>org.springdoc</groupId>
   <artifactId>springdoc-openapi-ui</artifactId>
   <version>${springdoc-openapi-ui.version}</version>
</dependency>
```
If you are using Webflux, the reactive stack you need to add: 
```xml
<dependency>
   <groupId>org.springdoc</groupId>
   <artifactId>springdoc-openapi-webflux-ui</artifactId>
   <version>${springdoc-openapi-ui.version}</version>
</dependency>
```

- https://github.com/salaboy/fmtok8s-email/blob/master/pom.xml#L40 

In real-life projects, these user interfaces and API specification documents can be used by other teams to understand with concrete details about how to interact with your services. The sooner that you get an API exposed, the sooner that other teams can start leveraging your service. 

The following screenshot shows the Open API User Interface that is provided by just including the previous dependency. This screen can be accessed by pointing your browser to host:port/swagger-ui.html and it provides a simple client to interact with your services, understand which endpoints are exposed and which data these endpoints expect and return. 

![chapter_02_06](images/chapter_02_06.png)

Feel free to clone one of the services from this example and run it with `mvn spring-boot:run` command to explore each service APIs definitions. By default, each service will start in port 8080 so you should point your browser at http://localhost:8080/swagger-ui.html

### Context Map to understand Team and Technical interactions

Bounded Contexts are great to understand a well-scoped set of functionalities that need to be provided together. When we have several of these contexts, we need to understand how they will interact with each other and their relationships. That is where the concept of Context Maps really helps. With Context Maps, you can map out the relationships between bounded contexts and what they will actually need to interact. Context mapping also gives you visibility about how the teams responsible for each Bounded Context will interact with other teams.

On the practical side, this is all about System Integrations. How our services or the services that expose some kind of APIs talk to each other. How do they transform and move data around and how do they know which services are available to consume. 

As you might guess, APIs are extremely important, but understanding who is going to consume our APIs, what is expected from these APIs and who actually depends on us is just as critical. 

Well-defined Context Maps help a lot to plan and understand how these “isolated" Bounded Context and teams working on them will interact on a day to day basis. 

For our example the following context map would make sense:
![chapter_02_07](images/chapter_02_07.png)

This diagram depicts the relationships between the simple Bounded Context that we have for our Conference Site application. Here we can see that there is a **Customer/Supplier** relationship between Call for Proposals and the Conference Agenda Bounded Context. Where Call for Proposals **is a consumer** of the upstream service Conference Agenda. Between these two teams, there is a **Partnership** relationship as well, as they need to collaborate to get things done. This means that the communication between these two teams is important and they should be able to influence each other’s roadmap. 

On the other hand, the relationship with the Notification service is different. Call For Proposals has an upstream relationship with the Notification Bounded context, but it will **comfort** with their contracts. This means that from the Call for Proposals team perspective they cannot influence or change the Notification Bounded Context APIs. This happens a lot when we have legacy systems or when this bounded context is external to our company. 

> **TIP:** Jumping on the practical side, while System integrations is a very broad topic, this section focuses on a very practical recommendation: “You must learn about Consumer-Driven Contact Testing”. Once again Martin Fowler has an article, published in 2006, about this: https://martinfowler.com/articles/consumerDrivenContracts.html.

While the topic itself is not new, there are very up to date tools to actually implement this in your projects, such as [Spring Cloud Contracts](https://spring.io/projects/spring-cloud-contract). 

#### Bonus: Implementing Contracts with Spring Cloud Contracts

With Spring Cloud Contracts, the story goes like this, first you define a contact for your APIs, this basically means what kind of request the consumer should submit and what kind of response we need to provide as a service. 

A contract looks like this: https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/src/test/resources/contracts/shouldAcceptPostProposal.groovy

```groovy
Contract.make {
       name "should accept POST with new Proposal"
       request{
           method 'POST'
           url '/'
           body([
               "title": $(anyNonEmptyString()),
               "description": $(anyNonEmptyString()),
               "author": $(anyNonEmptyString()),
               "email": $(anyEmail())
           ])
           headers {
               contentType('application/json')
           }
       }
       response {
           status OK()
           headers {
               contentType('application/json')
           }
           body(
                   "id": $(anyUuid()),
                   "title": $(anyNonEmptyString()),
                   "description": $(anyNonEmptyString()),
                   "author": $(anyNonEmptyString()),
                   "email": $(anyEmail())
           )
       }
   }
```
This contract defines the interaction for submitting a new Proposal to the Call for Proposal Service. As you can see, it involves a `POST` request and a body with some predefined properties including a `header` with a very specific `Content Type`. This contract also defines that the return for the consumer will add to the information sent an `id` property with a `UUID` format. 

Now, this contract can be used to generate a test to actually test that your service is working as expected from a consumer point of view. So, if you have defined any contract in your project when you build and test your project, the contracts will be executed against a real instance of your service. This enables us to make sure that we break the build if a contract was broken. In order to automatically create and execute these tests you only need to add a dependency and a plugin to your maven project:
https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/pom.xml#L50

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-contract-verifier</artifactId>
  <version>${spring.cloud.contract}</version>
  <scope>test</scope>
</dependency>

```
And in the `<build><plugins>` section the following plugin:
https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/pom.xml#L88

```xml
<plugin>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-contract-maven-plugin</artifactId>
  <version>${spring.cloud.contract}</version>
  <extensions>true</extensions>
  <configuration>
    <packageWithBaseClasses>com.salaboy.conferences.c4p</packageWithBaseClasses>
    <testMode>EXPLICIT</testMode>
   </configuration>
</plugin>

```

Finally, depending on the shape of the service that you will be testing some confirmation might be needed: https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/src/test/java/com/salaboy/conferences/c4p/ContractVerifierBase.java#L16

```java
@RunWith(SpringRunner.class)
@SpringBootTest( webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT,
        properties = "server.port=0")
public abstract class ContractVerifierBase {

    @LocalServerPort
    int port;
    
    ...
}
```

If you need to add any initialization code, this is the class that all the autogenerated test should inherit. 

Now that the contract is validated with every build, we can also use the contract to generate a Stub, which is a Service that behaves like the actual service but with mock data. The mock data is also automatically generated, as it can be provided by the contract definition as well. This Stub is an artifact on its own that can be distributed to other services, for example, the ones consuming the “real” service for testing.

This basically means that now, every time that you build your service **two** JAR files are going to be created. One is the actual Spring Boot application JAR and the other one is the Service Stub. 
This Service Stub can be automatically pushed to your artifactory repository (e.g. Nexus or Artifactory) and it will live under the same group and artifact name as your application JAR. 

Finally, a Service X that is designed to consume your service can create tests that will start the previously generated Stub locally to avoid requiring an actual instance or an entire environment setup. You can easily start the Stub before your tests by using the following annotations:


```java
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)
@AutoConfigureMockMvc
@AutoConfigureJsonTesters
@AutoConfigureStubRunner(stubsMode = StubRunnerProperties.StubsMode.REMOTE, repositoryRoot = "<your nexus repository>", ids = "com.salaboy.conferences:fmtok8s-c4p")
public class C4PApisTests {
   @StubRunnerPort("fmtok8s-c4p")
    int producerPort;

...
}
```

https://github.com/salaboy/fmtok8s-api-gateway/blob/master/src/test/java/com/salaboy/conferences/site/C4PApisTests.java#L29

This automatically downloads the latest version of the stub and runs it before your test starts using a random port that you can obtain via injection using `@StubRunnerPort`. 

It is important to notice that both the Service and the contracts are versioned together as part of the same code base. This implies that the generated Stub and the Service itself will have the same version. A consumer service, to run its tests can depend on the Stub, as it should never depend on the service itself. As soon as the consumer has tested using the producer Service Stub, you can quickly recognize when a contract is broken or when a new version of the contract is no longer supported by consumers, as the tests using the Stubs will break when new and incompatible versions are released. At the point in time, the consumers are faced with a simple decision, to stay depending on the old contracts with a fixed version, or to update to the latest version of the contract. As you can see, this might require you to run several versions of your service at the same time, luckily for us, Kubernetes is built to support these scenarios. You might want to read about Canary Releases if you are interested in aspects of multi-version deployments.

> **TIP:** The [Cloud]() chapter covers Canary Releases as well as other deployment strategies. #TODO on review phase: add reference to https://github.com/otaviojava/manual-arquiteto-moderno/blob/master/chapter_07.md

Both Bounded Contexts and Context Maps are great conceptual tools to understand how to structure your teams and your software, but more importantly, these concepts help you to focus on business value. 


### Focus on Business Value

When working on real applications, you need to focus on what is going to bring more value and solve more problems for your business. As a very practical person, I tend to analyze use cases and how Bounded Context in conjunction with Context Maps provides a business flow end to end. 

For this particular example, the conference scenario, we are looking at the Call for Proposals basic flow, which looks like this:

1. Potential Speaker Submits a proposal via the conference website
2. Board/Committee review the proposal
3. If the proposal gets accepted
   1. A new agenda item is created and published into the Agenda Page
4. A notification is sent via email for Accepted and Rejected proposals

You need to pay attention to your human/people interactions as these interactions tend to require asynchronous behaviors such as reminders, notifications, alerts as well as User Interfaces which will need to be carefully designed. 

> **TIP:** As engineers, we tend to oversimplify and underestimate the amount of work and iterations that crafting a good user experience might take.

The User Interface that covers this simple scenario looks like this: 

* The main page inside the Conference Site displays the Agenda divided by days. The items inside the agenda are the ones that are already confirmed and were approved by the committee.

  ![chapter_02_08](images/chapter_02_08.png)

* The main page also allow potential speakers to submit proposals by filling up a form:

  ![chapter_02_09](images/chapter_02_09.png)

* Once the proposal is submitted the potential speaker will need to wait for Approval or Rejection by the committee. The committee members have a back-office page where they can Approve or Reject each submitted proposal: 

  ![chapter_02_10](images/chapter_02_10.png)

* The back-office page also offers Board members the option to send email notifications to the potential speakers.

  ![chapter_02_11](images/chapter_02_11.png)



Once again, you can notice the simplification of this scenario on purpose to establish a base set of functionality, quickly iterate and get it working and then expand the requirements. 

#### Architecture and Services 

From an architectural perspective it might look more like this: 
![chapter_01_12](images/chapter_01_12.png)

Where the User Interface with some routing capability is required to forward requests to the Call for Proposals Service (C4P) to the Agenda or Emails Service.  In this example, all the communications happen via HTTP/Rest invocations. 

### API Gateway / User Interface
Most of the time, an API Gateway is also used to hide all the other services from direct access. It is quite common to see this service delegating Authorization and Authentication to an OAuth or SAML provider, serving as a security fence to the outside world. The example uses the [Spring Cloud Gateway](https://spring.io/projects/spring-cloud-gateway) which provides the routing mechanism to forward inbound requests to the rest of the services. Spring Cloud Gateway allows us to transform any Spring Boot application into a request router with advanced capabilities. 

> **INFO:** It is important to notice that Spring Cloud Gateway gives you the flexibility to actually add programmatically any transformation that you want/need into the incoming requests. This power and freedom come with the drawback that then it is up to you to maintain, test, and bug fix. In large projects, you might want to evaluate a third-party API gateway (such as Kong, 3Scale, Apigee, etc) based on your project requirements. 

The API Gateway / User interface module can be found in this repository: https://github.com/salaboy/fmtok8s-api-gateway

Which adds to its maven dependencies:

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
```
And configure the default routes for our services inside the application.yaml file: 
https://github.com/salaboy/fmtok8s-api-gateway/blob/master/src/main/resources/application.yaml#L4

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: c4p
        uri: ${C4P_SERVICE:http://fmtok8s-c4p}
        predicates:
        - Path=/c4p/**
        filters:
          - RewritePath=/c4p/(?<id>.*), /$\{id}
      - id: email
        uri: ${EMAIL_SERVICE:http://fmtok8s-email}
        predicates:
        - Path=/email/**
        filters:
          - RewritePath=/email/(?<id>.*), /$\{id}
```

These routes define a path into the gateway such as `/c4p/**` will automatically forward the request to the http://fmtok8s-c4p service.

> **INFO:** The user interface for the website can be found here: https://github.com/salaboy/fmtok8s-api-gateway/tree/master/src/main/resources/templates 
> The Controller which fetches the data from the backend services here: https://github.com/salaboy/fmtok8s-api-gateway/blob/master/src/main/java/com/salaboy/conferences/site/DemoApplication.java

Because we are running in Kubernetes, we can use the name of the Kubernetes Service instead of point to a specific Pod. This routing mechanism enable us to only expose the API Gateway Endpoints to the outside world, leaving behind a secure network all the other services.

### Domain Events and the Call for Proposals Service
Because the flow under analysis is core to the Call For Proposals Bounded Context it is no surprise that the core logic belongs to the Call For Proposal Service, more concretely to the following two pieces of functionality: Proposal Submission and Decision Made By the Board. 

> **INFO:** The Call for Proposals Service can be found here:  https://github.com/salaboy/fmtok8s-c4p/

The Proposal Submission endpoint accepts a proposal from the User Interface and stores it in a Database or storage. This is an important step, we need to make sure that we don’t loose proposals. Notice that we might be interested in emitting a DDD Domain Event at this point as other systems/applications might be interested to react every time that a proposal is received. 

> **INFO:** Check an actual implementation here: https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/src/main/java/com/salaboy/conferences/c4p/C4PController.java#L37

More importantly, Decision Made by the Board endpoint records a decision made by the board, but it also defines the following steps based on that decision. In real life, this decision will affect the course of action. Most of the time, these decision points and the actions derived by them are critical to run a cost-effective and efficient business. 
https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/src/main/java/com/salaboy/conferences/c4p/C4PController.java#L60

```java
@PostMapping(value = "/{id}/decision")
    public void decide(@PathVariable("id") String id, @RequestBody ProposalDecision decision) {
        emitEvent("> Proposal Approved Event ( " + ((decision.isApproved()) ? "Approved" : "Rejected") + ")");
        Optional<Proposal> proposalOptional = proposalStorageService.getProposalById(id);
        if (proposalOptional.isPresent()) {
            Proposal proposal = proposalOptional.get();

            // Apply Decision to Proposal
            proposal.setApproved(decision.isApproved());
            proposal.setStatus(ProposalStatus.DECIDED);
            proposalStorageService.add(proposal);

//          Only if it is Approved create a new Agenda Item into the Agenda Service
            if (decision.isApproved()) {
                agendaService.createAgendaItem(proposal);
            }

            // Notify Potential Speaker By Email
            emailService.notifySpeakerByEmail(decision, proposal);
        } else {
            emitEvent(" Proposal Not Found Event (" + id + ")");
        }

    }
```

As you can probably guess, this method contains the logic of the entire flow. It starts by checking if the proposal `Id` can be found, if it is present it will apply the decision to the proposal object (`Approved` or `Rejected`) and then only if it was approved call the Agenda Service to create a new Agenda Item. No matter if it was accepted or not, an email needs to be sent to the potential speaker to notify him/her about the decision.  Both [agendaService](https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/src/main/java/com/salaboy/conferences/c4p/services/AgendaService.java) and [emailService](https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/src/main/java/com/salaboy/conferences/c4p/services/EmailService.java) are simply encapsulating simple REST calls. 

This simple method example, in real-life scenarios is never simple. Because of the inherent complexity of real-life challenges, you must expect simple methods like the one discussed above to become real monsters. The next section covers some of the major pitfalls and considerations that you must have in mind while writing business logic that is key to your domain. The next section also tries to share valuable resources, approaches, and projects which can help you to make better decisions early on to avoid common mistakes.

### Common Pitfalls
Real-life applications are complex, and that complexity tends to come from the inherent complexity of the problems that we are trying to solve. We can try to reduce this complexity by using a continuous improvement approach and by making sure that we are not reinventing unnecessary wheels that are not providing any business differentiation. 

Let’s start with something that you might have faced in the past: REST to REST communications can be challenging. 

#### REST to REST communications can be challenging
If you look at the example provided in the previous section both the Agenda and Email services are invoked from the Call for Proposals Service by using a [REST call](https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/src/main/java/com/salaboy/conferences/c4p/services/AgendaService.java#L29). As we discussed before, these interactions represent a key part of our business flow, so you need to make sure that these interactions go as planned 100% of the times. It is vital that our application doesn’t end up in an inconsistent state, for example, a proposal gets approved and published into the agenda, but we never sent a notification to the speaker. As it is coded in this example, if the Agenda or Email Service goes down, the HTTP request will silently fail.

This basic requirement, when working with distributed systems where there is no shared state between services becomes a challenge that has several possible solutions. 

One common solution is to write inside our service code to retry in case of failure, this makes every call much more complicated. In the past, there have been several libraries that provided helpers for such situations. It is important to notice that vanilla Kubernetes doesn’t deal with these kind of failure in any way. 

Another solution might be to use a messaging or pub/sub mechanism to communicate our services that out of the box provide you with more guarantees about the delivery of the messages sent. Switching to using messaging such as RabbitMQ or Kafka introduces another set of challenges, most of the time related with now dealing with another complex piece of infrastructure that needs to be maintained over time. Nonetheless, messaging has been proven to be robust and the only option for certain scenarios where these guarantees are required, and the volume of interactions is high. 

Finally, a newer approach are Service Meshes, where we delegate the responsibility of retrying, for example,  to the infrastructure. Service Meshes uses proxies to inspect HTTP payloads and error codes so automatic retries can be don in case of failure.
(corrections)

> **TIP:** You should check out Istio, Gloo, and LinkerD if you are interested in understanding more about how Service Meshes work and what are their advantages. More details about Service Meshes are shared on the Cloud chapter.  #TODO on review phase: add reference to https://github.com/otaviojava/manual-arquiteto-moderno/blob/master/chapter_07.md

#### Flow Buried in Code
It is quite common to find complex business logic hidden inside our services, obscured in a way by all the boilerplate required to deal with technical errors, fetch data from different sources and transform data between different formats. In real-life projects, it gets quite hard for Domain Experts to actually understand the code that implements their business flows. 

The example discussed in this chapter would become hard to read if we add the code to deal with other aspects such as: 
- Unhappy paths and exceptional cases: such as, the speaker that submitted a proposal disappeared and it is not answering any emails.
- Time-Based events, reminders and constraints: for example, schedule a reminder for the board members to review a proposal before 3 days after the submission happened. Cancel the reminder if the decision was made before the deadline. 
- New requirements are added which push developers to change the sequence of the flow: such as, an email that needs to be sent with a confirmation link to the speaker before we publish and approved talk to the agenda. The more requirements the more code that we need to add, the more it looks like spaghetti. 
- Reporting and Analytics: for example, your manager wants to know how many proposals we receive per day and how much time on average it takes to approve or reject proposals. You might be tempted to add new endpoints to deal with such reports inside the same service. 

There are no silver bullets to tackle these challenges but I wanted to mention a couple of things that might help in your scenarios to reduce complexity and provide visibility about how your services are working. 

Domain Events are introduced in DDD to externalize the application’s state that might be relevant for other services to consume. On a practical side, these events can be created using [Cloud Events](cloudevents.io) which provides a transport-agnostic format for events to be exchanged. 

Service Orchestration tools can be used in conjunction with Cloud Events to externalize the buried business logic inside our services.

> **TIP:** Check projects such as Zeebe, jBPM or Kogito to understand more about how these tools can help you out. Also, you might find this  blog post about Cloud Events very useful. 

Finally, as discussed in the book *Implementing DDD*, patterns such as CQRS (Command/Query Responsibility Segregation) can help you out a lot when dealing with reports and analytics. You want to avoid running expensive reports or intense data crunching routines on top of your service database. By applying CQRS you externalize the data that you are interested in reporting on into a separate store that has an optimized format for indexing, searching, and summarizing data. A popular approach is to send data to ElasticSearch for full-text indexing. Then in your application, if you want to search between thousands of proposals you don’t query the Call For Proposal Service, instead, you use ElasticSearch indexes, offloading your Call for Proposal service, so it can continue accepting valuable proposals for your conferences. 

#### Adapters for Legacy Systems

A short note on Legacy Systems, try to abstract them away so you have control on top of their APIs. For the example covered in this chapter, a service called Email was introduced to expose via HTTP endpoints the functionality of an Email Server. This was done on purpose to highlight the advantages of providing an adapter to a Server that we cannot change (an SMTP server). In this adapter, we can include helper functions, templates, and domain-specific functionality that is required by our use cases. 

The Email Service provided doesn’t include an SMTP connection but it exposes a set of APIs that are easy to consume and don’t require other services to include SMTP clients. 

> **INFO:** The source code for this service can be found here: https://github.com/salaboy/fmtok8s-email

Consider creating Adapters for your legacy Services, remember that inside Kubernetes even if the adapters are created in separate containers, these containers can be run inside the same host, avoiding an extra network hop. 

## Sum up

This chapter has covered a wide range of tools, principles with reference to an example application. Some of these tools will make sense to your scenario, some of them will not. What is important to take away from here can be summarized in the following points.

- Optimize decisions around building or integrating third-party software to solve specific or cross-cutting concerns. Having a clear process to evaluate tools against building in house software for challenges that are not core to your business is key to gain agility. 

- Bring your teams up to speed with training. Knowledge transfer is a big problem when the technology stack is broad and complex. Learn to identify what are the main topics that your teams struggle with and find training that can help to spread knowledge across your teams.  

- Use conventions over in-house definition, tap into open source communities. Open Source communities are great places to find best practices applied, innovations, and trends. Don’t be afraid to participate, get involved, and share your learnings. 

- Consider using SaaS providers instead of hosting in-house when possible. If you are already running in a Cloud Provider you need to seriously consider the set of services that they offer. Cloud Providers and SaaS offerings will save you valuable time when setting up and maintaining key pieces of your infrastructure. Because a developer can run Kafka, ElasticSearch or any other third party tool using containers doesn’t mean that they are willing to maintain, upgrade, and backup these services for the entire company. 



PT

# Tenho lido sobre DDD, para onde devo ir a seguir?

Você já codifica Java há muitos anos, já leu sobre Domain-Driven Design (DDD) e deseja aplicar isso a um projeto da vida real. Como isso funciona? O que realmente significa aplicar DDD no ecossistema de hoje? Realmente vale a pena o tempo investido?

O DDD nos fornece uma base teórica e padrões muito bons para construir um software robusto que agregue valor real ao negócio. Para fazer isso, o DDD ajuda a fornecer uma estrutura para organizar o software construído e otimizar equipes independentes que possam trabalhar juntas consumindo APIs bem definidas. No final das contas, o DDD fornece os princípios que você precisa para traduzir para Java ou qualquer outra linguagem de sua escolha. Mas, ao fazer isso, você precisará tomar algumas decisões difíceis sobre como esses componentes/serviços/aplicações Java serão executados e interagirão.

Este capítulo aborda o lado prático de como esses conceitos podem ser mapeados em um exemplo existente executado no Kubernetes, oferecendo dicas práticas sobre como os conceitos DDD podem ser mapeados para um stack tecnológico (pilha de tecnologia) concreto. Claro, existem milhares de opções diferentes para escolher hoje, mas você pode tomar isso como um exemplo de que tipo de coisas você precisará se atentar ao passar por esta jornada.

> ** INFO: ** é importante destacar que este capítulo não é sobre os conceitos básicos de DDD, portanto, se você é novo no DDD, os seguintes livros são recomendados:  Implementing DDD e DDD Distilled.

Este capítulo está dividido em duas seções principais:
- [Introdução aos tópicos relacionados a Java e nuvem] (# java-in-the-cloud)
- [De Monolith até K8s usando DDD] (# from-monolith-to-k8s-using-ddd)


## Java na nuvem

Existem muitos frameworks por aí com o objetivo de fornecer uma experiência mais fácil para os desenvolvedores que criam microsserviços. Exemplos disso são Spring Boot, Quarkus, Micronaut, Helidon, etc. Em geral, o objetivo desses frameworks é criar um arquivo JAR autônomo que possa ser executado, fornecendo a você um executável livre de dependências que requer apenas a Java Virtual Machine (JVM) para rodar.

Isso vai contra ao que nós (como comunidade Java) estávamos fazendo cinco anos atrás, e alguns de nós ainda fazem, que era fazer o deploy de nossas aplicações Java dentro de um servidor de aplicação ou um Servlet Container como o Tomcat.

Enquanto costumávamos ter um monólito com todos os recursos de nossas grandes aplicações integradas, agora buscamos ter um conjunto de serviços com funcionalidades bem definidas. Esses novos (micro) serviços compartilham as seguintes características:
- Tendem a localizar e versionar todos os artefatos que são necessários para ir do código-fonte até um serviço em execução em um ambiente.
- Cada serviço é construído, mantido, desenvolvido e implantado por uma equipe diferente.
- Cada serviço tem seu próprio ciclo de lançamento.
- Cada serviço expõe um conjunto bem definido de APIs.

Construir um serviço hoje com endpoints REST é uma tarefa bastante fácil se você estiver usando um desses frameworks mencionados anteriormente. Você tem um modelo de programação baseado em anotação que permite mapear métodos Java para terminais REST e mecanismos avançados de serialização/desserialização que lidarão com todo o boilerplate de análise de solicitações HTTP.

> ** DICA: ** Para mais detalhes sobre a arquitetura de microsserviços, consulte o capítulo [Microsserviços] () #TODO na fase de revisão: adicionar referência a https://github.com/otaviojava/manual-arquiteto-moderno/blob/ master / capítulo_05.md

O verdadeiro problema surge quando você começa a ter mais do que um punhado de serviços. A execução de cada serviço em sua própria JVM irá forçá-lo a executar cada serviço em uma porta diferente e cuidar dos problemas quando essas JVMs travarem. Por isso, a indústria saltou rapidamente para containers por volta de 2015.


### Containers & Organização de Containers

Seguindo a mesma linha de ficar livre de dependências, como os frameworks Java mencionados na seção anterior, containers nos ajudam a executar nosso software em qualquer lugar. Isso significa que vamos um passo adiante e agora não queremos nem mesmo depender da Java Virtual Machine sendo instalada em nossa máquina host onde os serviços serão executados.

Docker (um container runtime) nos ajudou com isso. Podemos encapsular nosso JAR autônomo junto com a JVM e os arquivos de configuração para torná-lo um container que possa ser executado em qualquer lugar onde o Docker Container Runtime estiver instalado.

Cada container Docker tem seu próprio tempo de execução isolado. Permitindo-nos isolar as falhas nos limites do container Docker.

Quando você tem um Bounded Context (contexto delimitado) e alguns Serviços, provavelmente está tudo bem apenas executar esses microsserviços Java usando um script (ou docker-compose). No entanto, mesmo para as práticas de desenvolvimento diárias, você notará que cada um desses serviços exigirá uma porta dedicada e será necessário manter o controle dessas portas para garantir que estejam disponíveis para cada um dos serviços.

Quando o número de serviços aumenta, isso se torna incontrolável.
Por esse motivo, os Container Orchestrators (orquestradores de container) se tornaram populares nos últimos anos, e o Kubernetes está liderando o caminho. O Kubernetes é responsável por lidar com a criação desses container runtimes, como escaloná-los quando há carga e como lidar com containers que apresentem mau comportamento ou falhem.

> ** DICA: ** Para obter mais detalhes sobre containers e ferramentas de orquestração, consulte [Cloud] () capítulo #TODO na fase de revisão: adicionar referência a https://github.com/otaviojava/manual-arquiteto-moderno/blob /master/chapter_07.md

O sucesso do Kubernetes é baseado no fato de que cada grande provedor na nuvem fornece um serviço Kubernetes gerenciado, tornando-o o padrão de fato para suporte a multicloud. Em outras palavras, não importa qual provedor você escolha, você sempre pode confiar que haverá uma API Kubernetes exposta para você interagir e provisionar seus serviços.


### Capitalizando os benefícios do DDD

Se você seguir o caminho do DDD, precisará primeiramente aproveitar algumas das promessas que o DDD lhe deu e certificar-se de que está colhendo os benefícios. Se não podemos fornecer continuamente novas versões de nossos serviços sem interromper a aplicação inteira, estamos apenas tornando nossa vida mais complicada para nada. Se não estamos entregando valor de negócio concreto como resultado do DDD, todas as mudanças sugeridas neste capítulo não valem o esforço ou o tempo.

Eu recomendo o seguinte artigo: [“The Business Value of using DDD”] (https://www.informit.com/articles/article.aspx?p=1944876&seqNum=4), que dá uma visão geral de alto nível dos benefícios de adotar o DDD, não para você como desenvolvedor, mas para o seu negócio.

A próxima seção explora um exemplo que eu criei com base na minha experiência durante a rearquitetura de aplicações monolíticas Java para uma abordagem mais distribuída. O exemplo é fictício, qualquer semelhança com a realidade é mera coincidência :) Nós encorajamos você a abstrair os conceitos e padrões do cenário de exemplo e mapeá-los para seu próprio domínio. No final das contas, este é apenas um exemplo, embora complexo e totalmente funcional.


## Evoluindo seu monólito na prática, usando DDD

Esta seção cobre um cenário de exemplo que nos ajuda a explicar alguns dos conceitos em ação. Você pode mapear esses conceitos para seu domínio de negócios e copiar a solução técnica real do exemplo para alguns dos desafios apresentados.

Como esperado, criar uma aplicação completa é um trabalho árduo e geralmente requer muito tempo. Por esse motivo, o exemplo a seguir é fornecido como um conjunto de repositórios de código aberto onde você pode contribuir para melhorá-lo.

- [Repositório De Monolith para K8s Github] (https://github.com/salaboy/from-monolith-to-k8s)

> ** DICA: ** Como uma aplicação real, o exemplo evoluirá com o tempo, agregando mais ferramentas e melhores práticas, por isso convidamos você a participar dessa jornada onde todos podemos aprender e compartilhar informações valiosas juntos.

Começamos nossa jornada com uma aplicação Java monolítica. O cenário que iremos abordar pertence a uma empresa que se encarrega de fornecer uma plataforma para a criação de sites de conferências. Imagine que cada um de nossos clientes exija que hospedemos e escalonemos seu site de conferências.
Todos nós vimos grandes aplicações Java Web e, neste cenário, a aplicação se parece com isto:

![chapter_02_01](images/chapter_02_01a.png)

O Facade (fachada) “Customer Management” se encarrega de isolar os diferentes clientes uns dos outros. Em algumas empresas, isso é definido como uma plataforma ou aplicação multi-tenant (multilocatário). Infelizmente, isso é bastante comum no espaço Java. Por razões históricas, as implementações acabaram crescendo em monólitos grandes e assustadores que vieram com muitos problemas de escalabilidade, bem como desafios de isolamento de tráfego e dados. Não importa o quão sofisticada seja a aparência de nossa plataforma, ela está apenas rodando em uma única JVM. Você não tem como dimensionar cada cliente individualmente - você dimensiona tudo ou nada.

Como você pode ver na caixa vermelha, cada Site de Conferência conterá um monte de módulos dependendo da seleção de cada cliente, mas na realidade, em tempo de execução, todo o código estará lá para cada conferência.

> Observe que se você tiver uma plataforma como esta e ela fizer o trabalho que deve fazer, você ** NÃO deve ** alterá-la. A menos que você esteja tendo problemas para gerenciar ou escalar esta "plataforma", você não deve rearquitetar a coisa toda.

Agora, essa arquitetura de monólito tem algumas desvantagens claras e, para este cenário, podemos considerar o seguinte motivo para rearquitetá-la em uma plataforma adequada nativa da nuvem:

- Os clientes não podem ser escalados independentemente.
- O tráfego do cliente é todo gerenciado pela mesma aplicação.
- Ponto Único de Falha na JVM.
- Se os dados são armazenados em um banco de dados, os dados são compartilhados entre os clientes. O código da aplicação precisa lidar com o isolamento dos dados de cada cliente. O banco de dados também se torna um gargalo, pois todos os dados dos clientes estão no mesmo banco de dados.
- Cada mudança na plataforma requer que toda a aplicação seja reiniciada.
- Cada desenvolvedor envolvido com a aplicação trabalha com a mesma base de código, tornando um lançamento e uma fusão de recursos uma grande tarefa com muitos riscos envolvidos. Isso geralmente pode ser feito por alguém que entende toda a aplicação.

> ** DICA: ** Se você já tem esta aplicação instalada e funcionando e tem clientes usando a plataforma, terá um bom entendimento de quais recursos são essenciais e como começar a reformulá-los.

Como Martin Fowler descreve no post [Monolith First] (https://martinfowler.com/bliki/MonolithFirst.html) é o caminho a tomar. Por ter um monólito, você já entende a solução que precisa construir, tornando mais fácil estimar como a nova arquitetura resolverá os problemas da versão existente. Em outras palavras, se você não tem um monólito existente, não comece com uma arquitetura distribuída do zero. Crie um monólito primeiro e depois divida, se necessário.

O próximo passo em nossa jornada é decidir por onde começar. Em minha experiência, vi três padrões comuns se repetindo:

- ** Iniciar novas funcionalidades como serviços separados **: isso geralmente é recomendado se você puder manter o monólito como está. Novos serviços não resolverão os problemas já existentes, mas ajudarão suas equipes de desenvolvedores a se acostumarem a trabalhar com uma mentalidade de microsserviços.
- ** Dividir a funcionalidade existente do monólito ** (e lentamente desative o código antigo): se você tiver problemas urgentes com o monólito, pode avaliar a ramificação de algumas das funcionalidades externas para um novo serviço. Isso pode resolver alguns dos problemas existentes, mas não trará nenhum valor de negócios imediatamente. Isso também aumenta a complexidade das operações do dia a dia, pois você pode acabar executando duas soluções para o mesmo problema por um longo período de tempo. Isso também pode ser usado para entender o quão complexa e cara uma rearquitetura central pode ser.
- ** Reestruturar o núcleo da plataforma como microsserviços ** (para resolver os problemas existentes): Mais cedo ou mais tarde, se você estiver tendo problemas para manter e dimensionar seu monólito, precisará repensar e redesenhar as partes principais de sua plataforma, certificando-se de focar em resolver os problemas atuais de escalabilidade e manutenção. Isso pode ser uma iniciativa cara, mas pode ser feito de forma totalmente isolada de seus ambientes de produção. Várias vezes eu vi como isso é feito como uma Prova de Conceito para demonstrar que é realmente possível e também para garantir que os membros de sua equipe entendam as implicações de um negócio (vantagens) e do ponto de vista técnico (novo stack tecnológico, novas ferramentas).

Neste capítulo, irei cobrir a última dessas opções (** Reestruturar o núcleo da plataforma como microsserviços **) para destacar a solução para nossos problemas existentes com a aplicação monolítica, mas você pode explorar as outras duas, se elas forem mais apropriadas para sua situação.

> ** DICA: ** Mais informações sobre estratégias e práticas sobre como migrar um monólito existente para a arquitetura de microsserviços podem ser encontradas no capítulo [Microsserviços] () #TODO na fase de revisão: adicionar referência a https://github.com /otaviojava/manual-arquiteto-moderno/blob/master/chapter_05.md

É aqui que os conceitos e padrões DDD se tornam realmente úteis para definir como dividir as funcionalidades do monólito e como organizar nossas equipes em torno dos novos serviços. Nas seções a seguir, exploraremos alguns desses conceitos em ação.


### Suposições e simplificações

As plataformas são muito complexas. Tentar reestruturar algo grande vai lhe dar mais dores de cabeça do que soluções. Por esse motivo, você deve tentar simplificar o escopo do problema para enfrentar diferentes desafios de forma controlada.

Para o nosso cenário, isso pode significar que, em vez de tentar a rearquitetura de toda a plataforma, primeiro tentaremos resolver a arquitetura e a forma de um simples site de conferências. Isso significa que, em vez de focar na plataforma, primeiro nos concentraremos no que o cliente espera de seu site de conferência. Ao compreender a forma de um único site de conferência, você e suas equipes têm um conjunto bem definido de desafios a serem resolvidos que podem agregar valor imediato aos negócios. Posteriormente, automatizaremos como cada um desses sites de conferência pode ser provisionado.

Do ponto de vista arquitetônico, isso pode significar um site de conferência monolítico ou um site de conferência construído com diferentes serviços distribuídos. Se os sites de conferência forem complexos o suficiente e quisermos reutilizar os módulos para todos eles, podemos considerar uma abordagem distribuída. Com base na minha experiência, esse tipo de plataforma aproveita os serviços compartilhados na maioria das vezes, então faz sentido arquitetá-los pensando na reutilização.

> ** DICA **: Essa estratégia leva a vários serviços desde o primeiro dia, então isso é algo com o qual você e suas equipes devem se acostumar.

Nossos sites de conferências independentes serão semelhantes a este:
![chapter_02_02](images/chapter_02_02.png)

Como você pode ver no diagrama anterior, está bastante claro que há mudanças arquitetônicas importantes. É bastante comum ter a Interface do Usuário, neste caso, a caixa “Conference Site” separada dos serviços principais. Na maioria das vezes, esse componente voltado para o usuário agirá como um roteador, encaminhando solicitações do site de conferência para serviços que não são expostos diretamente aos usuários.

Mas antes de entrar em detalhes técnicos, como priorizamos e nos certificamos de que começamos com o pé direito? Como podemos definir o escopo desses serviços corretamente (nem muito macro, nem muito micro)? Os conceitos DDD podem nos ajudar, fornecendo algumas respostas e orientações.


### Contextos limitados para começar a dividir seu Monólito

Se você está planejando uma rearquitetura para o núcleo de sua plataforma, você precisa ter cuidado e escolher com sabedoria quais componentes serão rearquitetados primeiramente e por quê.
Na minha experiência, você deve tentar lidar com os componentes principais primeiro. Escolha componentes que você entende e sabe que agregam valor ao negócio onde você está.

No caso do nosso cenário, lidar com a Chamada de Propostas (Call for Proposals) é fundamental para iniciar uma conferência.
Há um trabalho substancial para aceitar propostas, revisá-las e garantir que a conferência tenha uma agenda interessante.

Se estivermos confiantes de que a implementação da funcionalidade de Chamada de Propostas primeiro nos dará vitórias imediatas, precisamos estimar o quão grande e complexo é o esforço.

O DDD propõe o conceito de contexto limitado como um conjunto bem definido de funcionalidades que se encaixam. Essas funcionalidades geralmente mapeiam como um Especialista de Domínio (especialista no assunto) fará o trabalho se não houver software disponível. Para planejar, projetar e implementar essas funcionalidades, uma equipe é montada com Especialistas em Domínio que trabalharão lado a lado com Engenheiros de Software. Na maioria das vezes, um Especialista de Domínio saberá quais contextos delimitados já existem e pelo que eles são responsáveis.

> ** INFO: ** De uma perspectiva DDD, é extremamente importante não tornar esses Bounded Contexts um limite técnico que é imposto aos especialistas em domínio.

Um Bounded Context irá expor um conjunto bem definido de APIs para que outras equipes de diferentes contexto delimitado possam consumir e interagir com a funcionalidade de Chamada de Propostas.

O Bounded Context Call for Proposals permitirá que uma equipe implemente todas as funcionalidades necessárias independentemente das outras equipes. Esta equipa será responsável por todo o ciclo de vida do projeto, desde a concepção, implementação, execução para o resto da empresa e para que os seus clientes o consumam.

Assim que você começa a projetar a funcionalidade Call for Proposals, você percebe que precisará consumir e interagir com outras equipes. Logo no início, os seguintes Bounded Contexts são identificados:

![chapter_02_03](images/chapter_02_03.png)

Cada um desses Bounded Contexts deve pertencer a equipes diferentes e precisamos ter certeza de que eles têm autonomia suficiente para progredir, criar novas versões com novos recursos e implantar componentes de software concretos para os ambientes de nossos clientes.

Do lado prático, cada Bounded Context será implementado como um ou um conjunto de serviços que implementam os recursos do contexto. Não há necessidade de ser estrito quanto ao número de serviços que irão compor um Bounded Contexts, mas geralmente há um que se encarrega de expor um conjunto de APIs para o mundo exterior.

Para o propósito deste exemplo, um único serviço implementará toda a lógica do Bounded Contexts Call for Proposals e a equipe por trás desse serviço será responsável por projetar suas APIs, escolher as estruturas que usarão e realmente implantá-las em um ambiente ao vivo.

Aprofundando os detalhes práticos, existem algumas das melhores práticas compartilhadas por muitas empresas e ferramentas:
- Um Repositório / Um Serviço + Entrega Contínua
- APIs abertas


#### Um Repositório / Um Serviço + Entrega Contínua

Normalmente, é recomendável manter todos os recursos técnicos necessários para executar um serviço próximos ao código-fonte. Isso facilita a manutenção e a carga cognitiva de compreensão de como executar nossos Serviços em um ambiente real.

Hoje em dia, é bastante comum ter um serviço, digamos escrito em Spring Boot versionado em um provedor git como o GitHub, onde podemos encontrar um Dockerfile para criar uma versão em container de nosso serviço que pode ser executado em qualquer lugar onde um Docker daemon esteja presente.

Com o aumento da popularidade do Kubernetes, também é comum encontrar o Manifesto do Kubernetes (arquivos YAML) descrevendo como nosso serviço pode ser implantado em um cluster do Kubernetes. Finalmente, tendemos a usar pipelines de integração contínua para realmente construir todos esses componentes de software, portanto, é bastante comum também encontrar a definição de pipeline perto da fonte que precisa ser construída.

Você, como desenvolvedor que visa o Kubernetes como sua plataforma de implantação, agora é responsável por vários artefatos, não apenas pelo código-fonte do serviço Java.

![](images/chapter_02_04.png)

Para fazer o deploy do seu código no Kubernetes, você precisará:
- Construir e testar seu código fonte, se for Java você pode usar, por exemplo, Maven ou Gradle para fazer isso;

- Isso resultará em um arquivo JAR que você pode querer enviar para um repositório como Nexus ou Artifactory. Este arquivo JAR já terá uma versão nele e se você estiver usando Maven ou Gradle este JAR será identificado por seu GAV (Grupo/Artefato/Versão).

- A partir desse JAR, você cria uma imagem Docker definindo um Dockerfile que entende como executar sua aplicação Java com uma JVM fornecido.

- Por fim, se quiser fazer o deploy do seu container para um cluster Kubernetes, você precisará de alguns manifestos YAML.

- Opcionalmente, se você estiver criando muitos serviços, convém usar o Helm para empacotar e liberar esses arquivos YAML. Todos esses artefatos precisam versionados adequadamente, o que significa que quando você constrói uma nova versão do seu arquivo JAR, um novo container precisa ser construído e um novo gráfico do Helm precisa ser lançado.

  > ** INFO: ** O Helm fornece a ideia de gráficos (pacotes) que mapeiam um a um a forma como lidamos com nossos artefatos Maven. Se você estiver trabalhando com gráficos do Helm, esses gráficos geralmente também são enviados/liberados para um repositório de gráficos, como o Chart Museum.

Neste ponto, se você está pensando "isso é muito trabalho", você está 100% certo. Se você está pensando: 'Eu não quero fazer tudo isso', você está absolutamente certo. Eu também não quero fazer isso. Se você quer que isso funcione, você precisa usar ferramentas especializadas que já entregam todas essas funcionalidades de forma automatizada. Você deve tentar automatizar cada etapa, e a indústria usa pipelines de integração contínua para conseguir isso. Vamos falar sobre como entregar pipelines com Jenkins X.

> ** INFO: ** Neste exemplo, estamos visando o Kubernetes e optamos por usar Jenkins X neste conjunto de ferramentas. Jenkins X, traz CI/CD (Continuous Integration/Continuous Delivery) para o Kubernetes e faz parte da Continuous Delivery Foundation.

Como você pode notar, o Jenkins X não é apenas sobre integração contínua, mas também sobre entrega contínua. Ao cobrir a entrega contínua, o pipeline não para quando esses componentes são construídos. O pipeline é responsável por construir, testar e também implantar nossos artefatos em um ambiente ativo onde serão executados para atender nossos clientes. A parte “contínua” faz referência ao fato de que você deseja ter certeza de que a implantação de uma nova versão do seu serviço é fácil e você terá como objetivo implantar novas versões em um curto período de tempo.

> ** INFO: ** Para alcançar a entrega contínua, o Jenkins X usa um conjunto de convenções para permitir que os desenvolvedores se concentrem na construção de valor de negócios. Essas convenções não são exclusivas do Jenkins X e fazem parte das melhores práticas coletadas de diferentes setores e profissionais. Projetos como o Jenkins X são os catalisadores para milhares de membros da comunidade, que são especialistas em CI/CD, o que resulta nas melhores práticas e ferramentas que as aplicam.

Uma das convenções usadas pelo Jenkins X é chamada “Trunk Based Development”. Basicamente, significa que cada alteração aplicada (merged) à branch master irá gerar uma nova versão de nossos artefatos. Na maioria das vezes, isso não é confortável para os desenvolvedores, já que muitas dessas práticas são comumente definidas em cada empresa e tendem a variar bastante. A principal motivação para usar algo como o Trunk Based Development é garantir que as equipes não gastem tempo definindo essas práticas. Ao trabalhar com essa convenção, você pode se concentrar na escrita do código e, quando o código estiver pronto e mesclado ao master, uma nova versão é criada e implantada em algum tipo de ambiente de teste para validações adicionais.

> ** DICA: ** Recomendo fortemente que, se você estiver iniciando um novo projeto, verifique as vantagens do Trunk Based Development, bem como do livro Accelerate, pois foi usado como base para a criação de ferramentas como Jenkins X. https://jenkins-x.io/about/overview/accelerate/

No fim das contas, Jenkins X usa ambas as convenções, “Um Repositório / Um Serviço” mais “Trunk Based Development”, para levar seu serviço do código-fonte para uma instância em execução dentro de um Cluster Kubernetes.

![chapter_02_05](images/chapter_02_05.png)

Em nosso exemplo, os links a seguir demonstram todos esses conceitos em ação

- Pipeline: https://github.com/salaboy/fmtok8s-email/blob/master/jenkins-x.yml
- DockerFile: https://github.com/salaboy/fmtok8s-email/blob/master/Dockerfile
- Gráficos do Helm: https://github.com/salaboy/fmtok8s-email/tree/master/charts/fmtok8s-email
- Versões contínuas: https://github.com/salaboy/fmtok8s-email/releases

Você pode encontrar o mesmo setup para todos os projetos dentro da Demonstração do Site de Conferência.


#### APIs abertas
Se você já está implementando um Bounded Context, logo no início você precisará projetar e especificar que tipo de interface irá expor para outro Bounded Context e serviços terceiros que possam estar interessados ​​na funcionalidade que seu contexto fornece. Uma maneira popular de implementar essas APIs são os terminais REST.

> ** INFO: ** Como você provavelmente está familiarizado com endpoints REST, esta seção se concentra na [Especificação de API aberta] (https://github.com/OAI/OpenAPI-Specification)

Conforme definido no texto das especificações “* a especificação OpenAPI remove as suposições ao chamar um serviço. *” Hoje em dia, frameworks populares como Spring Boot vêm com integração pronta para uso com API aberta e ferramentas de API aberta.

Apenas adicionando uma extensão/iniciador Spring Boot, você permite que sua aplicação exponha uma interface de usuário que serve como documentação e navegador ao vivo de suas APIs.

Se você está usando Spring Boot padrão (Tomcat), você precisa adicionar ao seu arquivo `pom.xml`:

```xml
<dependency>
   <groupId>org.springdoc</groupId>
   <artifactId>springdoc-openapi-ui</artifactId>
   <version>${springdoc-openapi-ui.version}</version>
</dependency>
```
Se você estiver usando o Webflux, a pilha reativa que você precisa adicionar:
```xml
<dependency>
   <groupId>org.springdoc</groupId>
   <artifactId>springdoc-openapi-webflux-ui</artifactId>
   <version>${springdoc-openapi-ui.version}</version>
</dependency>
```

- https://github.com/salaboy/fmtok8s-email/blob/master/pom.xml#L40 

Em projetos da vida real, essas interfaces de usuário e documentos de especificação de API podem ser usados por outras equipes para entender com detalhes concretos como interagir com seus serviços. Quanto mais cedo você expõe uma API, mais cedo outras equipes podem começar a aproveitar seu serviço.

A captura de tela a seguir mostra a interface de usuário da API aberta que é fornecida apenas incluindo a dependência anterior. Essa tela pode ser acessada apontando seu navegador para host:port/swagger-ui.html e fornece um cliente simples para interagir com seus serviços, entender quais terminais estão expostos e quais dados esses terminais esperam e retornam.

![chapter_02_06](images/chapter_02_06.png)

Sinta-se à vontade para clonar um dos serviços deste exemplo e executá-lo com o comando `mvn spring-boot: run` para explorar as definições de APIs de cada serviço. Por padrão, cada serviço iniciará na porta 8080, portanto, você deve apontar seu navegador para http://localhost:8080/swagger-ui.html

### Mapa de contexto para entender as interações técnicas e da equipe

Bounded Contexts são ótimos para entender um conjunto bem definido de funcionalidades que precisam ser fornecidas juntas. Quando temos vários desses contextos, precisamos entender como eles irão interagir uns com os outros e seus relacionamentos. É aí que o conceito de Mapas de Contexto realmente ajuda. Com mapas de contexto, você pode mapear as relações entre o bounded contexts e o que eles realmente precisam para interagir. O mapeamento de contexto também fornece visibilidade sobre como as equipes responsáveis ​​por cada contexto limitado irão interagir com outras equipes.

Do lado prático, trata-se de integrações de sistema. Como nossos serviços ou os serviços que expõem algum tipo de API conversam entre si. Como eles transformam e movem dados e como eles sabem quais serviços estão disponíveis para consumo.

Como você pode imaginar, as APIs são extremamente importantes, mas entender quem vai consumir nossas APIs, o que se espera dessas APIs e quem realmente depende de nós é igualmente crítico.

Mapas de Contexto bem definidos ajudam muito a planejar e compreender como esses Bounded Contexts “isolados” e as equipes que trabalham neles irão interagir no dia a dia.

Para nosso exemplo, o seguinte mapa de contexto faria sentido:
![chapter_02_07](images/chapter_02_07.png)

Este diagrama descreve as relações entre o Bounded Context simples que temos para nossa aplicação do site de conferência. Aqui podemos ver que existe uma relação ** Cliente/Fornecedor ** entre a Call for Proposals e o Bounded Context da Agenda da Conferência. Onde a Call for Proposals ** é um consumidor ** da Agenda da Conferência do serviço upstream. Entre essas duas equipes, existe também uma relação de ** Parceria(Partnership) **, pois elas precisam colaborar para fazer as coisas. Isso significa que a comunicação entre essas duas equipes é importante e elas devem ser capazes de influenciar o roteiro uma da outra.

Por outro lado, a relação com o serviço de Notificação é diferente. A Call for Proposals tem uma relação ascendente com o Bounded Context de Notificação(Notification), mas vai ** confortar ** com seus contratos. Isso significa que, da perspectiva da equipe de Call for Proposals, eles não podem influenciar ou alterar as APIs do Bounded Context de notificação. Isso acontece muito quando temos sistemas legados ou quando esse bounded context é externo à nossa empresa.

> ** DICA: ** Pulando para o lado prático, embora as integrações de sistema sejam um tópico muito amplo, esta seção se concentra em uma recomendação muito prática: “Você deve aprender sobre testes de contato orientados ao consumidor(Consumer-Driven Contact)”. Mais uma vez, Martin Fowler tem um artigo, publicado em 2006, sobre isso: https://martinfowler.com/articles/consumerDrivenContracts.html.

Embora o tópico em si não seja novo, existem ferramentas muito atualizadas para realmente implementar isso em seus projetos, como [Spring Cloud Contracts] (https://spring.io/projects/spring-cloud-contract).

#### Bônus: Implementação de contratos com Spring Cloud Contracts

Com Spring Cloud Contracts, a história é assim: primeiro você define um contato para suas APIs, isso basicamente significa que tipo de solicitação o consumidor deve enviar e que tipo de resposta precisamos fornecer como serviço.

Um contrato é semelhante a isto: https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/src/test/resources/contracts/shouldAcceptPostProposal.groovy

```groovy
Contract.make {
       name "should accept POST with new Proposal"
       request{
           method 'POST'
           url '/'
           body([
               "title": $(anyNonEmptyString()),
               "description": $(anyNonEmptyString()),
               "author": $(anyNonEmptyString()),
               "email": $(anyEmail())
           ])
           headers {
               contentType('application/json')
           }
       }
       response {
           status OK()
           headers {
               contentType('application/json')
           }
           body(
                   "id": $(anyUuid()),
                   "title": $(anyNonEmptyString()),
                   "description": $(anyNonEmptyString()),
                   "author": $(anyNonEmptyString()),
                   "email": $(anyEmail())
           )
       }
   }
```
Este contrato define a interação para o envio de uma nova Proposta ao Serviço Call for Proposal. Como você pode ver, ele envolve uma solicitação `POST` e um body com algumas propriedades predefinidas, incluindo um `header` com um `Content Type` muito específico. Este contrato também define que o retorno para o consumidor agregará às informações enviadas uma propriedade `id` com formato `UUID`.

Agora, este contrato pode ser usado para gerar um teste para realmente testar se o seu serviço está funcionando conforme o esperado do ponto de vista do consumidor. Portanto, se você definiu qualquer contrato em seu projeto ao construir e testar seu projeto, os contratos serão executados em uma instância real de seu serviço. Isso nos permite ter certeza de que quebraremos a construção se um contrato for quebrado. Para criar e executar automaticamente esses testes, você só precisa adicionar uma dependência e um plug-in ao seu projeto maven:
https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/pom.xml#L50

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-contract-verifier</artifactId>
  <version>${spring.cloud.contract}</version>
  <scope>test</scope>
</dependency>

```

E na seção `<build><plugins>` o seguinte plugin:
https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/pom.xml#L88

```xml
<plugin>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-contract-maven-plugin</artifactId>
  <version>${spring.cloud.contract}</version>
  <extensions>true</extensions>
  <configuration>
    <packageWithBaseClasses>com.salaboy.conferences.c4p</packageWithBaseClasses>
    <testMode>EXPLICIT</testMode>
   </configuration>
</plugin>

```

Finalmente, dependendo da forma do serviço que você testará, pode ser necessária alguma confirmação: https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/src/test/java/com/salaboy/conferences/c4p/ContractVerifierBase.java#L16

```java
@RunWith(SpringRunner.class)
@SpringBootTest( webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT,
        properties = "server.port=0")
public abstract class ContractVerifierBase {

    @LocalServerPort
    int port;
    
    ...
}
```

Se você precisar adicionar qualquer código de inicialização, esta é a classe que todo teste gerado automaticamente deve herdar.

Agora que o contrato foi validado com cada build, também podemos usar o contrato para gerar um Stub, que é um serviço que se comporta como o serviço real, mas com dados fictícios. Os dados fictícios também são gerados automaticamente, pois também podem ser fornecidos pela definição do contrato. Este Stub é um artefato por si só que pode ser distribuído para outros serviços, por exemplo, aqueles que consomem o serviço “real” para teste.

Isso basicamente significa que agora, toda vez que você construir seu serviço, ** dois ** arquivos JAR serão criados. Um é o JAR da aplicação Spring Boot real e o outro é o Stub de Serviço.
Este Stub de Serviço pode ser enviado automaticamente para o repositório do seu artefato (por exemplo, Nexus ou Artifactory) e viverá no mesmo grupo e nome do artefato da sua aplicação JAR.

Finalmente, um Serviço X projetado para consumir seu serviço pode criar testes que iniciarão o Stub gerado anteriormente localmente para evitar a necessidade de uma instância real ou uma configuração de ambiente inteira. Você pode facilmente iniciar o Stub antes de seus testes usando as seguintes anotações:


```java
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)
@AutoConfigureMockMvc
@AutoConfigureJsonTesters
@AutoConfigureStubRunner(stubsMode = StubRunnerProperties.StubsMode.REMOTE, repositoryRoot = "<your nexus repository>", ids = "com.salaboy.conferences:fmtok8s-c4p")
public class C4PApisTests {
   @StubRunnerPort("fmtok8s-c4p")
    int producerPort;

...
}
```

https://github.com/salaboy/fmtok8s-api-gateway/blob/master/src/test/java/com/salaboy/conferences/site/C4PApisTests.java#L29

Isso baixa automaticamente a versão mais recente do stub e o executa antes que seu teste comece usando uma porta aleatória que você pode obter via injection usando `@StubRunnerPort`. 

É importante observar que tanto o Serviço quanto os contratos são versionados juntos como parte da mesma base de código. Isso implica que o Stub gerado e o próprio Serviço terão a mesma versão. Um serviço de consumidor, para executar seus testes, pode depender do Stub, pois nunca deve depender do próprio serviço. Assim que o consumidor tiver testado usando o Stub de Serviço do produtor, você pode reconhecer rapidamente quando um contrato é quebrado ou quando uma nova versão do contrato não é mais compatível com os consumidores, pois os testes usando os Stubs serão interrompidos quando versões novas e incompatíveis forem lançadas. Neste momento, os consumidores se deparam com uma decisão simples: ficar dependendo dos contratos antigos com uma versão fixa, ou atualizar para a versão mais recente do contrato. Como você pode ver, isso pode exigir que você execute várias versões do seu serviço ao mesmo tempo. Felizmente para nós, o Kubernetes foi criado para oferecer suporte a esses cenários. Você pode ler sobre versões canário (Canary Releases) se estiver interessado em aspectos de multi-version deployments.

> ** DICA: ** O capítulo [Cloud] () cobre Canary Releases, bem como outras estratégias de implantação. #TODO na fase de revisão: adicione referência a https://github.com/otaviojava/manual-arquiteto-moderno/blob/master/chapter_07.md

Ambos Bounded Contexts e Mapas de contexto são ótimas ferramentas conceituais para entender como estruturar suas equipes e seu software, mas mais importante, esses conceitos ajudam você a se concentrar no valor do negócio.


### Foco no valor do negócio

Ao trabalhar com aplicativos reais, você precisa se concentrar no que vai agregar mais valor e resolver mais problemas para o seu negócio. Sendo muito prático, tendo a analisar casos de uso e como o Bounded Context em conjunto com os Mapas de Contexto fornece um fluxo de negócios de ponta a ponta.

Para este exemplo específico, o cenário de conferência, estamos analisando o fluxo básico da Call for Proposals, que se parece com isto:

1. O palestrante em potencial envia uma proposta pelo site da conferência
2. O Conselho/Comitê analisa a proposta
3. Se a proposta for aceita
   1. Um novo item da agenda é criado e publicado na página da agenda
4. Uma notificação é enviada por e-mail para propostas aceitas e rejeitadas

Você precisa prestar atenção às suas interações humanas, pois essas interações tendem a exigir comportamentos assíncronos, como lembretes, notificações, alertas, bem como interfaces de usuário, que devem ser cuidadosamente projetadas.

> ** DICA: ** Como engenheiros, tendemos a simplificar e subestimar a quantidade de trabalho e iterações que podem exigir a criação de uma boa experiência do usuário.

A interface do usuário que cobre este cenário simples é assim:

* A página principal dentro do Site da conferência exibe a Agenda dividida por dias. Os itens da pauta são os que já estão confirmados e foram aprovados pelo comitê.

  ![chapter_02_08](images/chapter_02_08.png)

* A página principal também permite que palestrantes em potencial enviem propostas, preenchendo um formulário:

  ![chapter_02_09](images/chapter_02_09.png)

* Assim que a proposta for enviada, o palestrante em potencial precisará aguardar a aprovação ou rejeição do comitê. Os membros do comitê têm uma página de back-office onde podem aprovar ou rejeitar cada proposta enviada:

  ![chapter_02_10](images/chapter_02_10.png)

* A página de back-office também oferece aos membros do Conselho a opção de enviar notificações por e-mail aos palestrantes em potencial.

  ![chapter_02_11](images/chapter_02_11.png)



Mais uma vez, você pode notar a simplificação deste cenário propositalmente para estabelecer um conjunto básico de funcionalidades, iterar rapidamente e fazê-lo funcionar e então expandir os requisitos.

#### Arquitetura e Serviços

De uma perspectiva arquitetônica, parece mais assim:
![chapter_01_12](images/chapter_01_12.png)

Onde a Interface do Usuário com alguma capacidade de roteamento é necessária para encaminhar solicitações ao Serviço Call for Proposals (C4P) para o Serviço de Agenda ou Emails. Neste exemplo, todas as comunicações acontecem por meio de invocações HTTP/Rest.

### Gateway de API / Interface do usuário
Na maioria das vezes, um Gateway de API também é usado para ocultar acesso direto a todos os outros serviços. É bastante comum ver esse serviço delegando autorização e autenticação a um provedor OAuth ou SAML, servindo como uma barreira de segurança para o mundo externo. O exemplo usa o [Spring Cloud Gateway] (https://spring.io/projects/spring-cloud-gateway), que fornece o mecanismo de roteamento para encaminhar solicitações de entrada para o restante dos serviços. O Spring Cloud Gateway nos permite transformar qualquer aplicação Spring Boot em um roteador de solicitação com recursos avançados.

> ** INFO: ** É importante observar que o Spring Cloud Gateway oferece a flexibilidade de realmente adicionar programaticamente qualquer transformação que você deseja/precisa nas solicitações(requests) de entrada. Esse poder e liberdade vêm com a desvantagem de que depende de você manter, testar e corrigir o bug. Em grandes projetos, você pode querer avaliar um gateway de API de terceiros (como Kong, 3Scale, Apigee, etc) com base nos requisitos do seu projeto.

O módulo API Gateway / interface do usuário pode ser encontrado neste repositório: https://github.com/salaboy/fmtok8s-api-gateway

O que adiciona às suas dependências maven:

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
```
E configure as rotas padrão para nossos serviços dentro do arquivo application.yaml:
https://github.com/salaboy/fmtok8s-api-gateway/blob/master/src/main/resources/application.yaml#L4

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: c4p
        uri: ${C4P_SERVICE:http://fmtok8s-c4p}
        predicates:
        - Path=/c4p/**
        filters:
          - RewritePath=/c4p/(?<id>.*), /$\{id}
      - id: email
        uri: ${EMAIL_SERVICE:http://fmtok8s-email}
        predicates:
        - Path=/email/**
        filters:
          - RewritePath=/email/(?<id>.*), /$\{id}
```

Essas rotas definem um caminho para o gateway como `/c4p/**` irá encaminhar automaticamente a solicitação para o serviço http://fmtok8s-c4p.

> ** INFO: ** A interface de usuário do site pode ser encontrada aqui: https://github.com/salaboy/fmtok8s-api-gateway/tree/master/src/main/resources/templates
> O Controller que busca os dados dos serviços de back-end aqui: https://github.com/salaboy/fmtok8s-api-gateway/blob/master/src/main/java/com/salaboy/conferences/site/DemoApplication.java

Como estamos executando no Kubernetes, podemos usar o nome do serviço Kubernetes em vez de apontar para um pod específico. Esse mecanismo de roteamento nos permite expor apenas os Endpoints do Gateway de API para o mundo externo, deixando todos os outros serviços atrás de uma rede segura.

### Eventos de Domínio e o Serviço Call for Proposals
Como o fluxo em análise é fundamental para o Bounded Context Call For Proposals, não é surpresa que a lógica central pertença ao Serviço Call For Proposals, mais concretamente, às duas funções a seguir: Envio de Proposta e Decisão Tomada pelo Conselho (Proposal Submission and Decision Made By the Board).

> ** INFO: ** O Serviço Call for Proposals pode ser encontrado aqui: https://github.com/salaboy/fmtok8s-c4p/

O endpoint de Envio de proposta aceita uma proposta da interface do usuário e a armazena em um banco de dados ou storage. Este é um passo importante, precisamos garantir que não perderemos propostas. Observe que podemos estar interessados ​​em emitir um evento de domínio DDD neste momento, pois outros sistemas/aplicações podem estar interessados ​​em reagir sempre que uma proposta for recebida.

> ** INFO: ** Verifique uma implementação real aqui: https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/src/main/java/com/salaboy/conferences/c4p/C4PController.java#L37

Mais importante, o endpoint Decision Made by the Board registra uma decisão feita pela diretoria, mas também define as etapas a seguir com base nessa decisão. Na vida real, essa decisão afetará o curso da ação. Na maioria das vezes, esses pontos de decisão e as ações derivadas deles são essenciais para administrar um negócio eficiente e com boa relação custo-benefício.
https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/src/main/java/com/salaboy/conferences/c4p/C4PController.java#L60

```java
@PostMapping(value = "/{id}/decision")
    public void decide(@PathVariable("id") String id, @RequestBody ProposalDecision decision) {
        emitEvent("> Proposal Approved Event ( " + ((decision.isApproved()) ? "Approved" : "Rejected") + ")");
        Optional<Proposal> proposalOptional = proposalStorageService.getProposalById(id);
        if (proposalOptional.isPresent()) {
            Proposal proposal = proposalOptional.get();

            // Apply Decision to Proposal
            proposal.setApproved(decision.isApproved());
            proposal.setStatus(ProposalStatus.DECIDED);
            proposalStorageService.add(proposal);

//          Only if it is Approved create a new Agenda Item into the Agenda Service
            if (decision.isApproved()) {
                agendaService.createAgendaItem(proposal);
            }

            // Notify Potential Speaker By Email
            emailService.notifySpeakerByEmail(decision, proposal);
        } else {
            emitEvent(" Proposal Not Found Event (" + id + ")");
        }

    }
```

Como você provavelmente pode imaginar, esse método contém a lógica de todo o fluxo. Começa por verificar se o `Id` da proposta pode ser encontrado, se estiver presente aplicará a decisão ao objeto da proposta (`Approved` ou `Rejected`) e somente se for aprovado chame o Serviço Agenda para criar um novo item da agenda. Não importa se foi aceito ou não, um e-mail deve ser enviado ao potencial palestrante para notificá-lo sobre a decisão. Ambos [agendaService](https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/src/main/java/com/salaboy/conferences/c4p/services/AgendaService.java) e [emailService](https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/src/main/java/com/salaboy/conferences/c4p/services/EmailService.java) estão simplesmente encapsulando chamadas REST simples.

Este exemplo de método simples, em cenários da vida real, nunca é simples. Devido à complexidade inerente dos desafios da vida real, você deve esperar que métodos simples como o discutido acima se tornem monstros reais. A próxima seção aborda algumas das principais armadilhas e considerações que você deve ter em mente ao escrever a lógica de negócios que é a chave para seu domínio. A próxima seção também tenta compartilhar recursos, abordagens e projetos valiosos que podem ajudá-lo a tomar melhores decisões no início para evitar erros comuns.

### Armadilhas comuns
As aplicações da vida real são complexas, e essa complexidade tende a vir da complexidade inerente dos problemas que estamos tentando resolver. Podemos tentar reduzir essa complexidade usando uma abordagem de melhoria contínua e certificando-nos de que não estamos reinventando rodas desnecessárias que não estão fornecendo qualquer diferenciação nos negócios.

Vamos começar com algo que você pode ter enfrentado no passado: comunicações REST para REST podem ser desafiadoras.

#### comunicações REST para REST podem ser desafiadoras
Se você olhar o exemplo fornecido na seção anterior, os serviços de Agenda e de Email são chamados a partir do Serviço Call for Proposals usando uma [chamada REST](https://github.com/salaboy/fmtok8s-c4p/blob/no-workflow/src/main/java/com/salaboy/conferences/c4p/services/AgendaService.java#L29). Como discutimos antes, essas interações representam uma parte fundamental de nosso fluxo de negócios, então você precisa ter certeza de que essas interações ocorram conforme planejado 100% das vezes. É vital que nosso aplicativo não acabe em um estado inconsistente, por exemplo, uma proposta é aprovada e publicada na agenda, mas nunca enviamos uma notificação ao palestrante. Como está codificado neste exemplo, se o Serviços Agenda ou Email ficarem inativos, a solicitação HTTP silenciosamente falhará.

Este requisito básico, ao trabalhar com sistemas distribuídos onde não existe estado compartilhado entre os serviços, torna-se um desafio com várias soluções possíveis.

Uma solução comum é escrever dentro de nosso código de serviço para tentar novamente em caso de falha, o que torna cada chamada muito mais complicada. No passado, havia várias bibliotecas que forneciam auxiliares para tais situações. É importante notar que o Kubernetes por padrão não lida com esse tipo de falha de forma alguma.

Outra solução pode ser usar um mecanismo de mensagem ou pub/sub para comunicar nossos serviços que, prontos para uso, oferecem mais garantias sobre a entrega das mensagens enviadas. Mudar para o uso de mensagens como RabbitMQ ou Kafka apresenta outro conjunto de desafios, na maioria das vezes relacionado a lidar com outra peça complexa de infraestrutura que precisa ser mantida ao longo do tempo. No entanto, o sistema de mensagens provou ser robusto e a única opção para determinados cenários em que essas garantias são necessárias e o volume de interações é alto.

Finalmente, uma abordagem mais recente são as Service Meshes, em que delegamos a responsabilidade de tentar novamente, por exemplo, à infraestrutura. O Service Mesh usa proxies para inspecionar cargas HTTP e códigos de erro, de forma que novas tentativas automáticas possam ser feitas em caso de falha.

> ** DICA: ** Você deve dar uma olhada em Istio, Gloo e LinkerD se quiser entender mais sobre como funcionam Service Meshes e quais são suas vantagens. Mais detalhes sobre as Service Meshes são compartilhados no capítulo Nuvem. #TODO na fase de revisão: adicione referência a https://github.com/otaviojava/manual-arquiteto-moderno/blob/master/chapter_07.md

#### Fluxo enterrado no código
É bastante comum encontrar lógicas de negócios complexas escondidas dentro de nossos serviços, de certa forma obscurecidas por todos os padrões necessários para lidar com erros técnicos, buscar dados de diferentes fontes e transformar dados em diferentes formatos. Em projetos da vida real, fica muito difícil para os Especialistas em Domínio entenderem de fato o código que implementa seus fluxos de negócios.

O exemplo discutido neste capítulo se tornaria difícil de ler se adicionarmos o código para lidar com outros aspectos, como:
- Caminhos infelizes e casos excepcionais: como o palestrante que apresentou a proposta desapareceu e não está respondendo e-mails.
- Eventos, lembretes e restrições com base no tempo(Time-Based): por exemplo, programe um lembrete para os membros do conselho analisarem uma proposta antes de 3 dias após o envio. Cancele o lembrete se a decisão foi tomada antes do prazo.
- Novos requisitos são adicionados, o que leva os desenvolvedores a mudar a sequência do fluxo: como, por exemplo, um e-mail que precisa ser enviado com um link de confirmação para o palestrante antes de publicarmos e aprovarmos a palestra para a agenda. Quanto mais requisitos, mais código precisamos adicionar, e mais ele vira um código espaguete.
- Relatórios e análises: por exemplo, seu gerente quer saber quantas propostas recebemos por dia e quanto tempo em média leva para aprovar ou rejeitar propostas. Você pode ficar tentado a adicionar novos endpoints para lidar com esses relatórios dentro do mesmo serviço.

Não existem soluções milagrosas para enfrentar esses desafios, mas eu gostaria de mencionar algumas coisas que podem, em seus cenários, ajudar a reduzir a complexidade e fornecer visibilidade sobre como seus serviços estão funcionando.

Os eventos de domínio (Domain Events) são introduzidos no DDD para externalizar o estado da aplicação que pode ser relevante para o consumo de outros serviços. Do lado prático, esses eventos podem ser criados usando [Cloud Events](cloudevents.io), que fornece um formato independente de transporte para a troca de eventos.

As ferramentas de Orquestração de Serviço podem ser usadas em conjunto com Cloud Events para externalizar a lógica de negócios enterrada em nossos serviços.

> ** DICA: ** Verifique projetos como Zeebe, jBPM ou Kogito para entender mais sobre como essas ferramentas podem ajudá-lo. Além disso, você pode achar este post sobre Cloud Events muito útil.

Finalmente, conforme discutido no livro * Implementing DDD *, patterns(padrões) como CQRS (Command/Query Responsibility Segregation [Segregação de Responsabilidade de Consulta e Comando]) podem ajudá-lo muito ao lidar com relatórios e análises. Você quer evitar a execução de relatórios caros ou rotinas intensas de processamento de dados no banco de dados de serviço. Ao aplicar o CQRS, você externaliza os dados sobre os quais está interessado em relatar em um armazenamento separado que possui um formato otimizado para indexar, pesquisar e resumir dados. Uma abordagem popular é enviar dados para ElasticSearch para a indexação completa do texto. Então, na sua aplicação, se você deseja pesquisar entre milhares de propostas, você não consulta o Serviço Call For Proposals. Em vez disso, você usa os índices ElasticSearch, descarregando o serviço Call for Proposals, para que ele possa continuar aceitando propostas para suas conferências.

#### Adaptadores(Adapters) para sistemas legados

Uma breve nota sobre Sistemas Legados: tente abstraí-los para que você tenha controle sobre suas APIs. Para o exemplo visto neste capítulo, um serviço chamado Email foi introduzido para expor via endpoints HTTP a funcionalidade de um servidor de email. Isso foi feito propositalmente para destacar as vantagens de fornecer um adaptador para um servidor que não podemos alterar (um servidor SMTP). Neste adaptador, podemos incluir funções auxiliares, modelos e funcionalidades específicas de domínio que são exigidas por nossos casos de uso.

O serviço Email fornecido não inclui uma conexão SMTP, mas expõe um conjunto de APIs que são fáceis de consumir e não exigem que outros serviços incluam clientes SMTP.

> ** INFO: ** O código-fonte deste serviço pode ser encontrado aqui: https://github.com/salaboy/fmtok8s-email

Considere a criação de adapters para seus serviços legados, lembre-se de que dentro do Kubernetes, mesmo que os adapters sejam criados em containers separados, esses containers podem ser executados dentro do mesmo host, evitando um salto(hop) extra de rede.

## Resumindo

Este capítulo abordou uma ampla gama de ferramentas e princípios referentes à uma aplicação de exemplo. Algumas dessas ferramentas farão sentido para o seu cenário, outras não. O que é importante tirar daqui pode ser resumido nos seguintes pontos.

- Otimize as decisões sobre a construção ou integração de software de terceiros para resolver questões específicas ou cruzadas (cross-cutting concerns). Ter um processo claro para avaliar as ferramentas em comparação com a construção de software internamente para desafios que não são essenciais para o seu negócio é fundamental para ganhar agilidade.

- Mantenha suas equipes atualizadas com treinamentos. A transferência de conhecimento é um grande problema quando o stack tecnológico é amplo e complexo. Aprenda a identificar quais são os principais tópicos com os quais suas equipes têm dificuldade e encontre treinamento que possa ajudar a difundir o conhecimento entre elas.

- Use convenções em vez de definições internas, aproveite as comunidades de código aberto. Comunidades de código aberto são ótimos lugares para encontrar as melhores práticas aplicadas, inovações e tendências. Não tenha medo de participar, envolver-se e compartilhar seus aprendizados.

- Considere o uso de provedores SaaS (Software as a Service) em vez de hospedagem interna, quando possível. Se você já está rodando em um provedor na nuvem, precisa seriamente considerar o conjunto de serviços que eles oferecem. Provedores na nuvem e ofertas de SaaS economizarão um tempo valioso ao configurar e manter peças-chave de sua infraestrutura. Como um desenvolvedor pode executar Kafka, ElasticSearch ou qualquer outra ferramenta de terceiros usando containers, não significa que ele esteja disposto a manter, atualizar e fazer backup desses serviços para toda a empresa.



