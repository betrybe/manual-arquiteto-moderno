# Clean Architecture



O livro clean architecture é um livro muito bom e ele faz parte de uma "trilogia" da qual eu recomendo a leitura de todas as partes, ou seja, clean code, clean coder e finalmente Clean Architecture. Esse capítulo não tem o objetivo de falar sobre o livro em si, mas uma visão de como eu o utilizo e o aplico com uma visão prática. Assim, como sempre falamos em todo o livro para melhor entendimento desse capítulo é importante ler o livro primeiramente.



Uma coisa interessante é como o clean architecture pode ser relacionado com o livro de DDD em diversos aspectos. Por exemplo, o livro DDD se utiliza em muitas vezes em criar uma linguagem próximo do negócio: A linguagem ubiquá. Já no clean architecture fala muito sobre separar o código de negócio com do que importa, ou seja, não amarrar regra de negócio com a tecnologia como um tipo de banco de dados.

Essa estratégia de dividir e conquistar tem muitas vantagens dentre elas, inicialmente, podemos citar duas:

* Testes: Uma das grandes vantagens nessa separação, certamente, se encontra na facilidade de testes principalmente os testes de unidade. Uma maior separação das camadas facilita, por exemplo, mockar as camadas de infraestrutura e fazer com que o teste seja barato. Ou seja, fácil de criar, rápido de executar e por consequência, esses testes tendem a ser executados constantemente e com a facilidade a criação e com maior cobertura. Muitas pessoas tem a experiência em projetos que levam horas para serem executadas e isso leva uma consequência negativas. Por exemplo, os testes que falham constantemente tendem a ser ignorados e testes não confiáveis fará com que o time nunca saberá se houve um problema de regressão ou simplesmente é aquele “erro amigo”. Vale salientar, que o ponto não é ser contra os outros tipos de testes, porém, testes unitários tem bastantes vantagens uma vez que tecnologias como Hibernate, Java, JPA já possuem os seus testes e o que precisa ser testado são as regras do seu negócio.
* Baixo impacto na mudança: Com uma separação maior do negócio da tecnologia faz com que, caso seja necessário, uma mudança de tecnologia, por exemplo, entre banco de dados. Esse impacto tenderá a ser menor. Um ponto importante que quando falamos na área da arquitetura o pragmatismo é um ponto crucial, afinal, o maior foco de um arquiteto é resolver um problema usando a tecnologia como meio, porém, não como fim. De modo que o usuário não precisa e, muitas vezes, não quer saber qual o banco, linguagem está sendo utilizado, etc.



Basicamente o livro tem pontos que valem a pena a serem seguidos:![](images/chapter_03/image_01.jpg)

*Fonte: https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html*



 

\* Uma estratégia de teste eficaz que segue a pirâmide de teste.

\* As estruturas são isoladas em módulos individuais. Quando (não se) mudarmos de ideia, só precisamos fazer uma mudança em um só lugar.

\* Arquitetura gritando também conhecida como uso pretendido. Quando você olha para a estrutura do pacote, percebe o que o aplicativo faz, em vez de ver detalhes técnicos. Existem muito semelhante quando você faz package by layer ao invés de package by layer.

\* Toda a lógica de negócios está em um caso de uso, portanto, é fácil encontrar e não duplicar em nenhum outro lugar.

\* Bom monólito com casos de uso claros que você pode dividir em microsserviços mais tarde, depois de aprender mais sobre eles.



Falando sobre o livro algum muito importante que sempre falamos em livros de arquitetura em geral, se encontra na granularidade e nas camadas. Com o tempo se percebe que as camadas podem ajudar tanto em abstrair e separar responsabilidade de negócio quanto para aumentar a complexidade do código. Assim é muito importante pensar quando se coloca camadas em uma aplicação e atenção para não a se tornar uma arma de destruição ao invés de ajuda. A estratégia que o livro fala é de uma visão “fora para dentro”, ou seja, a camada framework acessa a camada de adaptação seguindo essa a linha do princípio da dependência. 

De uma maneira geral, as camadas são as seguintes abaixo: 



## Entidades

 

Caso você veio do DDD, não verá muita novidade nessa camada e nos conceitos. Ela é responsável por encapsular o domínio do negócio. O ponto principal é que essa camada que é o core, ou seja, é a razão de fazer a aplicação em si. Ou seja, é nela que se concentra as regras de negócio e não deve mudar quando algo externo, por exemplo, mudança de banco de dados. Particularmente, não acho nenhum crime federal se também existir tecnologias que tendem a não mudar, por exemplo, bibliotecas utilitárias usadas por todas as empresas. Em uma aplicação menor, podem ser apenas interfaces e classes que tenham que ser utilizadas em todas as camadas, como a interface de um repositório.



## User cases



Nessa camada se concentra as ações da regra de negócios uma maneira de pensar de essa seja uma continuação da camada de entidade. Muitas regras que envolvem as entidades ficam muito grandes para caberem apenas na entidade sem falar no clássico problema de responsabilidade única que tanto falamos no [SOLID](https://en.wikipedia.org/wiki/SOLID). 



## Interface de adaptação


 Uma maneira interessante de pensar é que essa camada é uma grande implementação do padrão de projeto [Adapter](https://refactoring.guru/design-patterns/adapter). O seu maior objetivo é deixar as camadas de entidades e User cases mais transparente. Por exemplo, uma interface repositório podem ter diversas maneiras de implementação seja uma base de dados relacional seja não relacional e o maior objetivo dessa camada é garantir que as mudanças de tecnologia não impactem as outras camadas. Afinal, para o usuário não importa se banco de dados é um Cassandra ou um PostgreSQL, mas num nível técnico é importante pensar sim que existem diferentes estratégias de modelagem.



## Frameworks


 Essa é camada que “não importa” para o negócio, quero dizer é a camada para o meio e não para o fim. Ela é composto por ferramentas, e tecnologias como banco de dados. O maior ponto para essa estratégia dessa camada é evitar que ela passa para o menor número de camadas possíveis. É importante salientar que quanto menos código nessa camada melhor, ou seja, ele terá o necessário para interligar as tecnologias, modelagem de banco dados.



Esse livro traz uma boa referência para utilizar com arquiteturas maduras e uma melhor estratégia de como utilizar e comunicar entre as camadas. Um ponto importante, não existe bala de prata e esse livro também não é um. O material é bastante rico e com bastante detalhes, porém, as camadas também tendem a aumentar a complexidade do seu código, afinal, quanto mais camadas são criadas mais camadas são mantidas. Como diria o livro [fundamentos de arquitetura de software](https://www.amazon.com/Fundamentals-Software-Architecture-Comprehensive-Characteristics/dp/1492043451), tudo é um trade-off e o bom senso ainda é a melhor ferramenta para o arquiteto para saber quando e como se aplicam os conceitos.

