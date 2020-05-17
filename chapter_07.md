# Cloud

Agora que já cobrimos conceitos sobre microservices, boas práticas de arquitetura e design de código, vamos falar um pouco sobre computaçào em nuvem (*cloud*).

A computação em nuvem trouxe metodologias e técnicas que revolucionaram os mundos comercial e técnico. Entre os termos que surgiram, nasceu o *cloud native*, e para atender e cobrir essas expectativas no universo Java, nasceu o Jakarta EE.

## Cloud-Native ou Cloud-Enabled?

Estes dois conceitos são fundamentais e servem de embasamento para o entendimento da situação atual de seu ambiente, e o planejamento de criação e modernização de aplicações.

No contexto da computação em nuvem, uma aplicação pode ser categorizada conforme seu nível de adequação a um ambiente de cloud como sendo *cloud-enabled* ou *cloud-native* (a.k.a. *cloud-ready*).  

Uma aplicação que roda na cloud, mas que originalmente foi criada para rodar em ambiente tradicional - um data-center local, um cluster estático de servidores de aplicação, por exemplo, pode ser categorizada como *cloud-enabled*. Aplicações como esta costumam ter maior consumo de recursos (cpu, memória, storage) se comparado a aplicações cloud-native. 

Uma aplicação cloud-enabled passou por refatorações e ajustes para ser conteinerizada e orquestrada por plataformas como Kubernetes. O custo ou esforço de se refatorar toda a aplicação não são viáveis, portanto ela não usufrui de todos os benefícios existentes em um ambiente de cloud. 

> **INFO:** [Kubernetes](https://kubernetes.io/): É uma ferramenta open-source de orquestração de containers e trabalha muito bem com o [Docker](https://www.docker.com/). Atualmente, é uma das ferramentas mais populares. 

Vamos entender melhor estas diferenças ao aprofundarmos o conceito de *cloud*-native. Vamos discorrer um pouco mais sobre este conceito uma vez que não há uma, no momento da escrita deste livro, uma definição exata acerca do termo.

### Desmistificando o Cloud-Native

Existem vários entendimentos acerca deste conceito. Se lermos livros ou artigos sobre cloud native, poderemos nos deparar com diferentes opinões e um consequente não consenso sobre o termo. Vejamos alguns exemplos abaixo:



> Cloud-native é uma abordagem para criar e executar aplicações que explora as vantagens do modelo de computação em nuvem.  —Pivotal



> Cloud-native é uma maneira diferente de pensar e raciocinar sobre sistemas de software. Ele incorpora os seguintes conceitos: Alimentado por infraestrutura descartável, composta por limites, escalas globalmente, adota a arquitetura descartável. — [Architecting Cloud Native Applications](https://www.amazon.com/Architecting-Cloud-Native-Applications-high-performing-ebook/dp/B07QTJ8WW8/ref=sr_1_4?keywords=cloud+native+applications&qid=1575059989&sr=8-4)



> De maneira geral, “cloud-native” é uma abordagem para criar e executar aplicações que explora as vantagens do modelo de entrega de computação em nuvem. "Cloud-native" é sobre como os aplicações são criados e implantados, não onde. — [InfoWorld](https://www.infoworld.com/article/3281046/what-is-cloud-native-the-modern-way-to-develop-software.html)



> As tecnologias cloud-native capacitam as empresas a criar e executar aplicações escaláveis em ambientes modernos e dinâmicos, como públicos, privados, e nuvens híbridas. Containers, service meshes, microservices, infraestrutura imutável e APIs declarativas exemplificam essa abordagem. — [Cloud-Native Computing Foundation](https://www.cncf.io/)



Uma abordagem do tema sobre a perspectiva do autor:

> Um conjunto de boas práticas para otimizar uma aplicação na nuvem por meio de: containerização, orquestração e automação. —  [Otávio Santana](https://twitter.com/otaviojava)



Em um consenso mútuo em torno das definições de vários artigos, podemos dizer que o cloud native é um termo usado para descrever ambientes baseados em containers e as boas práticas ligadas a esta característica. Portanto, o cloud-native não está relacionado a linguagens ou estruturas de programação específicas ou mesmo a uma empresa provedora de nuvem: está relacionado, mas não limitado, ao uso de containers.



### O ciclo de vida de uma aplicação cloud-native

Vejamos um cenário que demonstra o ciclo de vida de uma aplicação cloud-native, com aplicação de melhores práticas de implantação:

Uma vez criada a aplicação:

* O código fonte deve ser disponibilizado em um repositório de código fonte - *a single source of truth*.

  >  **TIP**: Conforme [pesquisas](https://softwareengineering.stackexchange.com/questions/136079/are-there-any-statistics-that-show-the-popularity-of-git-versus-svn) sobre os dados de 2020 (baseadas em códigos open-source) repositórios `git`  são atualmente a escolha mais popular.

* Este repositório por sua vez, pode ser configurado de forma que ações chave - como criação de uma nova tag, ou commit no branch master - disparem automáticamente uma ferramenta de integração contínua. 

* A ferramenta iniciará a execução de testes unitários e empacotamento dessa aplicação (podendo incluir mais passos como por exemplo, checagem da qualidade do código). 

  >  **TIP:** Tendo em mente uma melhor utilização de recursos, deve-se destacar que: a imagem base utilizada para construir a aplicação *não* deve ser a mesma imagem base utilizada para se executar a aplicação. Ferramentas para construção e empacotamento (como maven, npm, etc) que não são utilizadas na execução da aplicação, *não* devem estar incluídas na imagem base de execução.

* Após empacotar a aplicação, a ferramenta de integração é responsável pela criação do container com base na imagem-base que inclua este novo pacote (i.e. `.jar`) . 

  >  **TIP:** É recomendado que a imagem-base gerada seja armazenada em um registro de imagens.

* A partir desta imagem, a plataforma utilizada (i.e. Kubernetes, OpenShift, Rancher, etc) irá criar a quantidade de containers especificada em suas configurações. 

  > **TIP:** É muito comum a existência de casos de uso que podem usufruir da utilização de Operators para gerenciamento e manutenção de aplicações. 

Alguns conceitos extras sobre a existência de uma aplicação cloud-native:

* A imagem que contém a aplicação executável deve preferencialmente ser imutável. De acordo com esse conceito uma vez construída a imagem, não deve ser alterada. Portanto, todas as configurações e customizações passam a ser feitas via variáveis de ambiente na plataforma.
* A aplicação pode usufruir de forma transparente de features providas por sidecars, containers que existem no mesmo pod e agregam funcionalidades de forma transparente à aplicação. Exemplos de fucionalidades são observabilidade, segurança, tracing, políticas de comunição, entre outros.
* A utilização de ferramentas centralizadoras de logs facilitará a analise e troubleshooting quando necessário;
* A aplicação deve disponibilizar uma API que permita que a plataforma identifique sua "saúde". Com base nisso a plataforma decidirá se um container está pronto para atender requests, se está estagnado e deve ser destruído para que um novo seja recriado.

## A jornada cloud-native

Entendidos os conceitos que giram em torno de uma aplicação cloud-native e após se ter um resumo superficial sobre o ciclo de vida de uma aplicação cloud-native, o próximo passo é entender as formas que estas aplicações podem ser entregues e as opções disponíveis no mercado. 

Há de se concordar que a transformação digital rumo a cloud acarreta não só uma mudança na forma de se codificar aplicações (time de desenvolvedores), mas se estende a outras áreas da organização de T.I. que precisará se reformular e lidar com novos desafios. Cada organização se encontra em um momento diferente, possui budgets diferentes e times com características e perfis muito distintos:

- O time de administradores de sistema (sysadmins, também conhecido como time de infraestrutura) não possui conhecimento técnico para entregar ambientes arquitetados com containers;
- O time de sysadmins  possui capacidade para instalar, gerenciar, escalar e fazer ajustes finos em uma plataforma de orquestração de containers como Kubernetes e derivados;
- O time de desenvolvimento entende as vantagens e desvantanges de se migrar para uma arquitetura de microservicos, e está tecnicamente preparada para lidar com os novos desafios;
- A empresa atualmente é mais composta por desenvolvedores, portanto, não possuem equipe para lidar especificamente com sistema operacional, virtualização ou containerização;
- O que se busca pelo time de negócios, é atingir resultados rápido e há budget disponível para aplicação em serviços já existentes;
- O time de desenvolvedores possui capacidade técnica para entregar aplicações cloud-native, porém, a infra-estrutura será mesclada, uma cloud-híbrida por falta de recursos físicos. 

Vamos falar sobre como *infraestrutura como serviço (Infrastructure As A Service - IaaS)*, *plataforma como serviço (Platform As A Service - PaaS)* e *software como serviço (Software As A Service - SaaS )* coexistem, seus prós e cons e como cada uma delas pode auxiliar o momento atual de sua organização na jornada cloud-native.



## IaaS, PaaS e SaaS: Uma perspectiva arquitetural

A melhor maneira de pensar no cloud em termo de abstração para o negócio, certamente é na analogia de uma pizza como serviço. Podemos partir do cenário onde temos a opção de preparar tudo em casa e ter que gerenciar todo o processo ou, sair para comer uma pizza e não se preocupar com nada. 

![](images/chapter_07_01.png)

Na imagem acima, temos dispostos quatro formatos:  on-premise, IaaS, PaaS e SaaS, onde as caixinhas brancas são as tarefas de nossa responsabilidade, e as azuis são de responsabilidade de terceiros. Essa mesma comparação pode ser feita, se trocarmos as tarefas de criação de pizza, por tarefas referentes a criação de software: Instalação e gerenciamento de sistema operacional, networking, storage, orquestração de serviços, gerenciamento de middleware, rurntime, pipelines de CI/CD, e até a criação da aplicação propriamente dita.

Quando nós falamos de cloud e seus serviços, note que quanto menor a abstração que utilizamos como serviço, por exemplo com IaaS, teremos a maior responsabilidade no processo de construção do software. Esse grande número de processos crescerá na mesma medida que em o número de servidores aumenta, e essa complexidade é diretamente proporcional relacionada aos riscos. Em contrapartida, quanto menor a abstração, maior o controle a possibilidade de customização da arquitetura e componentes.



![](images/chapter_07_02.png)



### IaaS

Com este approach, será possível atingir e usufruir de todos os benefícios de um ambiente de cloud, porém, toda a responsabilidade de manutenção ficará para a sua equipe de T.I. A empresa que provê o serviço de infra-estrutura deve garantir a comunicação entre os serviços é responsavel por lidar com quedas, problemas de hardware e consertos. A sua equipe deve assumir tarefas referentes a banco de dados, backup, escalonamento tanto vertical quanto horizontal, etc. Todos esses pontos aumentam a possibilidade de customização, e por outra perspectiva geram complexidade e por sua vez, mais risco. Nesta opção o hardware e garantia de seu funcionamento pertence a terceiros, mas o serviço é executado pelo seu time, desta forma, pode ter um custo mais reduzido se comparado à outras opções.

A orquestração no contexto de IaaS é a configuração, o gerenciamento e a coordenação automatizada dos serviços, aplicações e sistemas de computador. Ela tem com o objetivo de facilitar e abstrair o IaaS, fazendo com que fique mais próximo de um PaaS.

Exemplos de IaaS são Microsoft Azure, Google Compute Engine e [Amazon EC2](https://aws.amazon.com/ec2/). De uma maneira geral, se pode pensar nele como sendo um grande aluguel de máquinas no qual se pode pagar pelo uso delas, semelhante a nossa conta de luz. Para ilustrar isso, vamos analisar a realização do deploy de uma aplicação containerizada no ambiente da Amazon. A [primeira tarefa]((https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html)) é a criação de uma instância, atividade esta que requer cerca de sete passos. Em seguida, a [instalação do Docker](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html), para que assim seja possível criar [uma imagem para executar essa instância](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html#docker-basics-create-image). É possível ver que um simples deploy de uma aplicação, requer não apenas know-how extra como a execução de tarefas de sysadmin por parte do seu time.

> **TIP:** A respeito do gerenciamento de Kubernetes em IaaS, como bem sabemos, não existem uma solução simples para um problema complexo no mundo da arquitetura de software. O que existe é uma coleção de [histórias de terror relacionada a experiência com o Kubernetes](https://github.com/hjacobs/kubernetes-failure-stories). Um ponto importante é que [só porque a ferramenta é popular não quer dizer que ela seja a melhor opção para nós](https://pythonspeed.com/articles/dont-need-kubernetes/). Ou seja, mesmo sendo uma abstração dentro da infraestrutura como serviço, o Kubernetes não é uma solução simples e existem diversos riscos com a infraestrutura que devem ser considerados.



### PaaS

Uma redução drástica de complexidade para focar na criação do software, certamente, é a maior vantagem dentro do PaaS. Com ele, no geral, não é necessário se preocupar com a manutenção das máquinas, criação de rotina de backup, compra de licenças, etc. Todos os cuidados do [desenvolvimento será na criação do software](https://twitter.com/gilzow/status/1251308583427465216). Porém, o PaaS costuma apresentar custo mais elevado se comparado com serviços sem maior abstração. 

> **TIP:** Veja abaixo, algumas opções de PaaS atualmente disponíveis no mercado: 
>
> * [Platform.sh](platform.sh) :  É um PaaS que utiliza todos os conceitos de infraestrutura como serviço e também é orientado ao Git, além de ser possível realizar o deploy da aplicação deixando todo trabalho para a plataforma. Basicamente, a partir de três arquivos: [aplicação](https://docs.platform.sh/configuration/app-containers.html), [serviços](https://docs.platform.sh/configuration/services.html) e [rotas](https://docs.platform.sh/configuration/routes.html) podemos fazer o push para um repositório Git. Um simples push para o sistema remoto do Platform.sh criará automaticamente os containers da aplicação, dos serviços como banco de dados e as rotas da aplicação. Nesse caso a abstração é gigantesca e faz com que o time foque muito mais na aplicação central da empresa.
> * [Red Hat Openshift Online](https://www.openshift.com/products/online/): uma opção para se utilizar o OpenShift (a.k.a. [OKD](https://www.okd.io/)) como serviço. Neste PaaS, o OpenShift é disponibilizado na AWS e permite aos desenvolvedores de aplicações Ruby, PHP, Node.js e Java utilizarem seus runtimes e banco de dados de preferência para rodar suas aplicações. Possui uma opção self-service e free para desenvolvedores. 

### SaaS

O software como serviço é a oferta que provê uma solução mais rápida para determinado problema. Nesta oferta, o cliente opta por consumir um programa pronto para uso, e não precisa se preocupar com hospedagem, escalabilidade, etc e nem mesmo desenvolvimento. Toda a complexidade e o risco já foram resolvidos. No entanto, a customização é bem reduzida e a possibilidade de configuração depende diretamente do provedor. 

---

De uma maneira geral temos que pensar nos seguintes três princípios cíclicos que compara:

* Quanto maior a complexidade menor abstração;
* Quanto maior abstração menor é o risco;
* Quanto menor o risco menor é complexidade.
  

É verdade que existem vários benefícios na navegação nos mares da computação em nuvem, porém, é muito importante conhecer os tipos de serviços que a cloud disponibiliza e fazer uma análise profunda entre a complexidade de um serviço com o seu respectivo risco, além de quanto tempo o time está disposto a gastar para criar e manter toda a infraestrutura. O PaaS fornece uma grande vantagem de abstração de toda a infraestrutura e manutenção para que o desenvolvedor foque no seu negócio. O IaaS te garantirá uma grande flexibilidade e poder para instalar e configurar o que seu time deseja sem nenhum problema mesmo que tudo tenha que seja configurado manualmente. É muito importante que a quanto o time quanto a empresa tenha noção de que, independente da escolha haverão benefícios e desvantagens. É muito importante para que os arquitetos avaliem o que melhor se encaixa na instituição, afinal, cloud e computação não está relacionado a quando, mas como.

# Kubernetes - Quando usar e quando não usar

Como decidir quando é a hora de migrar seu workload para uma arquitetura conteinerizada e orquestrada por plataformas como Kubernetes? Entenda mudanças culturais e operacionais para tomar uma decisão mais embasada. Os pontos abaixo descritos são válidos para plataformas como Kubernetes e derivados, como OpenShift.

* Instalação e manutenção da plataforma de orquestração: em caso de adoção de IaaS, o time de sysadmins deve estar preparado para realizar e gerenciar a instalação de um ou mais clusters de Kubernetes. Deve estar atenado em melhores práticas de gerenciamento e configuração de clusters Kubernetes.

* Utilização da plataforma Kubernetes: em caso de IaaS ou PaaS, neste ponto já temos um Kubernetes instalado e pronto para ser utilizado. 

  * Da perspectiva de um desenvolvedor, a conterinerização da aplicação garante que o código que roda em sua máquina, funcionará também nos ambientes produtivos. Basta garantir que a imagem seja corretamente descrita. Além disto, uma vez absorvidos os conceitos de criação de uma aplicação cloud-native e das vantagens de um ambiente de orquestração, o desenvolvedor tem a vantagem da perspectiva da carreira, de estar apto a trabalhar em diferentes vendors, uma vez que há certa padronização na forma de trabalho. Por outro lado, a gestão deve estar ciente da curva de aprendizado inicial que a equipe enfrentará.

    > TIP: É recomendada a leitura do livro [Kubernetes patterns for desiging cloud-native apps](https://www.redhat.com/en/resources/oreilly-kubernetes-patterns-cloud-native-apps). Trás boas práticas na implementação de serviços cloud-native. 

  *  Da perspectiva do time de middleware ou sysadmins, o dia-a-dia passa a ser diferente. Não é mais necessário despender tempo aprendendo como entregar diferentes tipos de aplicação e desvendando suas respectivas peculiaridades e dependencias. Basta aprender a lidar com containers, e a forma de trabalho passa a ser padrão, independente da tecnologia utilizada nas aplicações. Por outro lado, deve-se entender conceitos de operators, segregação e segurança no ambiente Kubernetes, estratégias de deployment (rolling, blue-green, canary), como lidar com storages, etc.

  

## Operators



## Service Mesh

### Arquitetura

### Orquestração

### Ferramentas

# Serviços e riscos na implantação

