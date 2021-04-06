Appendix A: Security

## Security Practices

Secure coding should be a rule in any application design, as in some branches, they are as crucial as any quality rule you may have. It is no different for microservices, and there is no secret here; it is the same rule for any application design: at the very least, one must look at the [OWASP Top 10](https://owasp.org/www-project-top-ten/) and follow these recommendations in their project. The least the developer needs to know is these vulnerabilities.

Seriously, there is nothing new here, and anyone trying to convince you of a "Cross Microservice Injection" or something like that is trying to fool you.

In the case of Microservices, the approach that changes is concerning authorization and authentication, and we will discuss this later.

An important tip: when it comes to safe code, use CI/CD practices to check if there is any vulnerability in your code. It is interesting to confirm that the third-party libraries you plan to use will not cause problems for the environment.

And there are several tools for that, including [Fortify](https://www.microfocus.com/en-us/solutions/application-security), [Snyk](https://snyk.io/), [JFrog Xray](https://jfrog.com/xray/). Because sometimes an outdated dependency can put your service at risk, looking at the best code practices and tools to help point out where to improve will form an unbeatable team.

Another practice I see in some companies, especially when services are exposed only internally, is not using HTTPS, or rather, using a TLS (Transport Layer Security). And what do you need it for? To have privacy, integrity, and identification.

And when we consider microservices, there will always be concerns about authorization servers. We may be talking about an API Key, a "client secret", or even credentials for basic authentication. So the first basic rule is: don't leave these keys in your source repository. They need to be environment variables or external configuration keys and must always be encrypted.

Regarding containers, these practices are also valid. Never run your container as "root". You need to assume that your system is never 100% secure; someone will be able to exploit something. So you can’t just prevent it; you need to detect and react to it.

The key is to follow at least five pillars:

- Secure by design;
- Search your dependencies;
- Always use HTTPS;
- Use Identity and Access Tokens;
- Protect and encrypt your secrets.

#### Solutions for authentication and authorization

For the microservices world, the main point is to check who you are (authentication) and what you can do (authorization). And within the microservice architecture, you will be spread out over many services over the network and will have to deal with some problems related to solving them.

Authentication and authorization must be resolved in each of the microservices. Part of this global logic will have to be replicated in all services. One way to solve this is to create libraries to standardize this implementation, but this will make you lose some of the flexibility of which technologies to use because the language or framework needs to support this standard library.

The library’s use also helps not to break the principle of single responsibility since the service should only be concerned with business logic.

And another point that needs to be analyzed is that microservices must be stateless, so it is necessary to use solutions to maintain this.

We can approach authorization and authentication using the distributed session template, using tools to store that session. You can approach/maintain the session in the following ways:

**Sticky Session** - The idea here is to use the load balancer and keep whoever uses it on the same server where the request came from. But this will make you expand only horizontally.

**Session Replication** - Every instance saves the session and synchronizes through the network. But this will cause a network "overhead". The more instances, the more you will need to replicate and deal with the latency.

**Centralized Session** - This means data can be retrieved from a shared repository. In many scenarios, this is an excellent design because you can give high performance to the applications, and you leave the login status hidden within that session. But of course, there is the  disadvantage that you need to create mechanisms to protect that session and replicate it among applications, which can also add latencies to your network.

But when we are in this microservice scenario, the recommendation is to use tokens. The most significant difference to the session model described above is that we no longer have something centralized on a server and pass the responsibility on to the person who will use it.

The token will have the identification information of those who use it, and every time it reaches the server, we can validate the identity and authorization there. The token is encrypted and may follow a pattern like [JWT](https://jwt.io/).

Using a token, we can delegate responsibility for the user’s state for some process that may demand their validation. We enable several types of security validations that can be placed on the mesh (Service Mesh) or your entry gateway and remove these responsibilities from services and applications and continue to guarantee security.

Using JWT, you will have a "client token", and you will move to a server to do the validation/creation of it.

And when it comes to tokens, the key is not to reinvent the wheel but to use what is already consolidated! Here OpenId and OAuth/OAuth2 come in. Oauth 2 is practically the most widely used pattern for authentication.

The OpenID pattern is used to connect or use the token to log in to various sites or services. But in your local pattern, the recommendation is OAuth, which establishes a protocol so that you have access to the resources you need, and it works with four roles:

**Resource Owner** - This is the role that controls access to resources.

**Resource Server** - This is where the services to be accessed are located; that is, this is where your APIs, applications, etc. are.

**Client** - Who requests the Resource Owner the resource they need to consume.

**Authorization Server** - Who generates the access tokens, allows the Client to reach the resources that were allowed with the defined access level.

And how does it work? The "client" asks the Resource Owner for access to the resource, and the latter, when authorizing, sends the "authorization" to the "Client", which is the credential that represents the Resource Owner's authorization. Then the "Client" will ask the Authorization Server for an access token; everything being valid, the Client receives its access token, which will be passed on to the Resource Server so that it can consume what was requested.

And there are four types of flows to obtain the access token in the OAuth pattern. We have the Authorization Code, which is the most common one; Implicit, which is widely used by SPA applications; the Resource Owner establishes trust between applications; and Client Credentials, which is used to connect one service to another.

Apart from the authentication and authorization parts, which need to be taken care of separately, the security of microservices is like any application’s security. We need to pay attention to it.