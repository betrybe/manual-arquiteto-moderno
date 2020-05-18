# Microservices



[Microservices](https://www.martinfowler.com/articles/microservices.html), ou em portugues, Microsserviços, são um novo jeito de construirmos nossas aplicações nos dias de hoje, pregam ser independentes e modeladas para o dominio de negócios (o que faz o DDD ser muito importante). Essas aplicações se comunicam entre si via protocolos de rede, e tem uma arquitetura que pode resolver problemas que você tenha. Os microsserviços são um dos tipos do SOA (service-oriented architeture), e te o foco de colocar fronteiras bem especificas de negocios e promover a entrega independente destes serviços, trazendo a vantagem de pode ser agnóstico de tecnologia. Resumindo no ponto de vista de tecnologia, são capacidades de negócios encapsuladas em um ou mais endpoints, mas seguindos os seguintes pilares:



* Flexibilidade
* Escalabidade
* Simplicidade



Neste cenário criamos aplicações que são desacopladas em pequenos serviços e cada um deles representando um objetivo de negócio. Onde eles podem ser desenvolvidos e facilmente mantidos de maneira individual, e podendo usar diferentes tipos de linguagem de programação.

<center><img src="images/chapter_04_01.png" alt="Arquitetura Microsserviços"  /></center>



​												<center>*Arquitetura de Microserviços*</center>

Mas isso não significa que devemos esquecer as arquiteturas Monoliticas, é preciso analisar os casos onde se encaixam cada um dos formates e analisar os pontos de atenção que são trazidos aos ambientes.



## Monolitos vs Microsserviços

Falar que está em uma arquitetura monolítica quer dizer que você tem uma artefato único que é a base de todos os seus componentes funcionais, que é deployada de uma vez só e roda em uma mesmo ambiente. Tudo em uma base de código única onde todas as alterações irão acontecer. 

Um monolito deve ser considerado como uma solução válida se você está desenvolvendo uma aplicação muito simples, onde pode-se testar repidamente e fácilmente colocar essa aplicação no ar.

Mas quanto a aplicação começa a se tornar muito complexa, com vários times de desenvolvimento, muitas funcionalidades, as adições ou alterações começam a se tornar complicadas por causa do acoplamento que começa a existir. Escalar então um ambiente destes começa a se tornar desafiador.

É aqui que entra a mão dos microsserviços em começar a desacoplar esses serviços e dar responsabilidade únicas para os "serviços", onde você pode alterar, deployar e escalar de maneira independente de todo o ecossistema. Justamente com a premissa de não afetar os outros microsserviços.



## Quais as vantagens de se escolher por Microsserviços

As suas aplicações tem muito o que ganhar com a arquitetura em Microsserviços, e entre elas podemos elencar:

- **Escalabilidade** - Se aproveitando das as funções são independentes, você pode fazer os serviços escalarem independente dos outros, conforme a necessidade e uso, e até mesmo escolher a melhor tecnologia para um serviço diferente das demais. 
- **Produtividade** - Quando se fala em times grandes, ou empresas com muitos times, trabalhar com o modelo monolítico pode tomar muito tempo e esforço, e quebrar os serviços em vários e independentes ajuda a escalar os times de trabalho e reduz atritos, e até mesmo em um mesmo projeto, os membros são capazes de atuar em peças independentes.
-  **Agilidade** - Todos almejam chegar nos modelos ágeis de trabalho para alavancar a produtividade, e separar os serviços em pedaços menores permite que se separe as responsabilidades e trazer a eficiência de ciclos de desenvolvimento menores.
- **Reusabilidade** - Trabalhar com microsserviços significa que você tem pequenos trechos de código espalhados por uma aplicação maior. E essas peças podem funcionar de maneira independente e dessa maneira ser usada em uma outra parte da aplicação ou compor os dados de outro serviço.



## E os desafios que Microsserviços vão trazer

Claro que não estamos falando de uma “bala de prata” que resolve todos os problemas. E existem desafios que vão surgir quando se migra para este cenário. E um dos primeiros que tem-se que lidar é a comunicação entre os mesmo.

A comunicação entre eles não é instantânea, e como estamos falando de comunicação de rede, um ponto de preocupação que você terá que colocar em seus desenhos e soluções será a latência. E quanto mais seu ambiente crescer em complexidade, este problema pode se tornar grande em seu ambiente. Lidar com o fato de que sua rede pode e vai falhar causa muita dor de cabeça, mas precisa ser feito.

Também não se pode esquecer que a estratégia de fazer microsserviços não diz respeito apenas ao backend, é necessário ter atenção ao Front-End. Não vale absolutamente nada se você deixar a sua interface como um monolito gigante, isso não vai lhe trazer vantagem nenhuma mesmo que todo o server-side esteja distrubuído, porque na hora do deploy a alteração pode quebrar a sua interface.

Umas das vantagens dos micro-serviços que é ser agnóstico de tecnologia. qie pode se transformar no seu calvário depois. Não adianta deixar que seu ambiente seja completamente poliglota, se depois ninguém vai saber manter e operar, e ainda começar a ter problemas de latência por uma linguagem mal utilizada. É preciso ser conciso e usar tecnologias que seu time e equipes de suporte dominem.

Um dos pontos importantes, e que é sempre pergunta em reuniões é: “Mas qual é o tamanho que o microserviço precisa ter?”. E a maioria das pessoas confunde o “Micro” com o tamanho e acha que ele precisa ser do menor tamanho possível, acha que realmente tem relação com a quantidade de bytes que ele ocupa.

Mas como é medido esse tamanho? Linhas de código? Tamanho em bytes? Nada disso, é o principio da responsabilidade única, esse é o tamanho que você precisa respeitar.

Um grande ponto de atenção que é necessário, é a governança e manter quem será o dono do microserviço. Em ambientes altamente distribuídos, um serviço que está rodando em produção e não apresenta problema pode acabar ficando sem um dono e na hora de um problema ou possível manutenção, fica dificil achar quem é que assume a responsabilidade, então é preciso ter processos de governança bem fortes, para que você não acabe com aplicações órfãs em ambiente produtivo.



## Quando não escolher por Microsserviços

Um dos principais pontos para não se escolher ir para essa direção é quando não temos limites bem definidos de quais são as fronteiras que definem o seu negócio. Então é necessário ter uma visão completa de todo o seu domínio, e após isso começar a desacoplar as partes.

Se você quer testar algo rápido no modelo de Startups, também não recomendo escolher por aqui, e sim ir para algo mais monolítico, ou descentralizado e depois de testado e estabelecido ir na direção de evoluir para microsserviços. Os microsserviços deveriam entrar depois que você encontrou aquilo que se encaixa ao seu negócio, então você consolida e ai sim coloca algo escalável para rodar.

Se você criar aplicações que são empacotadas e entregues para seus clientes operar, microsserviços são uma péssima opção para você. Esse padrão de arquitetura traz algumas complexidades para você operar. Normalmente aplicações que são instaladas no cliente tem uma necessidade e um ambiente especifico para rodar, então neste caso, um arquitetura mais fechada e monolítica faz mais sentido.

E o principal ponto a se escolher é não fazer porque está todo mundo fazendo. Você precisa ter um objetivo e analisar qual é o seu ganho em fazer isso, se não houver ganho, ou se você está fazendo só para dizer que fez, então a recomedação para você é: "não faça".

## Planejando uma migração de um monolito para uma arquitetura de microservices

Existem alguns padrões e tecnicas para fazer uma migração para de um monolito para um microserviço. Vale lembrar que são padrões, então são idéias que se pode seguir para chegar ao objeto que é migrar, mas não quer dizer que um padrão seja universalmente a melhor idéia, dependendo do caso, você pode precisar mesclar idéias e padrões.

#### Padrão: Strangler Fig Application (Estrangulamento)

Quem veio com esse padrão pela primeira vez foi Martin Fowler, https://martinfowler.com/bliki/StranglerFigApplication.html, e como o nome diz, o conceito é ir estrangulando o seu legado e ir substituindo as parte por microsserviços. E a idéia aqui é o "legado" funcionar junto com o novo, dando espaço para a parte nova começar a crescer e substituir o antigo. E a chave deste padrão é permitir uma migração incremental para o novo sistema. E como é incremental, lhe dá o espaço para aprender e parar ou pausar a migração e ir ajustando as direções do que está sendo entregue.

Esse padrão pode ser usado para migrar de uma monolito para outro e também ir de uma monolito para um padrão de microsserviços. As vezes, apenas separando código e reimplementando as funcionalidades segregadas, o unico ponto de atenção é que se a funcionalidade tem algum requerimento de persistência de estado, você vai precisar ver como vai levar isso para o mundo dos serviços.

Implementar esse padrão requer basicamente três passos. O primeiro passo é identificar as partes do sistema a serem migradas, então entrar no segundo passo que é implementar a funcionalidade em um novo microserviço. E então entrar no último passo que é fazer a aplicação antiga apontar para o novo microserviço.

Outra vantagem de seguir nessa direção é a possibilidade de em caso de necessidade, você poder voltar a aplicação para o estado anterior e continuar usando o legado enquanto você analisa o que fez com que o novo serviço não funcionasse como deveria.

Mas para este padrão funcionar, você precisa saber claramente o mapa de como funciona a funcionalidade que você quer levar para o microserviço.

#### Padrão: Composição de UI

O padrão de estrangulamente olha para o trabalho todo sendo no lado servidor, mas a interface também nos da ótimas oportunidades para ir separando as funcionalidades para o novo padrão de microsserviços.

E aqui temos diversos jeitos de aplicar essa separação, a idéia é ir do front até o backend. A chave é começar a componentizar a UI para que ela possa funcionar isolada, e ai poder encaixar a funcionalidade onde for necessário.

Um dos pontos de atenção desta técnica é que você precisa alterar seu monolito e lançar uma nova versão sempre que alterar essa parte do front, principalmente se a aplicação for mobile, porque será necessário uma nova versão na loja e nos clientes. E neste ponto é onde a técnica dos Micro-Frontends vai lhe ajudar bastante.

#### Padrão: Ramificar por Abstração

Para o padrão de estrangulamento, temos que ser capazes de interceptar as chamadas no perímetro do monolíto. Mas e quando a funcionalidade está completamente enraizada dentro da sua aplicação? 

Neste casos é preciso fazer alterações significantes e disruptivas na sua base de código, só que isso se tornar um problema para você, porque queremos fazer as mudanças no código de maneira incremental, mas ao mesmo tempo impactar o menor possível os desenvolvedores que já estão trabalhando na sua base de código. Aqui entra o padrão de ramificar, e como fazer isso.

O primeiro passo é criar uma abstração da funcionalidade para ser substituida, então a aplicação atual passa a usar a abstração, então você trabalha no código desta abstração e cria o mecanismo para alternar entre o modelo antigo e o novo microserviço criado, e se está tudo ok, você remove a implementação antiga.

O interessante neste modelo, é que ao mesmo tempo você acaba criando um modelo de *fallback* que pode ser usado se seu novo serviço não se comportar bem. 

Este é um padrão utilizado para resolver praticamente qualquer situação, e útil quando a sua base de código vai sobreviver junto por um bom tempo.

#### Padrão: Rodando em Paralelo

Quando você tem um cenário em que você não consegue testar todos os cenários possíveis de implementação e não quer mexer no código legado, e quer mitigar o risco de ficar trocando entre uma implementação e outra, acaba-se por usar o padrão de subir um segundo serviço e deixar ele rodar em paralelo com o principal.

Neste modelo você não roda a implementação velha ou a nova, você roda as duas e assim consegue comparar o resultado e a performance de ambos. E com isso o sistema antigo permanece intocado. Normalmente se usa técnicas como o Canary Releasing neste padrão para que você possa ir direcionando parte do seu tráfego para a nova funcionalidade e ir comparando os resultados. Outra técnica muito usada aqui é o chamado "dark launch", onde você mantem a funcionalidade velha ativa e a nova transparente para o usuário e você vai comparando as funcionalidades e quando trocar para a nova será completamente transparente para o sistema.

Esse padrão é uma opção para ser usada quando você tem aplicações de alto risco que não pode ficar indisponíveis de maneira alguma, que são críticas para o negócio, então você vai comparando o funcionamento da nova e faz a troca no momento em que você tem a certeza absoluta que vai funcionar tudo perfeitamente.

#### Padrão: Colaborador de Decoração

Esse padrão é utilizado quando você precisa mudar algo baseado no comportamento de algo acontecendo dentro do monolito, mas não pode alterar o monolíto. A idéia aqui é colocar algo como decoração para fazer com que o monolito esteja fazendo as chamadas aos serviços diretamente, mesmo que você não tenha chamado ele.

Em vez de você interceptar ou mudar a requisição, você deixa ela acontecer normalmente, e baseado no resultado desta chamada, juntamos com a chamada ao microserviço externo e até mesmo alteramos o comportamento da resposta.

Este padrão é usado normalmente quando você precisa mudar os dados, seja na chamada ou na resposta, e complementar essas informações sem alterar o seu monolito.



## Por onde começar?

O padrão de arquitetura de microserviços ele se encaixa para você se você tem um sistema que possui muitas funcionalidades, se você faz releases constantes, tem muitos subdomínios e linhas de negócio, se você tem times grandes e que precisam trabalhar em multiplas funcionalidades do seu sistemas ao mesmo tempo, se seu projeto está sempre em expansão.

E para isso algumas tecnologias vão te ajudar nessa missão, não são todas obrigatórias, mas elas ajudam nessa direção.

#### Containers

Microsserviços são normalmente vinculados a containers (mas não obrigatoriamente precisamos usar containers), onde vamos usar o empacotamento completo da solução para ele. Onde a solução mais utilizada no mercado é o [Docker](https://www.docker.com/), que vai lhe ajudar a subir maquinas virtuais leves e você controlar o deploy dos seus serviços de maneira mais eficiente.

Com o conceito de containers você consegue realmente abstrair sua aplicação do ambiente e dar a indenpencia que o time precisa. Usando o Docker, por exemplo, cada microserviço pode ser entregue de maneira independente no ambiente que vai ser o "host".

E ele também ajuda que suas aplicações seja pequenas, tenham indepencias necessária e possam se comunicar umas com as outras. E aqui entra uma segunda ferramenta para ajudar a sua vida, pois quando você coloca suas aplicações em containers, será necessário gerenciar de alguma maneira esses pedaços de aplicação espalhados.

#### Orquestração de Containers

Agora imagine você no mesmo cenário que a Google, onde tudo lá roda em cima de containers, você vai precisar gerenciar tudo isso para não cai no caos. E é aqui que entra o Kubernetes, que aliás começou com o engenheiros da Googles, que vai lhe ajudar a agrupar seus containers em clusters que você pode gerenciar mais facilmente. 

Vale lembrar que o Kubernetes não é uma alternativa para o Docker, e uma ferramenta não substituí a outra, na verdade elas são complementares. Você pode usar as duas separadas, mas a chave do sucesso e juntar as duas. No Docker se cria o container da aplicação, e com o Kubernetes você automatiza as operação de manutenção, networking, segurança, escalabilidade. 

#### Construindo o Serviço

Mas claro que para isso você precisa construir aplicações que sejam leves e fáceis de se levar para um container e colocar nesse mundo. E para isso existem muitos sabores para você escolher, mas os mais populares usados pelas comunidades, vamos encontrar:

- [Spring Cloud com Spring Boot](https://spring.io/projects/spring-cloud) -  O framework da Spring é um dos mais completos para se criar aplicações distribuídas e seus microsserviços. Fornecendo a você todo o ferramental para que você possa colocar seu serviços para funcionar.
- [.Net Core](https://dotnet.microsoft.com/download/dotnet-core) - A versão Open Source da Microsoft para o .NET framework e também entregar uma versão mais leve e adequada para o mundo dos microsserviços.
- [Vert.x](https://vertx.io/) - Este é um framework poliglota que roda em cima da JVM focado para que você crie seus serviços para um ambiente orientado a eventos de maneira reativa.
- [Akka](https://akka.io/) - Este framework também é feito para ambientes orientados a eventos, voltando a programação reativa. Também roda em cima da JVM, mas para aplicações baseados em Java ou Scala.
- [Quarkus](https://quarkus.io/) - Novo player neste mercado, mas é uma stack nativa para Kubernetes e baseado em JVM. Onde você pode seguir por padrões tanto reativos como imperativos.
- [Falcon](https://falcon.readthedocs.io/en/stable/) - Essa é uma biblioteca para Python que permite que você construa seus serviços com alta performance.
- [Go](https://golang.org/) - Linguagem criada pela Google, baseada em C, que pode lhe dar serviços leves e rapidos, permitindo o uso de programação funcional.
- [Moleculer](https://moleculer.services/) - Para quem gosta de usar o Node.js para construir seus serviços, esse framework pode ajudar a construir serviços leve.

Podemos citar ainda vários outros frameworks, o importante no caso de microserviços é manter os conceitos e pilares para que as aplicações possam ter responsabilidade única, serem independentes e performáticas.

E depois que seu serviço estiver construído e já montado dentro de uma container, será necessário escolher onde você irá colocar esse serviço para funcionar, e pode ser aonde a sua infra-estrutura estiver, seja na Cloud, seja em uma Nuvem Privada, seja na sua propria estrutura on-premise, não importa. Claro que para cada ambiente diferentes, você irá encontrar desafios para fazer com que os conceitos sejam aplicados.

Mas a grande chave aqui é escolher um framework e colocar em uma ambiente que sua equipe seja capaz de dar suporte e conheça como usar e monitorar, olhar performance. Pois escolher uma tecnologia, ou framework que você não conhece, vai ter uma curva de aprendizado e pode ser que seu time não tenho o tempo necessário para aprender e terá que lidar com ela em produção.

Ambientes Privados ou On-Premise são mais trabalhasos para se manter um escalabidade, mas não são impossíveis de serem feitos, se os pilares são mantidos, você está sim fazendo microsserviços.

## Práticas de Segurança

Código seguro deveria ser uma regra em qualquer design de aplicação, em alguns ramos são tão importantes quanto qualquer regra de qualidade que se possa ter. E para microsserviços não é diferente e aqui não existe nenhum segredo, é a mesma regra para qualquer desenho de aplicação, no mínino se deve olhar para o [Top 10 do OWASP](https://owasp.org/www-project-top-ten/) e seguir essas recomendações no seu projeto. O minimo que o seu desenvolvedor precisa saber é sobre essas vulnerabilidades.

Sério mesmo, não há nada novo aqui, e qualquer um que esteja tentando lhe convencer de um "Cross Microservice Injection" ou algo do tipo, está tentando lhe enrolar. 

No caso de Microsserviços há abordagem que muda é em relação a Autorização e Autenticação, e vamos discurtir isso mais a frente.

Uma dica importante sempre que se olha em termos de código seguro, é colocar algo na sua esteira de CI/CD e fazer a validação do seu código para procurar isso, e não só verificar se apenas o seu código traz vulnerabilidades, é interessante olhar se a bibliotecas de terceiros que você possa estar usando não podem estar causando algum problema no seu ambiente.

E ferramentas para isso tem várias, entre elas temos o [Fortify](https://www.microfocus.com/en-us/solutions/application-security), [Snyk](https://snyk.io/), [JFrog Xray](https://jfrog.com/xray/). Porque as vezes uma dependencia desatualizada pode colocar seu serviço em posição de risco, então olhar a melhor prática no código, e uma ferramenta para ajudar a apontar onde melhorar, formam um time imbátivel.

Outra prática que eu vejo em algumas empresas, principalmente quando os serviços estão expostos apenas internarmente e não para fora, é não usar o HTTPS, ou melhor, use um TLS (Transport Layer Security). E para que você precisa disso, privacidade, integridade e idenficicação.

E quando estamos falando de microsserviços, um cenário que vai acabar sempre existindo é termos que falar com servidores de autorização, e podemos estar falando de um API Key, ou de um "client secret", ou até mesmo credenciais para uma autenticação básica. Então a primeira regra básica não deixe essas chaves no seu repositório de fonte, esses caras precisam ser variáveis de ambientes ou chaves de configuração externa, e elas devem estar sempre encriptadas.

Como estamos falando de containers, as práticas valem também para lá e nunca rode seu container como "root". Você precisa assumir a premissa de que seu sistema nunca é 100% seguro, alguém vai conseguir explorar algo. Então você não pode só prevenir, você precisa detectar e reagir a isso.

A chave são seguir ao menos cinco pilares:

- Seguro por desenho
- Vasculhe suas dependencias
- Use sempre HTTPS
- Use Tokens de Acesso e Identidade
- Proteja e Encripte seus segredos.

#### Soluções para Autenticação e Autorização

Para o mundo de microsserviços o principal ponto é verificar quem você é (Autenticação) e aquilo que você pode fazer (Autorização). E dentro da arquitetura de microsserviços você vai estar espalhado em muito serviços pela rede e terá que lidar com alguns problemas em relação a como resolver isso.

Autenticação e Autorização precisam ser resolvidos em cada um dos microsserviços, e parte dessa lógica global vai ter que replicada em todas os serviços, e neste caso um jeito para se resolver isso é criar bibliotecas para padronizar essa implementação, só que isso vai fazer que você perca um pouco da flexibilidade de quais tecnologias usar, pois a linguagem ou framework precisa suportar essa biblioteca padrão.

Outro cuidado que o uso da biblioteca lhe ajuda é a não quebrar o principio da responsabilidade única, já que o serviço deveria se preocupar apenas com a lógica de negócio.

E outro ponto que é necessário ser analisado é que os microsserviços devem ser *stateless*, então é necessário usar soluções que consigam manter isso.

Podemos abordar a Autorização e Autenticação pelo modelo de sessão distribuída, usando ferramentas para você armazenar essa sessão, e onde você pode abordar manter a sessão das seguintes maneiras:

**Sticky Session** - A idéia aqui é usar o load balancer e manter o usuário sempre no mesmo servidor que veio o request. Só que esse cara vai fazer você só conseguir expandir horizontalmente.

**Replicação de Sessão** - Ou seja, toda instância salva a sessão e sincroniza através da rede. Só que aqui vai lhe causar um "overhead" de rede. Quanto mais instancias, mais terá que replicar e se terá que lidar com a latencia disso.

**Sessão Centralizada** - Isso significa que os dados podem ser recuperados em um repositório compartilhado. Em vários cenários, esse é um ótimo desenho, porque se pode dar alto desempenho para as aplicações, onde você deixa o status do login escondido dentro dessa sessão. Mas claro que existe a desvantagem que você precisa criar mecanismos para proteger essa sessão e replicar entre as aplicações, que pode também adicionar latências na sua rede.

Mas quando estamos neste cenário de microsserviços, a recomendação passa a ser o uso de Tokens, onde a maior diferença para o modelo de sessão descrito acima, é que deixamos de ter algo centralizado em um servidor, e passamos a responsabilidade para o próprio usuário.

O Token vai ter a informação de identificação de usuário, e toda vez que chega ao servidor, podemos validar no server a identidade e a autorização. O token é encriptado e podem seguir um padrão como o [JWT](https://jwt.io/).

E usando token conseguimos delegar a responsabilide do estado do usuário, para algum processo que possa a validade do mesmo. Habilitamos vários tipos de validações de segurança que podem ser colocadas na malha (Service Mesh) ou no seu gateway de entrada e retirar essas responsabilidades dos serviços e aplicações e mesmo assim ainda continuar garantido a segurança.

Com o uso do JWT você passa a ter um "client token" onde você vai passar a algum servidor para que ele possa fazer a validação/criação do mesmo. 

