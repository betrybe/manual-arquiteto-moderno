# The importance of learning concepts instead of new frameworks

Writing this chapter may not be one of the most straightforward tasks since developers with a high level of experience (senior developers) are the target audience for this material. The intention, therefore, is not for an academic to say what professionals should study. But, mainly, to generate self-criticism on many subjects that could be explored in greater depth (and even the application of the famous expression "connect the dots") while we were in the training process (at the university).

Well, how to start? A development professional's training is based on their background in a computer science (or engineering) course (or another bachelor's degree in the field). Many of these answers may be in subjects already forgotten by us as students and even by professionals.

The great "mystery" that we can try to reveal here in this chapter can be extremely simple (or become simple) from the moment we identify the following: the various disciplines we study are always interconnected, even if it doesn't seem so.

## Why study analysis of algorithms, data structure, operating systems, computer architecture, graphs, among others?

Can we interconnect the analysis of algorithms with object-oriented programming and databases? Yes! And in a more familiar way than you might imagine:

Consider the example of searching for objects in a database through an ORM. Depending on the application, resource sharing, scalability, and also competition, we have to think about algorithm efficiency even before its implementation.

Being aware that a `lazy fetch` with Hibernate, for instance, can generate much more asymptotic complexity than an `eager fetch` is paramount to a senior developer. That is because, for example, in a system for retrieving order headers and their respective items, a very large sequence of accesses to the database occurs in the first approach (lazy). Accesses to retrieve all order headers, and, for each order, one more sequence of accesses to the database. If we look at an algorithm with these characteristics, we will see something close to the following pseudocode:

	1. retrieve a set of orders and store them in listP
	2. for each order P in listP do
	3. retrieve items I[] from order P from its ID
	4. end-for

In a very general way, the execution of the instructions can be described as follows:

	1 execution of line 1
	N+1 executions of line 2 (for always has the test that returns false)
	N executions of line 3

Thus, the complexity of this algorithm is O(n). This means that, as the set of values recovered in line 1 increases, the computational time increases proportionally.

We can also extrapolate this algorithm and, for each item, retrieve the entire list of taxes, which can generate an algorithm of quadratic complexity O(n2). Look:

	1. retrieve a set of orders and store them in listP
	2. for each order P in listP[] do
	3. 		retrieve items I[] from order P from its ID
	4. 	for each item I of P, do
	5. 		retrieve list L of taxes from I[]
	6. 	end-for
	7. end-for

In this case, analyzing the asymptotic complexity, we have:

	1 execution of line 1
	N+1 executions of line 2
	N executions of lines 3 to 5, which implies:
		1 execution of line 3
		N+1 executions of line 4
		N executions of line 5

Therefore, if we add up all the executions, we have 1 + (N+1) + N * (1 + (N+1) + N), which results in a polynomial whose greatest exponent is 2 (Nˆ2). So, our order, item, and tax search algorithm can be considered a quadratic time algorithm. That's because we only think about retrieving information. We haven't even thought of these values iteration, which can further increase computational time. Now imagine this scenario on a web system with a few tens of thousands of users connected. If we access the disk (which is a very slow device compared to memory access) inefficiently for a single request, how will the application perform on several simultaneous requests?

What conclusion can we reach after all this discussion? It may not be a day-to-day task for a senior developer to perform asymptotic analysis of algorithms by obtaining the polynomials. However, for a developer who will work with more complex problems than most day-to-day cases, identifying complexities (even if in a more superficial way, initially) of algorithms is essential to make wise decisions.

## The importance of further study in theoretical disciplines

We know that Data Structure is a subject that, for many undergraduate students, is dark and extremely abstract. However, knowing basic and advanced structures is essential to understand the tools we use in current systems.

Want examples?
- **Queues**: Apache Kafka, Rabbit MQ, MQTT, among others, are examples of tools that use the concepts of `queues` in their implementations. Not only single queues but also priority queues, which are also the object of study during the undergraduate period.
- **Stacks**: Your IDE uses stacks all the time to check whether the open keys have been closed correctly (the language parser constantly uses stacks) or the parentheses that define the instructions in `Clojure` comply. That is just to cite immediate examples.

In addition to these more "simple" examples (and I highlight the importance of quoting the term), we have more complex ones, which generate many performance differences in applications. For example: what is the difference between using Lists (whether they are Vectors or linked lists) and hash? The main difference is in the search performance. Take a look at this small example of a Java benchmark :

		ArrayList<Product> list;
		list = new ArrayList<Product>();
		for (int i = 0; i < 100000; i++) {
		    Product p = new Product(i + 1, "Product " + (i + 1), 0, 0);
		    list.add(p);
		}
		int howManyFound=0; // number of occurrences found
		// start time measurement
		long start = System.currentTimeMillis();
		Product search;
		for (int cont = 0; cont < 10000; cont++) {
		    for (int i = 0; i < list.size(); i++) {
		        Product tmp = list.get(i);
		        if (tmp.getId() == -1) { // forcing the search of an nonexistent ID -> worst case
		             search = tmp;
		             howManyFound++;
		             break;
		         }
		     }
		 }
		 long end = System.currentTimeMillis();
		 // end time measurement
		 System.out.println("Found = " + howManyFound +" in "+(end-start));

That simple algorithm populates a list with 100,000 product-type objects and performs 10,000 searches for a product that does not exist in that list. As it is a linear structure (and we return to the analysis of algorithms), the search must go through all objects until it concludes that it does not exist in the list. An algorithm like this can take a few good seconds to run (a test on a regular machine can take between 2 and 5 seconds to run). Is it possible to improve the performance of this search? Of course. We could use, instead of linear search, a binary search algorithm. Nevertheless, for this strategy, our set would need to be previously ordered.

Now, if we think of another structure, like a HashMap, what is the advantage? Let's look at this code.

		HashMap<Integer, Product> map;
		map = new HashMap<Integer, Product>();
		for (int i = 0; i < 1000000; i++) {
		   Product p = new Product(i + 1, "Product " + (i + 1), 0, 0);
		   map.put(p.getId(), p);
		}
		int howManyFound=0;
		
		// start measurement
		long start = System.currentTimeMillis();
		for (int cont=0; cont< 10000; cont++) {
			Product search = map.get(-1); // again forcing the search of an nonexistent element
			if (search != null) {
				howManyFound++;
			}
		}
		long end = System.currentTimeMillis();
		// end measurement
		System.out.println("Found = "+howManyFound+" in "+(end-start));

By hash's definition, there is a calculation to determine what memory position it will occupy through the object's key attribute. Once this position has been determined, access is direct to the object (or the absence of it). Therefore, an object's computational access time in a hash map is O(1), i.e., constant!

Thus, regardless of the set's size, the access time will always be the same (which brings about an exceptional performance compared to the search performance in a list).

Not only hash, but also binary trees, AVLs, or even B-Trees, so that the senior developer has minimum conditions to define index strategies in relational database tables.

Now, I am placing a high value on the analysis of algorithms, right? Want an example where analysis of algorithms may not be enough for a solution?

A very common algorithm during graduation is the route and filling of matrices. This type of algorithm, regardless of whether it is filled in via rows x columns or columns x rows, in the unique and exclusive view of the analysis of algorithms, is irrelevant since both are asymptotic O(nˆ2). Look at these Java code snippets:

Snippet1: row x column filling

	for (int i=0; i < TAM; i++)
		for (int j=0; j < TAM; j++)
			matrix[i][j] = valor;

Fragment 2: filling column x row for (int i=0; i < TAM; i++) for (int j=0; j < TAM ; j++) matrix[j][i] = valor;

The interesting aspect about these codes is that, in practice, these two algorithms' performance is very different if the dimension of the matrix is ​​considerable (here, by considerable, we will think of values ​​above 5,000).

But why the performance difference? Due to the number of cache accesses. Let's go a little deeper into the study and also take computer architecture into account. We will see that a matrix is ​​allocated in memory as a large array (the matrix notation that programming languages ​​use is just an abstraction). That’s why (and taking into account the concept of locality of reference - neglected in the study of operating systems), obviously, if a row is brought into the cache, it is much faster to fill its adjacent elements until there is a need to access other memory pages (thus generating a page fault) than looking for elements in "distant" regions of memory, increasing - a lot - the pagination volume.

Considering operating systems and computer architecture may not seem very close to current web development. Still, many of these concepts of memory management and page replacement algorithms are, in fact, present in web development tools. Caching tools (such as REDIS) need to define an updating policy for this data that will be exposed. Knowing algorithms such as MRU - Most Recently Used, LRU - Least Recently Used, MFU - Most Frequently Used, among others, can be vital to understanding how such a tool works, or even (in the absence of something ready-to-use) to implement your own caching tool.

And the study of graph theory? Without the optimization algorithms seen in graphs (like the famous Dijkstra’s algorithm), we would not have applications like Waze, for example. But the key point is not limited to reproducing the algorithm, but mainly finding a model that represents what edges and vertices mean in the context of the problem to be solved. The distance between two vertices can be interpreted through the practical difference in meters (or kilometers) or, if based on the speed of the vehicles and the time to commute between two places, a distance, which can be dynamic, is inferred and, consequently, update the value of the edges.

Not only graphs, but automata and compilers, currently two fundamental disciplines for extracting texts through regular expressions (100% implemented through Deterministic Finite Automaton - the famous DFA) and also compilers for the construction of new languages ​​(if we look at the number of languages ​​that emerged since 2000, that's a lot).

But you, as a developer, may ask yourself, "when will I create a new language?" Perhaps the answer might be "hardly ever," not to say "never"; but, looking a little beyond the day-to-day, a front-end developer must have already used frameworks such as Angular or React, right? Well, such frameworks need to analyze HTML files with specific tags that replace values for objects. The reading of these tags (for a later generation of the final code of your single page application) is done by a parser that, from the created components, generates the entire HTML file with the Javascript libraries so that your application can work. Perhaps this will help answer the question that motivates this chapter in the following section.

## The key point: using frameworks, doing the tasks manually, or rather, creating your own?

Difficult to answer, but with a tendency to seek challenges. A developer becomes senior more quickly when they deeply understand how technologies, libraries, and frameworks work behind the scenes. Therefore, we will try to answer that question (or perhaps even a provocation) by analyzing it from three perspectives.

**Knowledge view**: obviously, you can (and should!) create your own framework at least once in your life, even if the only user of that framework is yourself. Why? This very one: knowledge. Silvio Meira (one of the leading influencers in computing in Brazil and the world) has already said in a talk that "*the computer professional who has never developed a compiler cannot consider themselves a complete professional, because a compiler requires you to know the entire syllabus of a computer science course*". And I dare to complement: develop a Game Engine too, as aspects of linear algebra and computer graphics are not often applied to a compiler. Like a game engine, a compiler requires the developer to *connect the conceptual dots* that have always been neglected.

**Corporate view**: can many companies adopt frameworks made by the community? Of course! However, there are strategic decisions within corporations so that their products do not depend on third-party technologies. Therefore, framework development teams are very common (mainly in the big players) for their products' internal use. This provides complete control over the technology they use. When these frameworks are mature enough, they can be made available and popularized by the community (under open source licenses). Or they are simply for internal and exclusive use, without access by development communities.

**Necessity view**: this can be a more immediate motivating agent because creating a framework aims to increase software development productivity. When the team's internal development processes are well defined, it is evident that the teams begin to see everyday needs and generate solutions for general use and specific purpose, whether they are back or front-end frameworks, ORM, or even at a lower level for code generation or optimization. The main objective of building technology like this is to invest time in creating a tool that will optimize the development time of an application (compare the development time of a Java database integration layer using pure JDBC or JPA, if there are still doubts).

Therefore, and to conclude, as the professional naturally becomes senior, their knowledge about the most diverse areas of expertise within computer science tends to become deeper and, at the same time, broader. Knowing a little about several things and specializing in just one of them is not enough. As complex problems become more common, the professional tends to be exposed to situations that require a lot of knowledge in various disciplines and how they are related to solving the problem. The famous "*connecting the dots*".