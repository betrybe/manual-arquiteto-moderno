# A importância de aprender conceitos ao invés de novos frameworks

Escrever este capítulo pode não ser uma das tarefas mais fáceis, uma vez que o público-alvo deste material é o de desenvolvedores com alto nível de experiência (*Senior Developers*). A intenção, portanto, deste texto não é de um acadêmico dizer o que um profissional deve estudar, mas, principalmente, gerar uma autocrítica sobre muitos assuntos que poderiam ser explorados com maior profundidade (e até mesmo a aplicação da famosa expressão "ligar os pontos") enquanto estávamos em processo de formação (na universidade).

Pois bem, como iniciar? Basicamente, a formação de um profissional de desenvolvimento tem como base seu *background* em um curso de Ciência (ou Engenharia) da Computação (ou outro bacharelado na área). Muitas dessas respostas podem estar em disciplinas já esquecidas por nós como estudantes, e mesmo pelos profissionais. 

O grande "mistério" que podemos tentar revelar aqui neste capítulo pode ser extremamente simples (ou tornar-se simples) a partir do momento em que identificamos o seguinte: as diferentes disciplinas que estudamos estão sempre interconectadas, mesmo que não pareça.

## Por que estudar Análise de Algoritmos, Estruturas de Dados, Sistemas Operacionais, Arquitetura de Computadores, Grafos, entre outros?

Podemos interconectar disciplinas de Análise de Algoritmos com Programação Orientada a Objetos e Bancos de dados? Sim! E de uma forma mais comum do que se pode imaginar:

Considere o exemplo de uma busca de objetos em um banco através de um ORM. Dependendo da aplicação, compartilhamento de recursos, escalabilidade e também concorrência, temos que pensar em eficiência de algoritmos mesmo antes de sua implementação. 

Ter consciência de que um `Lazy Fetch` com Hibernate, por exemplo, pode gerar uma complexidade assintótica muito maior do que um `Eager Fetch` é fundamental para um senior developer. Isso porque, por exemplo, em um sistema de recuperação de cabeçalhos de pedidos e seus respectivos itens, ocorre uma sequência de acessos ao banco de dados muito volumosa na primeira abordagem (*lazy*). Acessos para recuperar todos os cabeçalhos de pedidos, e, para cada pedido, mais uma sequência de acessos ao banco de dados. Se observarmos um algoritmo com essas características, veremos algo próximo ao pseudocódigo a seguir:

	1. recuperar conjunto de pedidos e armazenar em listaP
	2. para cada pedido P em listaP faça
	3. recuperar itens I[] do pedido P a partir de seu ID
	4. fim-para

De forma bastante geral, a execução das instruções pode ser descrita da forma abaixo:

	1 execução da linha 1
	N+1 execuções da linha 2 (for sempre tem o teste que retorna falso)
	N execuções da linha 3

Desse modo, a complexidade desse algoritmo é de ordem O(n). Isso significa que, à medida que o conjunto de valores recuperados na linha 1 aumenta, o tempo computacional aumenta proporcionalmente.

Podemos extrapolar ainda esse algoritmo e, para cada item, recuperar toda a lista de impostos, que pode gerar um algoritmo de complexidade quadrática O(n2). Observe:

	1. recuperar conjunto de pedidos e armazenar em listaP
	2. para cada pedido P em listaP[] faça
	3. 	   recuperar itens I[] do pedido P a partir de seu ID
	4.     para cada item I de P, faça
	5.          recuperar lista L de impostos de I[]
	6.     fim-para
	7. fim-para

Nesse caso, analisando a complexidade assintótica, temos:

	1 execução da linha 1
	N+1 execuções da linha 2
	N execuções das linhas de 3 a 5, o que implica:
		1 execução da linha 3
		N+1 execuções da linha 4
		N execuções da linha 5

Portanto, se somarmos todas as execuções, temos 1 + (N+1) + N * (1 + (N+1) + N), o que resulta num polinômio cujo maior expoente é 2 (Nˆ2). Logo, nosso algoritmo de busca de pedidos, itens e impostos pode ser considerado um algoritmo de ordem quadrática. Isso porque apenas pensamos na recuperação da informação. Nem pensamos ainda na iteração desses valores, o que pode elevar ainda mais o tempo computacional. Agora imagine esse cenário em um sistema Web com algumas dezenas de milhares de usuários conectados. Se fizermos acessos ao disco (que é um dispositivo bastante lento, se comparado ao acesso à memória) de forma ineficiente para uma única requisição, como será o desempenho da aplicação para diversas requisições simultâneas?

A que conclusão podemos chegar depois de toda essa discussão? Talvez não seja tarefa do dia a dia de um desenvolvedor sênior realizar a análise assintótica de algoritmos através da obtenção dos polinômios. Porém, para um desenvolvedor que irá trabalhar com problemas mais complexos que a maioria dos casos do dia a dia, identificar complexidades (mesmo que de forma mais superficial, inicialmente) de algoritmos é fundamental para ter uma visão criteriosa de quais decisões tomar.

## A importância de um estudo mais aprofundado em disciplinas teóricas

Sabemos que Estruturas de Dados é um assunto que, para muitos estudantes em formação, é tenebroso e extremamente abstrato. Porém a necessidade de termos conhecimento das estruturas básicas e avançadas é fundamental para também entendermos as ferramentas que utilizamos em sistemas atuais.

Quer exemplos? 
- **Filas**: Apache Kafka, Rabbit MQ, MQTT, entre outros são exemplos de ferramentas que utilizam os conceitos de `filas` em suas implementações. Não apenas filas únicas, mas filas com prioridades, que também são objeto de estudo no período da graduação.
- **Pilhas**: Sua IDE faz uso de pilhas a todo momento para conferir se as chaves abertas foram fechadas corretamente (o *parser* da linguagem faz uso de pilhas quase que constantemente). Se os parênteses que definem as instruções em `Clojure` estão em conformidade. Isso apenas para citar exemplos imediatos. 

Além desses exemplos mais "simples" (e destaco a importância de se colocar o termo entre aspas), temos também exemplos mais complexos, que geram muita diferença de desempenho nas aplicações. Por exemplo: qual a diferença entre utilizarmos Listas (sejam elas Vetores ou listas ligadas) e Hash? A principal diferença está no desempenho da busca. Observe este pequeno exemplo de benchmark em Java:

		ArrayList<Produto> lista;
		lista = new ArrayList<Produto>();
		for (int i = 0; i < 100000; i++) {
		    Produto p = new Produto(i + 1, "Produto " + (i + 1), 0, 0);
		    lista.add(p);
		}
		int quantosAchei=0; // numero de ocorrencias encontradas
		// inicio da medicao do tempo
		long inicio = System.currentTimeMillis();
		Produto busca;
		for (int cont = 0; cont < 10000; cont++) {
		    for (int i = 0; i < lista.size(); i++) {
		        Produto tmp = lista.get(i);
		        if (tmp.getId() == -1) { // forcando buscar um ID que nao existe na lista -> o pior caso
		             busca = tmp;
		             quantosAchei++;
		             break;
		         }
		     }
		 }
		 long fim = System.currentTimeMillis();
		 // fim da medicao do tempo
		 System.out.println("Achei = " + quantosAchei +" em "+(fim-inicio));

Este simples algoritmo popula uma lista com 100.000 objetos do tipo produto e realiza 10.000 buscas de um produto inexistente nessa lista. Como é uma estrutura linear (e voltamos à análise de algoritmos), a busca obrigatoriamente tem que passar por todos os objetos até concluir que ele não existe na lista. Um algoritmo deste pode levar alguns bons segundos para executar (um teste em uma máquina comum pode levar entre 2 e 5 segundos para executar).
É possível melhorar o desempenho dessa busca? Claro que sim. Poderíamos usar, ao invés de busca linear, um algoritmo de busca binária. Entretanto, para essa estratégia, nosso conjunto precisaria estar previamente ordenado. 

Agora, se pensarmos em outra estrutura, como um mapa Hash, qual a vantagem? Vamos observar este código.

		HashMap<Integer, Produto> mapa;
		mapa = new HashMap<Integer, Produto>();
		for (int i = 0; i < 1000000; i++) {
		   Produto p = new Produto(i + 1, "Produto " + (i + 1), 0, 0);
		   mapa.put(p.getId(), p);
		}
		int quantosAchei=0;
		
		// inicio da medicao
		long inicio = System.currentTimeMillis();
		for (int cont=0; cont< 10000; cont++) {
			Produto busca = mapa.get(-1); // novamente forcando a busca de um elemento que nao existe
			if (busca != null) {
				quantosAchei++;
			}
		}
		long fim = System.currentTimeMillis();
		// fim da medicao
		System.out.println("Achei = "+quantosAchei+" em "+(fim-inicio));

Pela própria definição de Hash, há um cálculo para determinar, através de um atributo-chave do objeto, qual a posição de memória que ele irá ocupar. Uma vez determinada essa posição, o acesso é direto ao objeto (ou à inexistência dele). Portanto, o tempo computacional de acesso de um objeto em um mapa hash é O(1), ou seja, constante!

Desse modo, independentemente do tamanho do conjunto, o tempo de acesso sempre será o mesmo (o que traz um desempenho excepcional, se comparado ao desempenho de busca em uma lista). 

Não somente Hash, como também Árvores binárias, AVLs, ou mesmo B-Trees, para que o desenvolvedor sênior tenha condições mínimas de definir estratégias de índices em tabelas de bancos de dados relacionais.

Agora, estou buscando valorizar bastante a Análise de Algoritmos, correto? Quer um exemplo de onde a Análise de Algoritmos pode não ser suficiente para uma solução?

Um algoritmo bastante comum na formação durante a Graduação é o percurso e preenchimento de matrizes. Esse tipo de algoritmo, independente de o preenchimento ser via Linhas x Colunas ou Colunas x Linhas, na visão única e exclusiva da Análise de Algoritmos é irrelevante, pois ambos são de ordem assintótica O(nˆ2). Observe estes fragmentos de código Java:

Fragmento1: preenchimento Linha x Coluna


	for (int i=0; i < TAM; i++)
		for (int j=0; j < TAM; j++)
			matriz[i][j] = valor;

   Fragmento 2: preenchimento Coluna x Linha
	   for (int i=0; i < TAM; i++)
		   for (int j=0; j < TAM ; j++)
			   matriz[j][i] = valor;

O aspecto interessante nesses códigos é que, na prática, o desempenho desses dois algoritmos é muito diferente, se a dimensão da matriz for considerável (aqui, para considerável, vamos pensar em valores acima de 5.000).

Mas por que a diferença de desempenho? Por conta do número de acessos ao cache. Se aprofundarmos um pouco mais o estudo e também levarmos em conta a Arquitetura de Computadores, veremos que uma matriz é alocada em memória como um grande array (a notação de matriz que as linguagens de programação utilizam é apenas uma abstração). Por conta disso (e levando em consideração o conceito da localidade referencial - negligenciado no estudo de Sistemas Operacionais), obviamente, se uma linha é trazida para o cache, é muito mais rápido preencher seus elementos adjacentes até que haja a necessidade de acesso a outras páginas de memória (gerando assim um *page fault*) do que ficar buscando elementos em regiões "distantes" da memória, aumentando - e muito - o volume de paginação.

Aliás, pode não parecer algo muito próximo do desenvolvimento Web atual levantarmos aspectos de Sistemas Operacionais e Arquitetura de Computadores, mas muitos desses conceitos de gerenciamento de memória e algoritmos de substituição de páginas estão, sim, presentes em ferramentas de desenvolvimento Web. Ferramentas de Cache (como REDIS, por exemplo) precisam definir uma política de atualização desses dados que serão expostos. Conhecer os algoritmos como o *MRU - Most Recently Used*, *LRU - Least Recently Used*, *MFU - Most Frequently Used*, entre outros, pode ser de fundamental importância para entender como uma ferramenta dessas trabalha, ou mesmo (na ausência de algo pronto) para implementar sua própria ferramenta de cache.

E o estudo de teoria dos Grafos? Sem os algoritmos de otimização vistos em Grafos (como o famoso *Algoritmo de Djikstra*), não teríamos aplicações como Waze, por exemplo. Só que o ponto principal não se limita a apenas reproduzir o algoritmo, mas principalmente em encontrar um modelo que represente o que arestas e vértices significam no contexto do problema a ser resolvido. A distância entre dois vértices pode ser interpretada através da diferença efetiva em metros (ou quilômetros) ou, se baseada na velocidade dos automóveis e o tempo para se trafegar entre dois pontos, infere-se uma distância que pode ser dinâmica e, por consequência, atualizar o valor das arestas.

Não apenas Grafos, mas Autômatos e Compiladores, duas disciplinas fundamentais hoje para extração de textos através de expressões regulares (100% implementadas através de Autômatos Finitos Determinísticos - os famosos AFDs) e também Compiladores para a construção de novas linguagens (se observarmos a quantidade de linguagens que surgiram desde o ano 2000 até hoje, isso é muita coisa).

Mas você, como desenvolvedor, pode se perguntar: "E quando eu farei uma nova linguagem?". Talvez a resposta para essa pergunta seja "dificilmente", para não dizer "nunca"; mas, olhando um pouco além do dia a dia, um desenvolvedor Front-End já deve ter utilizado frameworks como Angular ou React, correto? Pois bem, tais frameworks precisam fazer uso de análise de arquivos HTML com *tags* específicas que substituem valores para objetos. A leitura dessas tags (para posterior geração do código final da sua *single page appplication*) é feita por um *parser* que, a partir dos componentes criados, gera todo o arquivo HTML com as bibliotecas Javascript para que seu Aplicativo consiga funcionar. Talvez isso ajude a responder à questão que motiva este capítulo, na seção a seguir. 

## O ponto-chave: usar frameworks, fazer as tarefas manualmente, ou melhor, criar seu próprio?

Difícil de responder, porém com uma tendência a buscar desafios. Um desenvolvedor torna-se sênior com mais rapidez quando entende profundamente como as tecnologias, bibliotecas e frameworks funcionam nos bastidores. Portanto, tentaremos responder a essa pergunta (ou talvez até uma provocação) analisando-a por três diferentes visões.

**Visão do conhecimento**: Obviamente, você pode (e deve!) criar seu próprio framework pelo menos uma vez na vida, mesmo que o único usuário desse framework seja você mesmo. Qual o motivo? Este mesmo: conhecimento. Silvio Meira (um dos principais influenciadores em Computação do Brasil e do mundo) já disse em uma palestra que "*o profissional de computação que nunca desenvolveu um compilador não pode se considerar um profissional completo, pois um compilador exige que você conheça toda a grade de um curso de Ciência da Computação*". E ouso complementar o Silvio: desenvolva também uma *Game Engine*, pois existe a parte de Álgebra Linear e Computação Gráfica que é pouco aplicada em um compilador. Um compilador, assim como uma Game Engine, exige que o desenvolvedor *Ligue os Pontos* conceituais que sempre ficaram negligenciados.

**Visão Corporativa**: Muitas empresas podem adotar frameworks feitos pela comunidade? Sim, claro! Entretanto, existem decisões estratégicas dentro das corporações para que seus produtos não dependam de tecnologias de terceiros. Portanto, é muito comum haver times de desenvolvimento de frameworks (nos grandes *players*, principalmente) para uso interno em seus produtos. Isso proporciona controle total sobre a tecnologia que empregam. Quando esses frameworks estão maduros o suficiente, podem ficar disponíveis para a comunidade (em licenças *Open Source*) para que a comunidade faça bom uso e os popularizem. Ou simplesmente ficam para uso interno e exclusivo, sem acesso pela comunidade de desenvolvedores.

**Visão da Necessidade**: Este pode ser um agente motivador mais imediato, pois criar um framework tem por objetivo aumentar a produtividade no desenvolvimento de software. Quando os processos internos de desenvolvimento da equipe são bem definidos, é óbvio que os times começam a enxergar as necessidades comuns e gerar soluções de uso geral e propósito específico, sejam eles frameworks Back ou Front end, ORM ou mesmo em mais baixo nível para geração ou otimização de código. O principal objetivo de se construir uma tecnologia como essa é justamente investir tempo criando uma ferramenta que irá otimizar o tempo de desenvolvimento de uma aplicação (compare o tempo de desenvolvimento de uma camada de integração de Java com Bancos de Dados utilizando JDBC puro ou JPA, se ainda restam dúvidas).

Portanto, e para concluir, à medida que o profissional se torna-se naturalmente sênior, seu conhecimento sobre as mais diversas àreas do conhecimento dentro da Ciência da Computação tende a tornar-se mais profundo e ao mesmo tempo mais amplo. Não basta conhecer um pouco sobre várias coisas e especializar-se em apenas uma delas. À medida que os problemas difíceis passam a ser mais comuns, o profissional tende a ser exposto a situações que exigem muito conhecimento em muitas disciplinas diferentes, bem como a maneira como esses conhecimentos estão relacionados para a resolução do problema. O famoso "*ligar os pontos*".

EN

# The importance of learning concepts instead of new frameworks

Writing this chapter may not be one of the most straightforward tasks since developers with a high level of experience (senior developers) are the target audience for this material. The intention, therefore, is not for an academic to say what a professional should study. But, mainly, to generate self-criticism on many subjects that could be explored in greater depth (and even the application of the famous expression "connect the dots") while we were in the training process (at the university).

Well, how to start? A development professional's training is based on their background in a computer science (or engineering) course (or another bachelor's degree in the field). Many of these answers may be in subjects already forgotten by us as students and even as professionals.

The great "mystery" that we can try to reveal here in this chapter can be extremely simple (or become simple) from the moment we identify the following: the various disciplines we study are always interconnected, even if it does not seem so.

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
N + 1 executions of line 4
N executions of line 5

Therefore, if we add up all the executions, we have 1 + (N+1) + N * (1 + (N+1) + N), which results in a polynomial whose greatest exponent is 2 (Nˆ2). So, our order, item, and tax search algorithm can be considered a quadratic time algorithm. That's because we only think about retrieving information. We haven't even thought of these values iteration, which can further increase computational time. Now imagine this scenario on a web system with a few tens of thousands of users connected. If we access the disk (which is a very slow device compared to memory access) inefficiently for a single request, how will the application perform on several simultaneous requests?

What conclusion can we reach after all this discussion? It may not be a day-to-day task for a senior developer to perform asymptotic analysis of algorithms by obtaining the polynomials. However, for a developer who will work with more complex problems than most day-to-day cases, identifying complexities (even if in a more superficial way, initially) of algorithms is essential to make wise decisions.

## The importance of further study in theoretical disciplines

We know that Data Structure is a subject that, for many undergraduate students, is dark and extremely abstract. However, knowing basic and advanced structures is essential to understand the tools we use in current systems.

Want examples?
- **Queues**: Apache Kafka, Rabbit MQ, MQTT, among others, are examples of tools that use the concepts of `queues` in their implementations. Not only single queues but also priority queues, which are also the object of study during the undergraduate period.
- **Stacks**: Your IDE uses stacks all the time to check whether the open keys have been closed correctly (the language *parser* constantly uses stacks) or the parentheses that define the instructions in `Clojure` comply. That is just to cite immediate examples.

In addition to these more "simple" examples (and I highlight the importance of quoting the term), we have more complex ones, which generate many performance differences in applications. For example: what is the difference between using Lists (whether they are Vectors or linked lists) and hash? The main difference is in the search performance. Take a look at this small example of a Java benchmark :

ArrayList<Produto> lista;
	lista = new ArrayList<Produto>();
	for (int i = 0; i < 100000; i++) {
	    Produto p = new Produto(i + 1, "Produto " + (i + 1), 0, 0);
	    lista.add(p);
	}
	int quantosAchei=0; // numero de ocorrencias encontradas
	// inicio da medicao do tempo
	long inicio = System.currentTimeMillis();
	Produto busca;
	for (int cont = 0; cont < 10000; cont++) {
	    for (int i = 0; i < lista.size(); i++) {
	        Produto tmp = lista.get(i);
	        if (tmp.getId() == -1) { // forcando buscar um ID que nao existe na lista -> o pior caso
	             busca = tmp;
	             quantosAchei++;
	             break;
	         }
	     }
	 }
	 long fim = System.currentTimeMillis();
	 // fim da medicao do tempo
	 System.out.println("Achei = " + quantosAchei +" em "+(fim-inicio));

This simple algorithm populates a list with 100,000 product-type objects and performs 10,000 searches for a product that does not exist in that list. As it is a linear structure (and we return to the analysis of algorithms), the search must go through all objects until it concludes that it does not exist in the list. An algorithm like this can take a few good seconds to run (a test on a regular machine can take between 2 and 5 seconds to run). Is it possible to improve the performance of this search? Of course. We could use, instead of linear search, a binary search algorithm. Nevertheless, for this strategy, our set would need to be previously ordered.

Now, if we think of another structure, like a HashMap, what is the advantage? Let's look at this code.

HashMap<Integer, Produto> mapa;
	mapa = new HashMap<Integer, Produto>();
	for (int i = 0; i < 1000000; i++) {
	   Produto p = new Produto(i + 1, "Produto " + (i + 1), 0, 0);
	   mapa.put(p.getId(), p);
	}
	int quantosAchei=0;
	
	// inicio da medicao
	long inicio = System.currentTimeMillis();
	for (int cont=0; cont< 10000; cont++) {
		Produto busca = mapa.get(-1); // novamente forcando a busca de um elemento que nao existe
		if (busca != null) {
			quantosAchei++;
		}
	}
	long fim = System.currentTimeMillis();
	// fim da medicao
	System.out.println("Achei = "+quantosAchei+" em "+(fim-inicio));

By hash's definition, there is a calculation to determine what memory position it will occupy through the object's key attribute. Once this position has been determined, access is direct to the object (or the absence of it). Therefore, an object's computational access time in a hash map is O(1), i.e., constant!

Thus, regardless of the set's size, the access time will always be the same (which brings about an exceptional performance compared to the search performance in a list).

Not only hash, but also binary trees, AVLs, or even B-Trees, so that the senior developer has minimum conditions to define index strategies in relational database tables.

Now, I am placing a high value on the analysis of algorithms, right? Want an example where analysis of algorithms may not be enough for a solution?

A very common algorithm during graduation is the route and filling of matrices. This type of algorithm, regardless of whether it is filled in via rows x columns or columns x rows, in the unique and exclusive view of the analysis of algorithms, is irrelevant since both are asymptotic O(nˆ2). Look at these Java code snippets:

Snippet1: row x column filling

	for (int i=0; i < TAM; i++)
	for (int j=0; j < TAM; j++)
		matriz[i][j] = valor;

Fragment 2: filling column x row for (int i=0; i < TAM; i++) for (int j=0; j < TAM ; j++) matriz[j][i] = valor;

The interesting aspect about these codes is that, in practice, these two algorithms' performance is very different if the dimension of the matrix is ​​considerable (here, by considerable, we will think of values ​​above 5,000).

But why the performance difference? Due to the number of cache accesses. Let's go a little deeper into the study and also take computer architecture into account. We will see that a matrix is ​​allocated in memory as a large array (the matrix notation that programming languages ​​use is just an abstraction). That’s why (and taking into account the concept of locality of reference - neglected in the study of operating systems), obviously, if a row is brought into the cache, it is much faster to fill its adjacent elements until there is a need to access other memory pages (thus generating a page fault) than looking for elements in "distant" regions of memory, increasing - a lot - the pagination volume.

Considering operating systems and computer architecture may not seem very close to current web development. Still, many of these concepts of memory management and page replacement algorithms are, in fact, present in web development tools. Caching tools (such as REDIS) need to define an updating policy for this data that will be exposed. Knowing algorithms such as MRU - Most Recently Used, LRU - Least Recently Used, MFU - Most Frequently Used, among others, can be vital to understanding how such a tool works, or even (in the absence of something ready) to implement your own caching tool.

And the study of graph theory? Without the optimization algorithms seen in graphs (like the famous Dijkstra’s algorithm), we would not have applications like Waze, for example. But the key point is not limited to reproducing the algorithm, but mainly finding a model that represents what edges and vertices mean in the context of the problem to be solved. The distance between two vertices can be interpreted through the practical difference in meters (or kilometers) or, if based on the speed of the vehicles and the time to commute between two places, a distance, which can be dynamic, is inferred and, consequently, update the value of the edges.

Not only graphs, but automata and compilers, currently two fundamental disciplines for extracting texts through regular expressions (100% implemented through Deterministic Finite Automaton - the famous DFA) and also compilers for the construction of new languages ​​(if we look at the number of languages ​​that emerged since 2000, that's a lot).

But you, as a developer, may ask yourself, "when will I create a new language?" Perhaps the answer to this question might be "hardly ever," not to say "never"; but, looking a little beyond the day-to-day, a front-end developer must have already used frameworks such as Angular or React, right? Well, such frameworks need to analyze HTML files with specific tags that replace values for objects. The reading of these tags (for a later generation of the final code of your single page application) is done by a parser that, from the created components, generates the entire HTML file with the Javascript libraries so that your application can work. Perhaps this will help answer the question that motivates this chapter in the following section.

## The key point: using frameworks, doing the tasks manually, or rather, creating your own?

Difficult to answer, but with a tendency to seek challenges. A developer becomes senior more quickly when they deeply understand how technologies, libraries, and frameworks work behind the scenes. Therefore, we will try to answer that question (or perhaps even a provocation) by analyzing it from three perspectives.

**Knowledge view**: obviously, you can (and should!) create your own framework at least once in your life, even if the only user of that framework is yourself. Why? This very one: knowledge. Silvio Meira (one of the leading influencers in computing in Brazil and the world) has already said in a talk that "*the computer professional who has never developed a compiler cannot consider themselves a complete professional, because a compiler requires you to know the entire syllabus of a computer science course*". And I dare to complement Silvio: develop a *Game Engine* too, as aspects of linear algebra and computer graphics are not often applied to a compiler. Like a game engine, a compiler requires the developer to *connect the conceptual dots * that have always been neglected.

**Corporate view**: can many companies adopt frameworks made by the community? Of course! However, there are strategic decisions within corporations so that their products do not depend on third-party technologies. Therefore, framework development teams are very common (mainly in the big players) for their products' internal use. This provides complete control over the technology they use. When these frameworks are mature enough, they can be made available and popularized by the community (under open source licenses). Or they are simply for internal and exclusive use, without access to the developer community.

**Necessity view**: this can be a more immediate motivating agent because creating a framework aims to increase software development productivity. When the team's internal development processes are well defined, it is evident that the teams begin to see everyday needs and generate solutions for general use and specific purpose, whether they are back or front-end frameworks, ORM, or even at a lower level for code generation or optimization. The main objective of building technology like this is to invest time in creating a tool that will optimize the development time of an application (compare the development time of a Java database integration layer using pure JDBC or JPA, if there are still doubts).

Therefore, and to conclude, as the professional naturally becomes senior, their knowledge about the most diverse areas of expertise within computer science tends to become deeper and, at the same time, broader. Knowing a little about several things and specializing in just one of them is not enough. As complex problems become more common, the professional tends to be exposed to situations that require a lot of knowledge in many various disciplines and how they are related to solving the problem. The famous "*connecting the dots*".

cut capitalizing areas of study
checar os itálicos *
