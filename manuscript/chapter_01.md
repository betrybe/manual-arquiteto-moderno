# A importância de aprender conceitos ao invés de novos frameworks {#chapter_01}

Escrever este capítulo pode não ser uma das tarefas mais fáceis, uma vez que o público-alvo deste material é o de desenvolvedores(as) com alto nível de experiência (*Senior Developers*). A intenção, portanto, deste texto não é de um acadêmico dizer o que profissionais devem estudar, mas, principalmente, gerar uma autocrítica sobre muitos assuntos que poderiam ser explorados com maior profundidade (e até mesmo a aplicação da famosa expressão "ligar os pontos") enquanto estávamos em processo de formação (na universidade).

Pois bem, como iniciar? Basicamente, a formação de profissionais de desenvolvimento tem como base seu *background* em um curso de Ciência (ou Engenharia) da Computação (ou outro bacharelado na área). Muitas dessas respostas podem estar em disciplinas já esquecidas por nós, como estudantes, e mesmo por profissionais. 

O grande "mistério" que podemos tentar revelar aqui neste capítulo pode ser extremamente simples (ou tornar-se simples) a partir do momento em que identificamos o seguinte: as diferentes disciplinas que estudamos estão sempre interconectadas, mesmo que não pareça.

## Por que estudar Análise de Algoritmos, Estruturas de Dados, Sistemas Operacionais, Arquitetura de Computadores, Grafos, entre outros?

Podemos interconectar disciplinas de Análise de Algoritmos com Programação Orientada a Objetos e Bancos de dados? Sim! E de uma forma mais comum do que se pode imaginar:

Considere o exemplo de uma busca de objetos em um banco através de um ORM. Dependendo da aplicação, compartilhamento de recursos, escalabilidade e também concorrência, temos que pensar em eficiência de algoritmos mesmo antes de sua implementação. 

Ter consciência de que um `Lazy Fetch` com Hibernate, por exemplo pode gerar uma complexidade assintótica muito maior do que um `Eager Fetch` é fundamental para senior developers. Isso porque, por exemplo, em um sistema de recuperação de cabeçalhos de pedidos e seus respectivos itens, ocorre uma sequência de acessos ao banco de dados muito volumosa na primeira abordagem (*lazy*). Acessos para recuperar todos os cabeçalhos de pedidos e, para cada pedido, mais uma sequência de acessos ao banco de dados. Se observarmos um algoritmo com essas características, veremos algo próximo ao pseudocódigo a seguir:

	recuperar conjunto de pedidos e armazenar em listaP
	para cada pedido P em listaP faça
		recuperar itens I[] do pedido P a partir de seu ID
	fim-para

{pagebreak}

De forma bastante geral, a execução das instruções pode ser descrita da forma abaixo:

	1 execução da linha 1
	N+1 execuções da linha 2 (for sempre tem o teste que retorna falso)
	N execuções da linha 3

Desse modo, a complexidade desse algoritmo é de ordem O(n). Isso significa que, à medida que o conjunto de valores recuperados na linha 1 aumenta, o tempo computacional aumenta proporcionalmente.

Podemos extrapolar ainda esse algoritmo e, para cada item, recuperar toda a lista de impostos, que pode gerar um algoritmo de complexidade quadrática O(n2). Observe:

	recuperar conjunto de pedidos e armazenar em listaP
	para cada pedido P em listaP[] faça
	 	  	  recuperar itens I[] do pedido P a partir de seu ID
	      para cada item I de P, faça
	          recuperar lista L de impostos de I[]
	      fim-para
	fim-para

Nesse caso, analisando a complexidade assintótica, temos:

	1 execução da linha 1
	N+1 execuções da linha 2
	N execuções das linhas de 3 a 5, o que implica:
		1 execução da linha 3
		N+1 execuções da linha 4
		N execuções da linha 5

Portanto, se somarmos todas as execuções, temos 1 + (N+1) + N * (1 + (N+1) + N), o que resulta num polinômio cujo maior expoente é 2 (Nˆ2). Logo, nosso algoritmo de busca de pedidos, itens e impostos pode ser considerado um algoritmo de ordem quadrática. 
Isso porque apenas pensamos na recuperação da informação. Nem pensamos ainda na iteração desses valores, o que pode elevar ainda mais o tempo computacional. Agora imagine esse cenário em um sistema Web com algumas dezenas de milhares de pessoas conectadas. Se fizermos acessos ao disco (que é um dispositivo bastante lento, se comparado ao acesso à memória) de forma ineficiente para uma única requisição, como será o desempenho da aplicação para diversas requisições simultâneas?

A que conclusão podemos chegar depois de toda essa discussão? Talvez não seja tarefa do dia a dia de senior developers realizar a análise assintótica de algoritmos através da obtenção dos polinômios. Porém, para quem irá trabalhar com problemas mais complexos que a maioria dos casos do dia a dia, identificar complexidades (mesmo que de forma mais superficial, inicialmente) de algoritmos é fundamental para ter uma visão criteriosa de quais decisões tomar.

## A importância de um estudo mais aprofundado em disciplinas teóricas
Sabemos que Estruturas de Dados é um assunto que, para muitos(as) estudantes em formação, é tenebroso e extremamente abstrato. Porém é necessário e fundamental termos conhecimento das estruturas básicas e avançadas para também entendermos as ferramentas que utilizamos em sistemas atuais.

Quer exemplos? 

- **Filas**: Apache Kafka, Rabbit MQ, MQTT, entre outros, são exemplos de ferramentas que utilizam os conceitos de `filas` em suas implementações. Não apenas filas únicas, mas filas com prioridades, que também são objeto de estudo no período da graduação.
- **Pilhas**: Sua IDE faz uso de pilhas a todo momento para conferir se as chaves abertas foram fechadas corretamente (o *parser* da linguagem faz uso de pilhas quase que constantemente). Se os parênteses que definem as instruções em `Clojure` estão em conformidade. Isso apenas para citar exemplos imediatos. 

Além desses exemplos mais "simples" (e destaco a importância de se colocar o termo entre aspas), temos também exemplos mais complexos, que geram muita diferença de desempenho nas aplicações. Por exemplo: qual a diferença entre utilizarmos Listas (sejam elas Vetores ou listas ligadas) e Hash? A principal diferença está no desempenho da busca. Observe este pequeno exemplo de benchmark em Java:

```java
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
```

Esse simples algoritmo popula uma lista com 100.000 objetos do tipo produto e realiza 10.000 buscas de um produto inexistente nessa lista. Como é uma estrutura linear (e voltamos à análise de algoritmos), a busca obrigatoriamente tem que passar por todos os objetos até concluir que ele não existe na lista. Um algoritmo deste pode levar alguns bons segundos para executar (um teste em uma máquina comum pode levar entre 2 e 5 segundos para executar).
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

Não somente Hash, como também Árvores binárias, AVLs, ou mesmo B-Trees, para que dev sêniors tenham condições mínimas de definir estratégias de índices em tabelas de bancos de dados relacionais.

Agora, estou buscando valorizar bastante a Análise de Algoritmos, correto? Quer um exemplo de onde a Análise de Algoritmos pode não ser suficiente para uma solução?

Um algoritmo bastante comum na formação durante a graduação é o percurso e preenchimento de matrizes. Esse tipo de algoritmo, independente de o preenchimento ser via Linhas x Colunas ou Colunas x Linhas, na visão única e exclusiva da Análise de Algoritmos é irrelevante, pois ambos são de ordem assintótica O(nˆ2). Observe estes fragmentos de código Java:

{pagebreak}

**Fragmento1**: preenchimento Linha x Coluna


	for (int i=0; i < TAM; i++)
		for (int j=0; j < TAM; j++)
			matriz[i][j] = valor;

**Fragmento 2**: preenchimento Coluna x Linha

```
for (int i=0; i < TAM; i++)
	for (int j=0; j < TAM ; j++)
		matriz[j][i] = valor;
```

O aspecto interessante nesses códigos é que, na prática, o desempenho desses dois algoritmos é muito diferente, se a dimensão da matriz for considerável (aqui, para considerável, vamos pensar em valores acima de 5.000).

Mas por que a diferença de desempenho? Por conta do número de acessos ao cache. Se aprofundarmos um pouco mais o estudo e também levarmos em conta a Arquitetura de Computadores, veremos que uma matriz é alocada em memória como um grande array (a notação de matriz que as linguagens de programação utilizam é apenas uma abstração). Por conta disso (e levando em consideração o conceito da localidade referencial - negligenciado no estudo de Sistemas Operacionais), obviamente, se uma linha é trazida para o cache, é muito mais rápido preencher seus elementos adjacentes até que haja a necessidade de acesso a outras páginas de memória (gerando assim um *page fault*) do que ficar buscando elementos em regiões "distantes" da memória, aumentando - e muito - o volume de paginação.

Aliás, pode não parecer algo muito próximo do desenvolvimento Web atual levantarmos aspectos de Sistemas Operacionais e Arquitetura de Computadores, mas muitos desses conceitos de gerenciamento de memória e algoritmos de substituição de páginas estão, sim, presentes em ferramentas de desenvolvimento Web. Ferramentas de Cache (como REDIS, por exemplo) precisam definir uma política de atualização desses dados que serão expostos. Conhecer os algoritmos como o *MRU - Most Recently Used*, *LRU - Least Recently Used*, *MFU - Most Frequently Used*, entre outros, pode ser de fundamental importância para entender como uma ferramenta dessas trabalha, ou mesmo (na ausência de algo pronto) para implementar sua própria ferramenta de cache.

E o estudo de teoria dos Grafos? Sem os algoritmos de otimização vistos em Grafos (como o famoso *Algoritmo de Dijkstra*), não teríamos aplicações como Waze, por exemplo. Só que o ponto principal não se limita a apenas reproduzir o algoritmo, mas principalmente em encontrar um modelo que represente o que arestas e vértices significam no contexto do problema a ser resolvido. A distância entre dois vértices pode ser interpretada através da diferença efetiva em metros (ou quilômetros) ou, se baseada na velocidade dos automóveis e no tempo para se trafegar entre dois pontos, infere-se uma distância que pode ser dinâmica e, por consequência, atualizar o valor das arestas.

Não apenas Grafos, mas Autômatos e Compiladores, duas disciplinas fundamentais hoje para extração de textos através de expressões regulares (100% implementadas através de Autômatos Finitos Determinísticos - os famosos AFDs) e também Compiladores para a construção de novas linguagens (se observarmos a quantidade de linguagens que surgiram desde o ano 2000 até hoje, isso é muita coisa).

Mas você, como dev, pode se perguntar: "E quando eu farei uma nova linguagem?". Talvez a resposta seja "dificilmente", para não dizer "nunca"; mas, olhando um pouco além do dia a dia, desenvolvedores Front-End já devem ter utilizado frameworks como Angular ou React, correto? Pois bem, tais frameworks precisam fazer uso de análise de arquivos HTML com *tags* específicas que substituem valores para objetos. A leitura dessas tags (para posterior geração do código final da sua *single page application*) é feita por um *parser* que, a partir dos componentes criados, gera todo o arquivo HTML com as bibliotecas Javascript para que seu Aplicativo consiga funcionar. Talvez isso ajude a responder à questão que motiva este capítulo, na seção a seguir. 

{pagebreak}

## O ponto-chave: usar frameworks, fazer as tarefas manualmente, ou melhor, criar seu próprio?

Difícil de responder, porém com uma tendência a buscar desafios. Profissionais de desenvolvimento tornam-se sênior com mais rapidez quando entendem profundamente como as tecnologias, bibliotecas e frameworks funcionam nos bastidores. Portanto, tentaremos responder a essa pergunta (ou talvez até uma provocação) analisando-a por três diferentes visões.

**Visão do conhecimento**: Obviamente, você pode (e deve!) criar seu próprio framework pelo menos uma vez na vida, mesmo que a única pessoa a usar esse framework seja você. Qual o motivo? Este mesmo: conhecimento. Silvio Meira (um dos principais influenciadores em Computação do Brasil e do mundo) já disse em uma palestra que "*o profissional de computação que nunca desenvolveu um compilador não pode se considerar um profissional completo, pois um compilador exige que você conheça toda a grade de um curso de Ciência da Computação*". E ouso complementar: desenvolva também uma *Game Engine*, pois existe a parte de Álgebra Linear e Computação Gráfica que é pouco aplicada em um compilador. Um compilador, assim como uma Game Engine, exige que um dev *Ligue os Pontos* conceituais que sempre ficaram negligenciados.

**Visão Corporativa**: Muitas empresas podem adotar frameworks feitos pela comunidade? Sim, claro! Entretanto, existem decisões estratégicas dentro das corporações para que seus produtos não dependam de tecnologias de terceiros. Portanto, é muito comum haver times de desenvolvimento de frameworks (nos grandes *players*, principalmente) para uso interno em seus produtos. Isso proporciona controle total sobre a tecnologia que empregam. Quando esses frameworks estão maduros o suficiente, podem ficar disponíveis para a comunidade (em licenças *Open Source*) para que a comunidade faça bom uso e os popularize. Ou simplesmente ficam para uso interno e exclusivo, sem acesso pelas comunidades de desenvolvimento.

**Visão da Necessidade**: Este pode ser um agente motivador mais imediato, pois criar um framework tem por objetivo aumentar a produtividade no desenvolvimento de software. Quando os processos internos de desenvolvimento de equipe são bem definidos, é óbvio que os times começam a enxergar as necessidades comuns e gerar soluções de uso geral e propósito específico, sejam eles frameworks Back ou Front end, ORM ou mesmo em mais baixo nível para geração ou otimização de código. O principal objetivo de se construir uma tecnologia como essa é justamente investir tempo criando uma ferramenta que irá otimizar o tempo de desenvolvimento de uma aplicação (compare o tempo de desenvolvimento de uma camada de integração de Java com Bancos de Dados utilizando JDBC puro ou JPA, se ainda restam dúvidas).

Portanto, e para concluir, à medida que profissionais se tornam naturalmente seniores, seu conhecimento sobre as mais diversas áreas do conhecimento dentro da Ciência da Computação tende a tornar-se mais profundo e ao mesmo tempo mais amplo. Não basta conhecer um pouco sobre várias coisas e especializar-se em apenas uma delas. À medida que os problemas difíceis passam a ser mais comuns, profissionais tendem a ser expostos a situações que exigem muito conhecimento em muitas disciplinas diferentes, bem como a maneira como esses conhecimentos estão relacionados para a resolução do problema. O famoso "*ligar os pontos*".