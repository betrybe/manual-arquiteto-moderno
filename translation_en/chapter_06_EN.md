PT 

# NoSQL vs. SQL

Muitas das vezes, quando iniciamos um debate sobre SQL (Structured Query Language) e NoSQL no meio técnico, já podemos esperar o surgimento de discussões acaloradas. Isso se deve ao fato, principalmente, de que existe uma idéia de que há uma guerra, uma rixa, etc., que precisa ser vencida entre os usuários destas tecnologias. Já queremos deixar claro, não existe guerra alguma! Vamos desmistificar esse mito?

Para começar, precisamos falar um pouquinho de história. Com isto, entenderemos os detalhes sobre o NoSQL e suas diferenças para o SQL.

Assim como sempre ocorre no universo da tecnologia, nós criamos uma solução para resolver um problema específico. Vamos voltar mais de quarenta anos no tempo, para entender o problema existente, no contexto dos bancos de dados:

- Para que pudessem ser utilizados posteriormente, os dados produzidos pelos computadores deveriam ficar armazenados e não poderiam ser perdidos quando as máquinas fossem desligadas.

Porém, naquela época, a tecnologia de armazenamento estava engatinhando e os discos rígidos eram muito caros. Foi necessário então se estruturar e normalizar os dados que seriam gravados nestes discos. Com a adoção dessa prática, os dados armazenados utilizariam menos espaço e, consequentemente, trariam um aproveitamento melhor de recursos, gerando economia ou a possibilidade de se armazenar maior quantidade de dados em um mesmo espaço. 

Este foi o contexto em que os bancos de dados relacionais foram criados, inclusive implementando todos os conceitos que foram detalhado no capítulo anterior, como, formas normais e estrutura de dados bem definidas.

O tempo passou, nossa tecnologia evoluiu e, com essa evolução, mais um problema surgiu: produzimos dados em uma quantidade muito grande, muitas vezes de forma desestruturada e também descentralizada com sistemas cada vez mais distribuídos. 

**TIP:** _Estes dados são chamados de *desestruturados* por terem origem em diversas fontes, como, sensores IOT (_Internet of Things_, i.e. geladeiras conectadas à internet, relógios inteligentes e carros autônomos), imagens e documentos não catalogados, dentre outros exemplos._

Estruturar, isto é, organizar os dados provenientes de fontes como estas era (e é) possível, porém iria requerer muito tempo. Este tempo extra impactaria o processo de desenvolvimento e entrega de software, e consequentemente levava as empresas a perderem o time to market da solução sendo criada. Esse problema precisava ser resolvido e, assim, nasceram os bancos de dados NoSQL!

O termo **NoSQL** foi originalmente criado em 1998 por Carlo Strozzi e posteriormente re-introduzido por Eric Evans em 2009, quando este participou da organização de um evento para discutir bancos de dados **open source** e **distribuídos**. E por falar em bancos distribuídos, esse é um conceito amplamente utilizado pelos bancos NoSQL: basicamente, os bancos NoSQL são bancos de dados que operam em computação distribuída, o que impulsiona um significativo grando de escalabilidade e performance. 

**TIP:** Para entender um um pouco mais sobre computação distribuída, recomendamos a leitura do seguinte artigo: [Paradigma da computação distribuída](https://imasters.com.br/arquitetura-da-informacao/paradigma-da-computacao-distribuida).

## NoSQL

### O que significa NoSQL?

Não existe uma definição, digamos, "oficial" para o que realmente este termo significa, mas particularmente gosto da seguinte: **N**ot **O**nly **SQL**, ou seja, "Não Somente SQL". Essa definição enfatiza que estes bancos podem utilizar linguagens semelhantes ao [SQL ANSI](https://pt.wikipedia.org/wiki/SQL) para realizar consultas e demais operações e não somente o SQL em si.

### Particularidades do NoSQL

Quando você estudou sobre bancos de dados relacionais, viu que esses bancos são baseados no conceito **ACID** (**A**tomicity, **C**onsistency, **I**solation, **D**urability). Os bancos de dados NoSQL, em sua grande maioria, baseiam-se em um outro conceito: o **BASE** (**Ba**se Availability, **S**oft State, and **E**ventually Consistent).

Antes de detalhar cada ponto do conceito **BASE**, você precisa entender a representação de um termo no contexto de banco de dados que verá ao longo deste capítulo: **cluster**. Um cluster, neste contexto, se refere à capacidade de um conjunto de **servidores** (de banco) ou **instâncias** (de banco) se conectarem a um banco de dados. Uma **instância** é uma coleção de memória e processos que interagem com o banco de dados, que é o conjunto de arquivos físicos que efetivamente armazenam os dados.

Devemos descatar duas principais vantagens de um cluster, especialmente em ambientes de banco de dados de alto volume:

* **Tolerância a falhas** (_Fault Tolerance_): como há mais de um servidor ou instância para os usuários se conectarem, o cluster de bancos de dados oferece uma alternativa no caso de falha em um servidor. Quando se lida com dezenas de milhares de máquinas em um único [_data center_](https://pt.wikipedia.org/wiki/Centro_de_processamento_de_dados), tais falhas são um problema presente;

* **Balanceamento de carga** (_Load Balancing_): o cluster geralmente é configurado para permitir que os usuários sejam automaticamente alocados ao servidor com o mínimo de uso para que, assim, se otimize o uso da estrutura disponível para o banco.

#### Detalhando o conceito BASE

- Base Availability - **BA**
    - O banco de dados aparenta funcionar o tempo todo. Como existe a implementação do conceito de cluster, se um servidor falhar, o banco continuará funcionando por conta de outro servidor que suprirá essa falha;

- Soft State - **S**
    - Não é necessário estar consistente o tempo todo. Ou seja: com um banco distribuído em várias máquinas e todas sendo usadas com igual frequência para escrita e consulta, é possível que, em dado momento, uma máquina receba uma escrita e não tenha tido tempo de "repassar" essa escrita para as demais máquinas do banco. Assim, se um usuário consultar a máquina que já foi atualizada e  outro o fizer numa máquina menos atualizada os resultados, que deveriam ser iguais, serão diferentes. Imagine a sua _timeline_ do **Facebook**: nela são exibidos os posts de seus amigos, porém nem todos os posts são exibidos exatamente ao mesmo tempo. Neste caso, o que acontece é que a informação foi enviada ao banco de dados, mas nem todos os servidores do cluster têm essa mesma informação ao mesmo tempo. Isso permite que o banco de dados possa gerenciar mais informações de escrita sem ter que se preocupar em replicá-las em uma mesma operação;

- Eventually Consistent - **E**
    - O sistema se torna consistente em algum momento, em outras palavras, ele eventualmente se tornará consistente. Como não temos a informação replicada "instantaneamente", este conceito se encarrega de deixar o banco consistente "ao seu tempo". Isso porque, dependendo das configurações do cluster, essa replicação pode acontecer mais rapidamente ou não. Mas em algum momento as informações estarão consistentes e presentes em todos os servidores do cluster.

Para finalizar, uma outra particularidade marcante de bancos não estruturados é a ausência da feature de `schema` ou `schema flexível`. Isso quer dizer que não há necessidade de definição prévia do `schema` dos dados. Se por um lado isso torna mais dinâmico o processo de inclusão de novos atributos, por outro, pode impactar a integridade desses dados. Não se preocupe: a seu tempo, todos esses conceitos ficarão bem mais claros.

---

### NoSQL e suas classes

Os bancos de dados NoSQL podem ser categorizados em quatro tipos (que no contexto de banco de dados, são chamados de **classes**):

* Chave / Valor - `Key / Value`
* Família de Colunas - `Column Family`
* Documentos - `Document`
* Grafos - `Graph`

Cada classe acima possui uma aplicação diferente, portanto, devemos entender as características de cada classe para tirar o melhor proveito de cada uma e saber quando escolhe-las.

#### Classe `Key / Value`

![Estrutura de Chave-valor](images/chapter_06_01.png "Estrutura de Chave-valor")

Os bancos do tipo chave-valor possuem uma estrutura similar à da classe `java.util.Map` do Java, ou seja, a *informação* (`value`) será recuperada apenas pela *chave* (`key`). Este tipo de banco de dados pode ser utilizado, por exemplo, para gerenciar sessões de usuários. Outro exemplo de utilização é aliado a um DNS, onde a chave é o endereço, por exemplo, `www.google.com` e o valor é o IP desse servidor.

Atualmente existem diversas implementações de banco de dados do tipo chave-valor, dentre os quais os mais famosos são:

* AmazonDynamo
* AmazonS3
* Redis
* Scalaris
* Voldemort

Comparando o banco de dados relacional com o do tipo chave-valor, podemos elencar alguns pontos de atenção. O primeiro é que a estrutura do chave-valor é bastante simples:

| Estrutura relacional | Estrutura chave-valor |
| -------------------- | --------------------- |
| Table                | Bucket                |
| Row                  | Key/value pair        |
| Column               | ----                  |
| Relationship         | ----                  |

Nesta classe de banco, não é possível realizar operações como `join` entre os `buckets` e o valor é composto por um grande bloco de informação ao invés de ser subdivido em colunas, como ocorre na base de dados relacional.

#### Classe `Column Family`

![Estrutura família de colunas](images/chapter_06_02.png "Estrutura família de colunas")

Esse modelo se tornou popular através do paper "[Bigtable: A Distributed Storage System for Structured Data](https://static.googleusercontent.com/media/research.google.com/en//archive/bigtable-osdi06.pdf)", criado por funcionários do Google, com o objetivo de e montar um sistema de armazenamento de dados distribuído, projetado para ter um alto grau de escalabilidade e de volume de dados. Assim como o chave-valor, para realizar uma busca ou recuperar alguma informação dentro do banco de dados, é necessário utilizar o campo que funciona como um identificador único que seria semelhante à chave na estrutura chave-valor. Porém, as semelhanças terminam por aí. 

Em bancos do tipo `Column Family`, as informações são agrupadas em colunas: uma unidade da informação (composta pelo nome) e a informação em si.

Esses tipos de bancos de dados são importantes quando se lida com um alto grau de volume de dados, e quando é necessário distribuir estas informações entre diversos servidores. Mas vale salientar que a sua operação de leitura é bastante limitada, semelhante ao chave-valor, pois a busca da informação é definida a partir de um campo único ou uma chave. Existem diversos bancos de dados que utilizam essas estruturas. Podemos citar:

* Hbase
* Cassandra
* Scylla
* Clouddata
* SimpleDb
* DynamoDB

Dentre os tipos de bancos de dados do tipo família de coluna, o Apache Cassandra é o mais famoso. Caso uma aplicação necessite lidar com um grande volume de dados e com fácil escalabilidade, o Cassandra é certamente uma boa opção.


Ao contrapor o banco do tipo família de coluna com os bancos relacionais, é possível perceber que as operações, em geral, são muito mais rápidas. É mais simples trabalhar com grandes volumes de informações e servidores distribuídos em todo o mundo, porém, isso tem um custo: a leitura desse tipo de banco de dados é bem limitada. Por exemplo, não é possível realizar uniões entre família de colunas como no banco relacional. A família de coluna permite que se tenha um número ilimitado de coluna, que por sua vez é composta por nome e a informação, exatamente como mostra a tabela a seguir:

| Estrutura relacional | Estrutura de família de colunas |
| -------------------- | ------------------------------- |
| Table                | Column Family                   |
| Row                  | Column                          |
| Column               | nome e valor da Column          |
| Relacionamento       | Não tem suporte                 |

#### Classe `Document`

Os bancos de dados orientados a documento têm sua estrutura muito semelhante a um arquivo JSON ou XML. Eles são composto por um grande número de campos, que são criados em tempo de execução, gerando uma grande flexibilidade, tanto para a leitura como para escrita da informação. 

![Estrutura de documentos {w=30%}](images/chapter_06_03.png "Estrutura de coleção de documentos")

Eles permitem que seja realizada a leitura da informação por campos que não sejam a chave. Algumas implementações, por exemplo, têm uma altíssima integração com motores de busca, o que os torna crucial para a realização de análise de dados ou logs de um sistema. Veja abaixo algumas implementações dos bancos de dados do tipo documento:

* AmazonSimpleDb
* ApacheCouchdb
* MongoDb
* Riak


Destes, o mais popular é o MongoDB. Ao comparar com uma base relacional, apesar de ser possível realizar uma busca por campos que não sejam o identificador único, os bancos do tipo documentos não têm suporte a relacionamento. 

| Estrutura relacional | Estrutura de documentos |
| -------------------- | ----------------------- |
| Table                | Collection              |
| Row                  | Document                |
| Column               | Key/value pair          |
| Relationship         | --                      |

Uma outra característica de bancos do tipo documento, é que no geral, são _schemeless_.

#### Classe `Graph`

![Estutura de Grafos](images/chapter_06_04.png "Estutura de Grafos")

O banco do tipo grafo é uma estrutura de dados que conecta um conjunto de vértices através de um conjunto de arestas. Os bancos modernos dessa categoria suportam estruturas de grafo multirrelacionais, onde existem diferentes tipos de vértices (representando pessoas, lugares, itens) e diferentes tipos de arestas. Os sistemas de recomendação que acontecem em redes sociais são o maior case para o banco do tipo grafo.  Veja abaixo alguns exemplos deste tipo de banco:


* Neo4j
* InfoGrid
* Sones
* HyperGraphDB

Dos tipos de banco de dados mais famosos no mundo NoSQL, o grafo possui uma estrutura distinta com o relacional.

#### Multi-model database

Alguns bancos de dados possuem a comum característica de ter suporte de uma ou mais classes apresentadas. São exemplos:

* OrientDB
* Couchbase

### Teorema do CAP

![Teorema do CAP](images/chapter_06_05.png "Teorema do CAP")

Um dos grandes desafios dos bancos de dados NoSQL é que eles lidam com a persistência distribuída, ou seja, as informações ficam localizados em mais de um servidor. Foram criados diversos estudos para ajudar nesse desafio de persistência distribuída o mais famoso foi uma teoria criada em 1999, o Teorema do CAP. 

Este teorema afirma que é impossível que o armazenamento de dados distribuído forneça simultaneamente mais de duas das três garantias seguintes:

* *Consistência*: uma garantia de que cada nó em um cluster distribuído retorna a mesma gravação mais recente e bem-sucedida. Consistência refere-se a cada cliente com a mesma visão dos dados.
* *Disponibilidade*: cada pedido recebe uma resposta (sem erro) - sem garantia de que contém a escrita mais recente
* *Tolerância à partição*: o sistema continua a funcionar e a manter suas garantias de consistência apesar das partições de rede. Os sistemas distribuídos que garantem a tolerância continuam operando mesmo que aconteça alguma falha em um dos nós uma vez que existe, pelo menos, um nó para operar o mesmo trabalho e garantir o perfeito funcionamento do sistema.

De uma maneira geral, esse teorema explica que não existe mundo perfeito. Quando se escolhe uma característica, perde-se em outra como consequência. Em um mundo ideal, um banco de dados distribuído conseguiria suportar as três características, porém, na realidade, é importante para o desenvolvedor saber o que ele perderá quando escolher entre um ou outro.

Por exemplo, o Apache Cassandra ele é AP, ou seja, sua arquitetura focará em tolerância a falha e disponibilidade. Existirão perdas na consistência, assim, em alguns momentos um nó retornará informação desatualizada.

Porém, o Cassandra tem o recurso de nível de consistência, de modo que é possível fazer com que algumas requisições ao banco de dados sejam enviadas a todos os nós ao mesmo tempo, garantindo consistência. Vale ressaltar que fazendo isso ele perderá o `A`, de _aviability_ do teorema do CAP, da disponibilidade.



### Escalabilidade vs Complexidade

No mundo NoSQL cada classe de banco tem o objetivo de resolver problemas particulares. Como o gráfico abaixo mostra, existe um balanço entre o modelo de complexidade: modelos que permitem mais complexidade em modelagem e busca resultam en menos escalabilidade. Como um exemplo temos o banco de classse chave-valor que é mais escalável, porém, permitem menos complexidade uma vez que as queries são baseadas apenas na chave.

![Escalabilidade vs Complexidade](images/chapter_06_06.png "Escalabilidade vs Complexidade")

### Master/Slave vs Masterless

Em linha geral a persistência o mundo NoSQL possui duas maneiras de comunicação entre os servidores:



![Master/Slave vs Masterless](images/chapter_06_07.png "Master/Slave vs Masterless")


* *O Master/Slave*: é o modelo de comunicação da qual se caracteriza num controle unidirecional de um ou mais dispositivos. Em linhas gerais, o nó master é utilizado para a escrita e para a replicação das informações para todos os nós escravos, que por sua vez, são responsáveis por realizar a leitura das informações. Desta maneira, é possível garantir maior consistência de dados. Como há um único ponto para a escrita, é possível ter suporte a comportamentos como, por exemplo, transação. Porém, existe um ponto de falha: o master. Caso o servidor fique fora do ar, teremos problemas com a escrita. Em cenários como este, bancos de dados modernos conseguem realizar a eleição de um novo nó master de maneira automática.

* *Masterless*: é o modelo de comunicação da qual se caracteriza um controle multidirecional por um ou mais dispositivos. Ou seja, não existe um único nó responsável por leitura ou escrita. Cada nó pode ser responsável pelas duas operações. Assim, não existe nenhum ponto de falha, a elasticidade acontece de maneira natural, porém, a consistência da informação se torna mais difícil uma vez que é necessário um certo tempo para que os nós tenham a informação mais atualizada.

### Conclusão

Este capítulo teve como objetivo dar o pontapé inicial para os bancos de dados não relacionais. Foram discutidos os principais conceitos acerca de bancos não estruturados, as classes de bancos e suas estruturas. Com este novo paradigma de persistência não estruturada, as portas se abrem paranovas possibilidades e novos desafios na implementação de aplicações. 

Os bancos de dados NoSQL vieram para enfrentar e sustentar a nova era das aplicações, na qual velocidade e menor tempo de resposta é um grande diferencial e fator decisivo na hora da escolha. Com este capítulo introdutório, você está apto para seguir desbravando os bancos não relacionais, como o Cassandra, MongoDB, Neo4J e vários outros.

EN

# NoSQL vs. SQL

Often, when we start a debate on SQL (Structured Query Language) and NoSQL in the technical environment, we can already expect heated discussions because an idea of a war, a dispute, etc., that needs to be won by these technologies' users exists. We want to make it clear: there is no war! Let’s demystify this myth?

To begin with, we need to talk a little bit about history. Consequently, we will comprehend the details about NoSQL and its differences from SQL.

In the world of technology, we always create a solution to solve a specific problem. Let's go back more than forty years ago, to understand the existing situation in the context of databases:

- In order to be used later, the data produced by the computers had to be stored and could not be lost when the machines were turned off.

Nevertheless, at that time, storage technology was in its infancy, and hard drives were rather expensive. It was then necessary to structure and normalize the data that would be recorded on those drives. By adopting this practice, the stored data would utilize less space and, consequently, bring better use of resources, generating savings or the possibility of storing a greater amount of data in the same space.

That was the context in which relational databases were created, including implementing all concepts detailed in the previous chapter, such as normal forms and well-defined data structure.

Time has passed, our technology has evolved and, therefore, another problem has arisen: we produce data in a considerable quantity, often in an unstructured and decentralized way with increasingly distributed systems.

**TIP:** _This data is called *unstructured* because it comes from several sources, such as IoT sensors (_Internet of Things_, i.e., refrigerators connected to the internet, smartwatches, and autonomous cars), non-cataloged images and documents, among other examples._

Structuring, i.e., organizing data from sources like these was (and is) possible but would require a lot of time. This extra time would impact the software development and delivery process and consequently led companies to lose the time to market for the solution being created. This problem needed to be solved, and, thus, NoSQL databases were born!

The term **NoSQL** was created in 1998 by Carlo Strozzi and later re-introduced by Eric Evans in 2009 when the latter participated in the organization of an event to discuss **open-source** and **distributed** databases. And speaking of distributed databases, this is a concept widely used by NoSQL databases: basically, NoSQL databases operate in distributed computing, which represents a significant gain of scalability and performance.

**TIP:** To understand a little more about distributed computing, we recommend reading the following article: [Paradigm of distributed computing](https://imasters.com.br/arquitetura-da-informacao/paradigma-da-computacao-distribuida).

## NoSQL

### What does NoSQL mean?

There is no, say, "official" definition for what this term actually means, but I particularly like the following: **N**ot **O**nly **SQL**. This definition emphasizes that these databases may use languages similar to [SQL ANSI](https://pt.wikipedia.org/wiki/SQL) to perform queries and other operations and not just SQL itself.

### NoSQL features

When you studied relational databases, you saw that these databases are based on the **ACID** concept (**A**tomicity, **C**onsistency, **I**solation, **D**urability). The vast majority of NoSQL databases are based on another concept: **BASE** (**Ba**se Availability, **S**oft State and **E**ventually Consistent).

Before detailing each point of the **BASE** concept, you need to understand the representation of a term in the database context you will see throughout this chapter: **cluster**. A cluster, in this context, refers to the ability of a set of (database) **servers** or (database) **instances** to connect to a database. An **instance** is a collection of memory and processes that interact with the database, which is the set of physical files that effectively store the data.

We must highlight two main advantages of a cluster, especially in very large database environments:

* **Fault Tolerance**: as there is more than one server or instance for users to connect, the database cluster offers an alternative in case of a server failure. When dealing with thousands of machines in a single [_data center_](https://pt.wikipedia.org/wiki/Centro_de_processamento_de_dados), such failures are a present issue;

* **Load Balancing**: the cluster is usually configured to automatically allocate users to the least used server so that the use of the structure available to the database is optimized.

#### The BASE concept in details

- Base Availability - **BA**
    - The database appears to be working all the time. As the cluster concept is implemented, if a server fails, the database will continue functioning due to another server that will supply this failure;

- Soft State - **S**
    - It is not necessary to be consistent all the time. In other words: with a distributed database in several machines and all of them being equally used for writing and reading, it is possible that, at a given moment, a machine receives a writing command and does not have time to "pass on" that command to the other database machines. Thus, if a user accesses the updated machine and another one uses a dated one, the results, which should be the same, will be different. Imagine your **Facebook** _timeline_: it shows your friends' posts, but not all posts are displayed simultaneously
. In this case, what happens is that the information was sent to the database, but not all cluster servers have the same information at the same time. That allows the database to manage more writing information without having to worry about replicating it in the same operation;

- Eventually Consistent - **E**
     - The system will become consistent at some point; in other words, it will eventually become consistent. As we do not have the replicated information "instantly", this concept is responsible for making the database consistent "in its time". That is because, depending on the cluster configurations, this replication may happen more quickly or not. But at some point, the information will be consistent and present on all cluster servers.

Finally, another striking feature of unstructured databases is the absence of the `schema` or `flexible schema` characteristic. This means there is no need to define the data schema in advance. If, on the one hand, this makes adding new attributes more dynamic, on the other, it can impact the integrity of this data. Don't worry: in time, all of these concepts will become much clearer.

---

### NoSQL and its classes

NoSQL databases can be categorized into four types (called **classes** in the database context):

* `Key / Value`
* `Column Family`
* `Document`
* `Graph`

Each class above has a different application, so we must understand each class's characteristics to get the best out of each one and know when to choose them.

#### `Key / Value` Class

![Key-value structure](images/chapter_06_01.png "Key-value structure")

Key-value databases have a structure similar to Java's `java.util.Map` class, that is, the *information* (`value`) will be retrieved only by the *key*. This type of database can be used, for example, to manage user sessions. Another example is coupled with a DNS, where the key is the address, for example, `www.google.com`, and the value is the IP of that server.

Currently, there are several key-value database implementations; the most famous are:

* AmazonDynamo
* AmazonS3
* Redis
* Scalaris
* Voldemort

Comparing the relational database with the key-value one, we can list some points of attention. The first is that the key-value structure is quite simple:

| Relational structure | Key-value structure|
| -------------------- | --------------------- |
| Table                | Bucket                |
| Row                  | Key/value pair        |
| Column               | ----                  |
| Relationship         | ----                  |

In this database class, it is impossible to run operations such as `join` between the `buckets`, and the value is composed of a large block of information instead of being subdivided into columns, as in the relational database.

#### `Column Family` Class

![Column family structure](images/chapter_06_02.png "Column family structure")

This model became popular after the paper [Bigtable: A Distributed Storage System for Structured Data](https://static.googleusercontent.com/media/research.google.com/en//archive/bigtable-osdi06.pdf), created by Google employees, aiming to assemble a distributed data storage system designed to have a high degree of scalability and data volume. Just like the key-value one, to search or retrieve some information within the database, it is necessary to use the field that functions as a unique identifier - similarly to the key in the key-value structure. However, the similarities end there.

In 'Column Family` databases, the information is grouped in columns: an information unit (composed of the name) and the information itself.

These types of databases are essential when dealing with a high degree of data volume and distributing this information among several servers. But it is worth noting that its reading operation is quite limited, similar to the key-value one, since the search for information is defined from a single field or a key. Several databases use that use these structures. We can give as examples:

* Hbase
* Cassandra
* Scylla
* Clouddata
* SimpleDb
* DynamoDB

Among the column family databases, Apache Cassandra is the most famous. Cassandra is certainly a good option if an application needs to deal with a large volume of data and with easy scalability.


By comparing the column family databases with the relational ones, it is possible to notice that the operations, in general, are much faster. It is simpler to work with large volumes of information and servers distributed worldwide; however, this comes at a cost: the reading of this type of database is minimal. For example, it is not possible to join families of columns as in the relational database. The column family one allows you to have an unlimited number of columns, which are composed of name and information, exactly as shown in the following table:

| Relational structure | Column family structure |
| -------------------- | ------------------------------- |
| Table                | Column Family                   |
| Row                  | Column                          |
| Column               | Column name and value          |
| Relationship       | Not supported                 |

#### `Document` Class

Document-oriented databases have a structure very similar to a JSON or XML file. They are composed of many fields, which are created at run time, generating great flexibility both for reading and writing information.

![Document structure {w=30%}](images/chapter_06_03.png "Document collection structure")

They allow information to be read by fields that are not the key. Some implementations, for example, have a very high integration with search engines, which makes them crucial for data analysis or system logs. Below are some implementations of document databases:

* AmazonSimpleDb
* ApacheCouchdb
* MongoDb
* Riak

From those, the most popular is MongoDB. When comparing with a relational base, although it is possible to search for fields other than the unique identifier, document databases do not have relationship support.

| Relational structure | Document structure |
| -------------------- | ----------------------- |
| Table                | Collection              |
| Row                  | Document                |
| Column               | Key/value pair          |
| Relationship         | --                      |

Another characteristic of document databases is that, in general, they are schemeless.

#### `Graph` Class

![Graph Structure](images/chapter_06_04.png "Graph Structure")

The graph database is a data structure that connects a set of vertices through a set of edges. Modern databases in this category support multi-relational graph structures, where there are different types of vertices (representing people, places, items) and different types of edges. The social network recommender systems are the biggest use cases. Check some graph database examples below:

* Neo4j
* InfoGrid
* Sones
* HyperGraphDB

Of the most famous database types in the NoSQL world, the graph has a different structure than the relational one.

#### Multi-model database

Some databases have the common characteristic of supporting one or more presented classes. Examples include:

* OrientDB
* Couchbase

### CAP theorem

![CAP theorem](images/chapter_06_05.png "CAP theorem")

One of the great challenges of NoSQL databases is that they deal with distributed persistence, i.e., the information is located on more than one server. Several studies were created to tackle this challenge. The most famous one was a theory created in 1999: the CAP theorem.

This theorem states that distributed data storage can't provide more than two of the following three guarantees simultaneously:

* *Consistency*: a guarantee that each node in a distributed cluster returns the same most recent and successful recording. Consistency refers to each client with the same data view.
* *Availability*: each request receives a response (no error) - no guarantee that it contains the most recent writing
* *Partition tolerance*: the system continues to function and maintain its consistency guarantees despite network partitions. Distributed systems that guarantee tolerance continue to operate even if there is a failure in one of the nodes since there is at least one node to do the same job and ensure the system’s perfect functioning.

Generally speaking, this theorem says there is no perfect world. When a characteristic is chosen, another one is lost as a consequence. In an ideal world, a distributed database would support all three characteristics. Still, in reality, the developer needs to know what they will lose when choosing between one or another.

For example, Apache Cassandra falls under the AP system, which means its architecture will focus on fault tolerance and availability. There will be losses regarding consistency, so a node will return outdated information.

However, Cassandra has the consistency level feature so that it is possible to send some database requests to all nodes at the same time, ensuring consistency. It is worth mentioning that in doing so, it will lose the `A`, of _availability_, in the CAP theorem.

### Scalability vs. Complexity

In the NoSQL world, each database class solves specific problems. As the graph below shows, there is a balance between the complexity model: models that allow more complexity in modeling and searching result in less scalability. As an example, we have the key-value class database, which is more scalable; however, allowing less complexity since the queries are based only on the key.

![Scalability vs Complexity](images/chapter_06_06.png "Scalability vs Complexity")

### Master/Slave vs. Masterless

In general, the persistence of the NoSQL world has two ways of communicating between servers:



![Master/Slave vs Masterless](images/chapter_06_07.png "Master/Slave vs Masterless")


* *Master/Slave*: this communication model is characterized by a unidirectional control of one or more devices. Generally speaking, the master node is used for writing and replicating information for all slave nodes, which, in turn, are responsible for reading the information. Consequently, it is possible to guarantee greater data consistency. As there is a single point for writing, it is possible to support behaviors, such as a transaction. However, there is a point of failure: the master. If the server goes down, we will have problems with writing. In these scenarios, modern databases can automatically choose a new master node.

* *Masterless*: it is the communication model characterized by a multidirectional control by one or more devices. That is, there is no single node responsible for reading or writing. Each node might be responsible for both operations. Thus, there is no point of failure, and elasticity happens naturally; however, the information's consistency becomes more difficult since it takes a certain time for the nodes to have the most updated information.

### Conclusion

This chapter aimed to kick-start non-relational databases and discuss the main concepts about unstructured databases, database classes, and their structures. With this new paradigm of unstructured persistence, the doors are open for new possibilities and challenges in application implementations.

NoSQL databases have come to face and sustain the new era of applications, in which speed and shorter response times are a big differential and a decisive factor when choosing. With this introductory chapter, you can continue exploring non-relational databases, such as Cassandra, MongoDB, Neo4J, and several others.
