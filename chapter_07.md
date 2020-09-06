# Microservices

[Microsserviços](https://www.martinfowler.com/articles/microservices.html) é uma arquitetura que tem se estabeleceu como uma nova maneira de construirmos nossas aplicações. Este modelo tende a ser independentes e modeladas para o dominio de negócios (o que faz o DDD ser muito importante). Essas aplicações se comunicam entre si via protocolos de rede, e tem uma arquitetura que pode resolver problemas que você tenha. Os microsserviços são um dos tipos do SOA (service-oriented architeture), e tem como foco de colocar fronteiras bem especificas de negocios e promover a entrega independente destes serviços, trazendo a vantagem de pode ser agnóstico de tecnologia. Resumindo no ponto de vista de tecnologia, são capacidades de negócios encapsuladas em um ou mais endpoints, mas seguindos os seguintes pilares:

* Flexibilidade
* Escalabidade
* Simplicidade

Neste cenário criamos aplicações que são desacopladas em pequenos serviços e cada um deles representando um objetivo de negócio. Onde eles podem ser desenvolvidos e facilmente mantidos de maneira individual, e podendo usar diferentes tipos de linguagem de programação.

<center><img src="images/chapter_04_01.png" alt="Arquitetura Microsserviços"  /></center>



​												<center>*Arquitetura de Microserviços*</center>

Mas isso não significa que devemos esquecer as arquiteturas Monoliticas, é preciso analisar os casos onde se encaixam cada um dos formates e analisar os pontos de atenção que são trazidos aos ambientes.



## Monolitos vs microsserviços

Falar que está em uma arquitetura monolítica quer dizer que você tem uma artefato único que é a base de todos os seus componentes funcionais, que é disponibilizada (deployed) de uma vez só e roda em um mesmo ambiente. Tudo em uma base de código única onde todas as alterações irão acontecer. 

Um monolito deve ser considerado como uma solução válida se você está desenvolvendo uma aplicação muito simples, onde pode-se testar rapidamente e fácilmente colocar essa aplicação no ar. Quando estamos discutindo sobre monolitos normalmente estamos falando de um código que é disponibilizado em um processo único. Você até pode ter várias instâncias rodando, mas o código continua sendo um processo único. 

Pode-se ter inclusive monolitos modulares, que não passa de se separar os códigos em módulos que podem ser executados separadamente, mas eles tem dependencia entre si e não podem ser disponibilizados separadamente. Inclusive podemos ter neste cenário inclusive aplicações distribuídas - que não podem ser alteradas ou disponibilizadas separadamente. Inclusive, este é um modelo de monolito que devemos evitar, pois trás uma complexidade muito grande ao sistema.

Esse é o principal desafio do monolito: o acoplamento que ele gera, tanto em implementação quanto no deploy. Quando a aplicação começa a se tornar muito complexa, com vários times de desenvolvimento e muitas funcionalidades, as adições ou alterações começam a se tornar complicadas por causa do acoplamento existente. Escalar um ambiente destes começa a se tornar desafiador. 

É aqui que entra o conceito dos microsserviços que começa a desacoplar esses serviços e dar responsabilidade únicas para os "serviços". Nesta abordagem você pode alterar, disponibilizar e escalar de maneira independente todo o ecossistema, seguido sempre a premissa de não afetar os outros microsserviços.

## Vantagens 

Vejamos algumas vantagens da utilização da abordagem de uma arquitetura orientada a microsserviços:

- **Escalabilidade** - Devido à independência dos serviços, eles podem ser escalados conforme a necessidade e uso sem gerar impactos nos demais serviços. 
- **Liberdade de selecionar tecnologias por projeto**: É possível escolher a melhor tecnologia para resolver determinado problema mesmo que seja diferente das tecnologias utilizadas nos demais serviços.
- **Produtividade** - Esta abordagem suporta a existência de multiplos times multidiciplinares (também conhecido como squads) que podem atuar com um foco mais específico de negócio e devido à independência técnica, entregar com maior velocidade de entrega.
- **Agilidade** - metodologias ágeis e suas diversas ramificações têm se mostrado cada mais populares. Ao se aliar este estilo de gerenciamento de projeto com a arquitetura em microsserviços, permitimos tecnicamente a existência de ciclos completos e mais curto para entrega de valor .
- **Reusabilidade** - Os componentes, como por exemplo um serviço que processe a lógica de negócio, podem ser consumidos via APIs quando necessário evitando assim a duplicidade de código e impactos na manutenibilidade.

## Desafios

A comunicação entre componentes de uma aplicação monolítica ocorrem in-memory, ou seja, não possuem o overhead da  **latência** existente na comunicação via rede, como ocorre no cenário de microsserviços. Quanto mais o número de serviços e a complexidade arquitetural aumentam, este problema pode ser mais catastrófico. Lidar com o tempo de resposta do serviço invocado no cliente em si é uma boa prática (como configuração de timeouts e retries) assim como, manter em dia os serviços de monitoramento e alertas da sua rede.

Atente-se à separação por responsabilidade dos componentes de backend e de front-end. O **front-end** deve ser implementado de forma que, na falha de um dos serviços de back-end, os demais itens funcionem normalmente - garantindo a melhor experiência possível ao usuário final.

A arquitetura de microsserviços é agnostica a linguagens e frameworks. Com isso, seu ambiente pode se tornar **poliglota**. Do ponto de vista arquitetural e de liderança ténica, tenha parcimônia na avaliação das tecnologias a serem utilizadas para que não se depare com um cenário onde há um serviço sem profissionais capacitados para mantê-lo. A definição do escopo e tamanho de um microsserviço deve ser mensurada 

A definição do tamanho e escopo dos microsserviços pode ser uma tarefa que exija um pouco mais de esforço no início da jornada ao desacoplamento. Tenha em mente que o escopo e tamanho de um microsserviço deve ser mensurados a partir do princípio da responsabilidade única (**S**OLID). 

Um dos desafios de governança é evitar a existência de aplicações orfãs em ambiente produtivo. Procure estabelecer times responsáveis por cada serviço, inclusive em sua fase produtiva. Desta forma caso ocorra um problema inesperado ou uma nova solicitação de mudança, será mais fácil identificar quem poderá assumir as tarefas.

## Quando evitar

Nenhuma arquitetura é apropriada a todos os cenários e organizações, portanto, vamos conhecer alguns fatores que nos alertam que a arquitetura de microsserviços pode não ser a mais apropriada. 

Avalie o quanto o seu time de T.I. tem conhecimento das fronteiras que definem o negócio do software a ser implementado. Caso a visão seja muito restrita, fique alerta. Para maiores chances de sucesso na implementação desta arquitetura é necessário ter uma boa visão do domínio a ser implementado, para que seja possível identificar as possíveis partes a serem desacopladas.

Se você precisa testar uma idéia através de um MVP (Produto Viável Mínimo) , também deve-se avaliar com cautela esta arquitetura. Para a criação da maioria dos MVPs, o processo de desenvolvimento e entrega de software se mostra mais eficiente com a utilização de monolíticos. Uma vez validada a idéia do MVP, pode-se migrar para a abordagem microsserviços se a essa estratégia se mostrar necessária.

Os desafios de monitoramento e observabilidade dos serviços distribuídos e até conteinerizados trarão a necessidade de um time com perfis e skills mais mescladas e que vão além de um crud tradicional implantado em um servidor de aplicações instalado em uma máquina virtual. Tecnologias como contêineres, orquestradores de contêineres e funcionalidades cloud-native devem ser habilidades existentes no seu time de T.I.

Evitar esta arquitetura no início de um projeto não significa impossibilitar o seu uso futuramente. Vamos falar um pouco sobre estratégias para se realizar a migração de aplicações monolíticas para um cenário de aplicações menores e mais desacopladas.

## Planejando uma migração de um monolito para uma arquitetura de microservices

Existem alguns padrões e tecnicas para fazer uma migração para de um monolito para um microserviço. Vale lembrar que são padrões, então são idéias que se pode seguir para chegar ao objeto que é migrar, mas não quer dizer que um padrão seja universalmente a melhor idéia, dependendo do caso, você pode precisar mesclar idéias e padrões.

#### Padrão: Strangler Fig Application (Estrangulamento)

Quem veio com esse padrão pela primeira vez foi Martin Fowler, https://martinfowler.com/bliki/StranglerFigApplication.html, e como o nome diz, o conceito é ir estrangulando o seu legado e ir substituindo as parte por microsserviços. E a idéia aqui é o "legado" funcionar junto com o novo, dando espaço para a parte nova começar a crescer e substituir o antigo. E a chave deste padrão é permitir uma migração incremental para o novo sistema. E como é incremental, lhe dá o espaço para aprender e parar ou pausar a migração e ir ajustando as direções do que está sendo entregue.

Esse padrão pode ser usado para migrar de uma monolito para outro e também ir de uma monolito para um padrão de microsserviços. As vezes, apenas separando código e reimplementando as funcionalidades segregadas, o unico ponto de atenção é que se a funcionalidade tem algum requerimento de persistência de estado, você vai precisar ver como vai levar isso para o mundo dos serviços.

Implementar esse padrão requer basicamente três passos:

1. Identificar as partes do sistema a serem migradas,
2. Implementar a funcionalidade em um novo microserviço. 
3. Fazer a aplicação antiga apontar para o novo microserviço.

Outra vantagem de seguir nessa direção é a possibilidade de em caso de necessidade, você poder voltar a aplicação para o estado anterior e continuar usando o legado enquanto você analisa o que fez com que o novo serviço não funcionasse como deveria.

Mas para este padrão funcionar, você precisa saber claramente o mapa de como funciona a funcionalidade que você quer levar para o microserviço.

#### Padrão: Composição de UI

O padrão de estrangulamento olha para o trabalho todo sendo no lado servidor, mas a interface também nos da ótimas oportunidades para ir separando as funcionalidades para o novo padrão de microsserviços.

E aqui temos diversos jeitos de aplicar essa separação, a idéia é ir do front até o backend. A chave é começar a componentizar a UI para que ela possa funcionar isolada, e ai poder encaixar a funcionalidade onde for necessário.

Um dos pontos de atenção desta técnica é que você precisa alterar seu monólito e lançar uma nova versão sempre que alterar essa parte do front, principalmente se a aplicação for mobile, porque será necessário uma nova versão na loja e nos clientes. E neste ponto é onde a técnica dos Micro-Frontends vai lhe ajudar bastante.

#### Padrão: Ramificar por Abstração

Para o padrão de estrangulamento, temos que ser capazes de interceptar as chamadas no perímetro do monólito. Mas e quando a funcionalidade está completamente enraizada dentro da sua aplicação? 

Neste casos é preciso fazer alterações significantes e disruptivas na sua base de código, só que isso se tornar um problema para você, porque queremos fazer as mudanças no código de maneira incremental, mas ao mesmo tempo impactar o menor possível os desenvolvedores que já estão trabalhando na sua base de código. Aqui entra o padrão de ramificar, e como fazer isso.

O primeiro passo é criar uma abstração da funcionalidade para ser substituída, então a aplicação atual passa a usar a abstração, então você trabalha no código desta abstração e cria o mecanismo para alternar entre o modelo antigo e o novo microserviço criado, e se está tudo ok, você remove a implementação antiga.

O interessante neste modelo, é que ao mesmo tempo você acaba criando um modelo de *fallback* que pode ser usado se seu novo serviço não se comportar bem. 

Este é um padrão utilizado para resolver praticamente qualquer situação, e útil quando a sua base de código vai sobreviver junto por um bom tempo.

#### Padrão: Rodando em Paralelo

Quando você tem um cenário em que você não consegue testar todos os cenários possíveis de implementação e não quer mexer no código legado, e quer mitigar o risco de ficar trocando entre uma implementação e outra, acaba-se por usar o padrão de subir um segundo serviço e deixar ele rodar em paralelo com o principal.

Neste modelo você não roda a implementação velha ou a nova, você roda as duas e assim consegue comparar o resultado e a performance de ambos. E com isso o sistema antigo permanece intocado. Normalmente se usa técnicas como o Canary Releasing neste padrão para que você possa ir direcionando parte do seu tráfego para a nova funcionalidade e ir comparando os resultados. Outra técnica muito usada aqui é o chamado "dark launch", onde você mantem a funcionalidade velha ativa e a nova transparente para o usuário e você vai comparando as funcionalidades e quando trocar para a nova será completamente transparente para o sistema.

Esse padrão é uma opção para ser usada quando você tem aplicações de alto risco que não pode ficar indisponíveis de maneira alguma, que são críticas para o negócio, então você vai comparando o funcionamento da nova e faz a troca no momento em que você tem a certeza absoluta que vai funcionar tudo perfeitamente.

#### Padrão: Colaborador de Decoração

Esse padrão é utilizado quando você precisa mudar algo baseado no comportamento de algo acontecendo dentro do monólito, mas não pode alterar o monólito. A idéia aqui é colocar algo como decoração para fazer com que o monolito esteja fazendo as chamadas aos serviços diretamente, mesmo que você não tenha chamado ele.

Em vez de você interceptar ou mudar a requisição, você deixa ela acontecer normalmente, e baseado no resultado desta chamada, juntamos com a chamada ao microserviço externo e até mesmo alteramos o comportamento da resposta.

Este padrão é usado normalmente quando você precisa mudar os dados, seja na chamada ou na resposta, e complementar essas informações sem alterar o seu monolito.



## Por onde começar?

O padrão de arquitetura de microserviços ele se encaixa para você se você tem um sistema que possui muitas funcionalidades, se você faz releases constantes, tem muitos subdomínios e linhas de negócio, se você tem times grandes e que precisam trabalhar em múltiplas funcionalidades do seu sistemas ao mesmo tempo, se seu projeto está sempre em expansão.

E para isso algumas tecnologias vão te ajudar nessa missão, não são todas obrigatórias, mas elas ajudam nessa direção.

#### Containers

Microsserviços são normalmente vinculados a containers (mas não obrigatoriamente precisamos usar containers), onde vamos usar o empacotamento completo da solução para ele. Onde a solução mais utilizada no mercado é o [Docker](https://www.docker.com/), que vai lhe ajudar a subir maquinas virtuais leves e você controlar o deploy dos seus serviços de maneira mais eficiente.

Com o conceito de containers você consegue realmente abstrair sua aplicação do ambiente e dar a indenpencia que o time precisa. Usando o Docker, por exemplo, cada microserviço pode ser entregue de maneira independente no ambiente que vai ser o "host".

E ele também ajuda que suas aplicações seja pequenas, tenham independências necessária e possam se comunicar umas com as outras. E aqui entra uma segunda ferramenta para ajudar a sua vida, pois quando você coloca suas aplicações em containers, será necessário gerenciar de alguma maneira esses pedaços de aplicação espalhados.

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

Ambientes Privados ou On-Premise são mais trabalhosas para se manter um escalabidade, mas não são impossíveis de serem feitos, se os pilares são mantidos, você está sim fazendo microsserviços.

## Práticas de Segurança

Código seguro deveria ser uma regra em qualquer design de aplicação, em alguns ramos são tão importantes quanto qualquer regra de qualidade que se possa ter. E para microsserviços não é diferente e aqui não existe nenhum segredo, é a mesma regra para qualquer desenho de aplicação, no mínimo se deve olhar para o [Top 10 do OWASP](https://owasp.org/www-project-top-ten/) e seguir essas recomendações no seu projeto. O mínimo que o seu desenvolvedor precisa saber é sobre essas vulnerabilidades.

Sério mesmo, não há nada novo aqui, e qualquer um que esteja tentando lhe convencer de um "Cross Microservice Injection" ou algo do tipo, está tentando lhe enrolar. 

No caso de Microsserviços há abordagem que muda é em relação a Autorização e Autenticação, e vamos discurtir isso mais a frente.

Uma dica importante sempre que se olha em termos de código seguro, é colocar algo na sua esteira de CI/CD e fazer a validação do seu código para procurar isso, e não só verificar se apenas o seu código traz vulnerabilidades, é interessante olhar se a bibliotecas de terceiros que você possa estar usando não podem estar causando algum problema no seu ambiente.

E ferramentas para isso tem várias, entre elas temos o [Fortify](https://www.microfocus.com/en-us/solutions/application-security), [Snyk](https://snyk.io/), [JFrog Xray](https://jfrog.com/xray/). Porque as vezes uma dependencia desatualizada pode colocar seu serviço em posição de risco, então olhar a melhor prática no código, e uma ferramenta para ajudar a apontar onde melhorar, formam um time imbatível.

Outra prática que eu vejo em algumas empresas, principalmente quando os serviços estão expostos apenas internarmente e não para fora, é não usar o HTTPS, ou melhor, use um TLS (Transport Layer Security). E para que você precisa disso, privacidade, integridade e idenficicação.

E quando estamos falando de microsserviços, um cenário que vai acabar sempre existindo é termos que falar com servidores de autorização, e podemos estar falando de um API Key, ou de um "client secret", ou até mesmo credenciais para uma autenticação básica. Então a primeira regra básica não deixe essas chaves no seu repositório de fonte, esses caras precisam ser variáveis de ambientes ou chaves de configuração externa, e elas devem estar sempre encriptadas.

Como estamos falando de containers, as práticas valem também para lá e nunca rode seu container como "root". Você precisa assumir a premissa de que seu sistema nunca é 100% seguro, alguém vai conseguir explorar algo. Então você não pode só prevenir, você precisa detectar e reagir a isso.

A chave são seguir ao menos cinco pilares:

- Seguro por desenho
- Vasculhe suas dependências
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

E quando se fala em Tokens, a chave é não querer reinventar a roda, e sim usar aquilo que já está consolidadado, é onde entra o OpenId e o OAuth/OAuth2. O Oauth 2 é praticamente o padrão mais utilizado para autenticação.

O padrão de OpenID é aquele usando quando você pode se conectar ou usar o token para se logar em vários sites ou serviços. Mas no seu padrão local, a recomendação é o OAuth, que estabelece um protocolo para que você tenha acesso aos recursos que você precisa, e ele trabalha com quatro papéis:

**Resource Owner** - Este é o papel que controla os acessos aos recursos.

**Resource Server** - É onde fica os serviços a serem acessados, ou seja, aqui são onde estão as suas API's, aplicações e etc.

**Client** - É quem faz a solicitação ao Resource Owner do recurso que ele precisa consumir.

**Authorization Server** - Quem gera os tokens de acesso, permite que o Client chegue ao recursos que foram permitidos, com o nível de acesso definido.

E como funciona isso, basicamente o "client" solicita ao Resource Owner acesso ao recurso, e este quando autoriza envia para o "Client" o "authorization grant", que é a credencial que representa que o Resource Owner autorizou a passagem. Então o "Client" vai solicitar ao Authoration Server um Token de acesso, tudo sendo válido, o Client recebe o seu token de acesso, que vai ser repassado para o Resource Server para que ele possa consumir aquilo que foi solicitado.

E existem 4 tipos fluxos para obtermos o token de acesso no padrão OAuth. Temos o Authorization Code, que é o tipo mais comum. O Implicit que é muito utlizado por aplicações SPA. O Resource Owner, que estabelece a confiança entre as aplicações e o Client Credentials que é usado para falar de um serviço para outro.

Tirando a parte de Autenticação e Autorização, que precisam de um cuidado a parte, segurança de microsserviços é como a segurança de qualquer aplicação e precisamos estar atentos a isso.

------

Como visto acima, a arquitetura de microsserviços traz bastante benefícios para o seu ambiente e lhe oferece a vantagem de deixar o desenvolvimento independente quando se tem vários times e funcionalidade, e essa independencia se estende também para o deploy da aplicação. Ou seja, você da velocidade para seu times e agilidade, consegue ter código de melhor qualidade já que ele vai estar organizado ao redor da funcionalidade. Tem-se a vantagem de ser fácil de escalar apenas no ponto em que se precisa, e ainda poder ser aplicacado na tecnologia que você tem mais domínio.

Mas, como já dito anteriormente, não é nenhuma bala de prata, ele traz complexidades para o ambiente e novas preocupações em termos de segurança. Imagine um projeto gigante com múltiplas instâncias e centenas de microsserviços, como você irá monitorar? Em caso de erro, como você vai encontrar, desviar ou mesmo tratar o erro?

Se usado da maneira correta, e tratado de perto os pontos de atenção, esse padrão de arquitetura tem muito a agregar no seus projetos.