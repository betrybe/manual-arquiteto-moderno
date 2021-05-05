# Refatoração

Após alguns anos de estrada, quem desenvolve percebe que passa grande parte do seu tempo lendo código e, na maioria das vezes, código escrito por outras pessoas. Sem muito esforço, percebe-se que esse tempo é superior ao tempo gasto escrevendo novas linhas de código, afinal de contas, é preciso entender o funcionamento do código atual, onde deverá fazer suas mudanças, e, principalmente, quais classes ou arquivos serão impactados por elas.

Não à toa, uma simples alteração no código, como adicionar um `if`, pode levar horas ou mesmo dias. Geralmente, esse alto custo para manter o software é consequência de uma péssima qualidade do código que foi escrito, afinal, quanto maior a dificuldade em ler e entender um trecho de código, maior será o tempo gasto para alterá-lo. E não se engane, mesmo um(a) dev que implementou a funcionalidade no sistema pode ter dificuldades para ler o código após alguns meses.

Para diminuir o custo nas alterações do sistema, é necessário **investir na qualidade e na clareza do código produzido**, seja no código atual, seja na introdução de novas linhas de código. Em outros termos, deve-se melhorar o código já pronto e que funciona em produção sem mudar o seu comportamento. Essa técnica, conhecida por grande número de profissionais, é chamada de **Refatoração** (ou, em inglês, Refactoring).

Uma das definições mais aceitas na indústria para "Refatoração" é a de **Martin Fowler**, em seu livro **Refactoring: Improving the Design of Existing Code**, segundo a qual:

> "Refatoração é uma técnica controlada para reestruturar um trecho de código existente, **alterando sua estrutura interna sem modificar seu comportamento externo**. Consiste em uma série de pequenas transformações que preservam o comportamento inicial. Cada transformação (chamada de refatoração) reflete em uma pequena mudança, mas uma sequência de transformações pode produzir uma significante reestruturação. Como cada refatoração é pequena, é menos provável que se introduza um erro. Além disso, o sistema continua em pleno funcionamento depois de cada pequena refatoração, reduzindo as chances de o sistema ser seriamente danificado durante a reestruturação." -- Martin Fowler

Com base nas palavras de Fowler, podemos entender que refatoração é o processo de modificar um trecho de código já escrito, executando pequenos passos (**baby steps**) sem modificar o comportamento atual do sistema. É uma técnica utilizada para melhorar algum aspecto do código, entre os quais podemos citar melhorias na clareza do código para facilitar a leitura, ou também ajustes no design das classes a fim de trazer maior flexibilidade ao sistema.

## Medo de alterar o código que não é seu

Se você trabalha com desenvolvimento, em algum momento da sua carreira, entrou ou entrará no meio de um projeto de software. Isso quer dizer que você chegou (ou chegará) de paraquedas para desenvolver, corrigir e manter funcionalidades em cima de uma base de código existente, que pode ter desde 6 meses até 20 anos de vida. 

Essa base de código provavelmente já passou pela mão de diversas outras pessoas, desde quem tem mais experiência até quem tem nível mediano e quem faz estágio, geralmente sob pressão e prazos surreais e apertados. Para piorar, dificilmente a equipe que iniciou o projeto e tomou decisões importantes de arquitetura e de design estará ainda na empresa. Ou seja, um sistema com 5 anos de idade provavelmente sofreu trocas no time completo 2 ou 3 vezes - no mínimo.

Essa alta rotatividade de profissionais traz inúmeros prejuízos à empresa, em especial ao código do sistema. Isso porque certos trechos de código críticos foram (e são) alterados, mantidos e evoluídos por diversos profissionais, desde quem domina o negócio de ponta a ponta a quem nem tanto; desde quem tem maestria nas tecnologias e frameworks utilizados a quem não tem a mínima ideia de como eles funcionam; e há também quem tenha utilizado o projeto como laboratório no curto tempo que permaneceu na empresa. Não é de se espantar que boa parte desse código seja repleto de gambiarras e duplicação de rotinas, abarrotada de comentários desatualizados, pouca clareza nas lógicas de negócio, alto acoplamento e baixa coesão. Não se assuste ao ter que manter uma classe ou um método com mais de 5 mil linhas de código macarrônico e totalmente ilegível.

Situações como essa são mais comuns do que se pode imaginar. Lidar com sistemas problemáticos dessa magnitude traz frustração e, principalmente, **medo** para um(a) dev que acaba de entrar na empresa, que terá que trabalhar no código "dos outros", programando no escuro, sem ter a mínima noção se sua última alteração impacta noutras partes do sistema ou, pior ainda, qual o tamanho desse impacto.

## A importância dos testes automatizados na hora refatorar

Quanto menos conhecimento sobre sistema o(a) desenvolvedor(a) tem, maior é sua insegurança na hora de alterar código e menores são as garantias de que suas alterações não causarão mais estragos ao sistema, que já se encontra frágil pelo tempo. Mesmo pequenas refatorações com o intuito de melhorar a clareza ou a simplicidade do código podem gerar novos bugs, reintroduzir bugs antigos no sistema ou impactar o sistema de forma negativa.

Se refatorar código traz riscos, como se pode garantir que erros não serão introduzidos nas suas refatorações?

A verdade é que refatorar código sem uma garantia concreta de que o comportamento atual não vá mudar é um ato imprudente, pois não adianta nada melhorar o código se algo que já existe é quebrado. Essa garantia pode ser obtida de diversas maneiras, como uma equipe de QA (testers) ou um ambiente de homologação para que clientes possam validar a mudança, mas sem dúvida uma das melhores alternativas a um preço justo é através do uso de **Testes Automatizados**. Uma bateria de testes de regressão mostraria rapidamente se uma determinada mudança (ou refatoração) no código mudou o comportamento da funcionalidade ou mesmo impactou outras partes do sistema. Com isso, qualquer erro introduzido seria imediatamente apontado, facilitando a correção a cada passo da refatoração de maneira imediata.

Como dito por Fowler, refatorar é o ato de aplicar pequenas transformações no código existente sem mudar seu comportamento externo. Assim como muitas técnicas de engenharia de software, a teoria é mais fácil do que a prática. Em um processo complexo, como desenvolvimento de software, temos que verificar frequentemente onde estamos e, se necessário, fazer correções no percurso. No caso da refatoração, a cada transformação precisamos verificar que tudo continua funcionando como esperado para só então partirmos para o passo seguinte. O contrário também é verdade: precisamos identificar se nosso último passo quebrou algo no sistema para então desfazê-lo e adotar uma estratégia diferente. Esse ciclo frequente de verificação em que aprendemos a cada passo recebe o nome de **Feedback Loop**. No mundo de metodologias ágeis, o *Feedback* funciona como a engrenagem motora para todos os quatro valores do Manifesto Ágil e suas derivações encontradas no mercado.

Quanto mais curto e frequente o feedback loop, mais natural o processo de refatoração se torna. Mas como encurtá-lo? Bem, existem diversas práticas e ferramentas que podem ajudar, como pair programming, servidor de integração contínua (CI) ou code review, mas entre elas eu quero destacar a cobertura de testes. Quando temos código coberto por testes, esse ciclo é encurtado de tal forma que fica natural para quem desenvolve repeti-lo a todo momento. Basta um teste de unidade quebrar que sabemos de imediato o que foi feito de errado. Perceba que os testes funcionam como uma rede de segurança para encorajar quem desenvolve a refatorar código e, principalmente, a manter a motivação para tornar essa prática uma constante no seu dia a dia.

Não vou mentir, é possível fazer refatorações sem uma linha de teste automatizado, muitas empresas e equipes fazem isso; contudo, não se pode ignorar que há grandes riscos envolvidos nessa prática que podem introduzir bugs ou gerar prejuízos para a empresa ou cliente final. Refatorar código sem testes torna tudo mais difícil e arriscado, consome mais tempo do que o necessário, desencoraja melhorias no código e ainda exige muito mais da pessoa desenvolvedora e da equipe. Para refatorar sem testes, a experiência de quem desenvolve conta bastante, seja para fazer mudanças com passos pequenos e seguros, analisar e mensurar o impacto de suas (possíveis) mudanças, seja para simplesmente decidir que não vale a pena refatorar determinado trecho de código.

Apesar de a experiência de quem desenvolve ser um fator importante na hora de refatorar código não coberto por testes, dependendo da empresa e dos processos adotados por ela, as chances são de que gestores não permitam modificar um código que funciona e já se encontra em produção.

O tema testes automatizados é muito amplo e merece um capítulo completo ou mesmo um livro - e por sinal existem alguns muito bons. Mas para não alongar neste tópico, deixo o seguinte questionamento: **na hora de refatorar, quão longe podemos ir, ou quão ousados podemos ser sem uma boa cobertura de testes?**

## Refatoração contínua do código

Um ponto importante é que dificilmente a empresa ou stakeholder permitirá que se crie uma tarefa inteira para realizar a refatoração de um projeto. Afinal, para o(a) usuário(a) final, pouco impacta a tecnologia ou a qualidade de código que o projeto está usando. Uma boa estratégia é a famosa técnica de escoteiro. Essa técnica se baseia em refatorar os códigos relacionados que precisam de melhoria e clareza, ao passar por uma história que agrega valor para o produto. Sempre salientando que, por segurança, é importante adicionar testes antes de fazer qualquer refatoração.

## Qual sua motivação para refatorar o código?

Existem diversas motivações para realizar uma refatoração do código, e listamos aqui alguns dos principais:

* **Refatorando para legibilidade e flexibilidade**: Legibilidade traz diversos benefícios para o ciclo de vida de uma aplicação, seja como agilidade na manutenabilidade, seja como facilidade de depurar, além do fator de bem-estar dentro do time. Vale ressaltar que essa manutenção dificilmente entrará como uma história ou atividade, porém, se abster totalmente nessa atividade acarreta o fator "Janelas partidas", ou seja, à medida que o código for piorando a legibilidade, maior é a tendência de que a próxima pessoa que realizar manutenção abrirá mão da qualidade para se livrar dessa atividade o mais rápido possível até que o código seja um risco pelo fator da falta de legibilidade.
* **Refatorando para performance**: Performance é sempre importante, porém, deve-se considerar se essa melhoria resultará em maior complexidade ou algum outro eventual problema de consistência de dados. Como diria Donald Knuth, "a otimização prematura é a raiz de todo o mal". Assim, toda melhoria de performance é importante, porém leve em consideração os seus impactos e se isso faz sentido nesse momento.
* **Refatorando para remover duplicidade**: Reduzir código dentro do projeto é uma boa motivação. Quando somos juniores tendemos a ficar felizes quando adicionamos código, e quando ficamos mais experientes ficamos felizes com a redução de código de que precisamos. Uma das estratégias de reduzir o código é, justamente, evitar a duplicação de código. Isso te garantirá um único ponto para refatoração: facilidade de teste, além de performance, principalmente se você trabalha dentro da JVM, uma vez que existe o fator JIT.
* **Refatorando para usar uma biblioteca**: O melhor código, certamente, é aquele que não escrevemos. Reduzir a quantidade de código siginifica reduzir a complexidade do seu lado, além de diminuir os pontos que darão erro. Isso é excelente, principalmente quando essa biblioteca já esteja disponível dentro do seu projeto. Vale salientar que excesso de dependência também pode ser perigoso. Lembre-se de que, quanto maiores as suas dependências, maior a estratégia para atualizar as bibliotecas (além do conhecido problema de "Jar Hell").

# Concluindo    

Com isso concluímos o tópico sobre refatoração do código. Abordamos pontos importantes, como a motivação de se refatorar o código além dos seus riscos. Como bons profissionais de desenvolvimento, é sempre importante colocar na balança as motivações e evitar problemas futuros de manutenabilidade e legibilidade do código. Como sempre, o bom senso será sua melhor ferramenta.