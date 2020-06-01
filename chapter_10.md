# Destrinchando performance de aplicações

## Introdução e conceitos

Muitas empresas utilizam a abordagem "deixe o desempenho para depois" para tratar com possíveis problemas de performance que possam ocorrer no ambiente de produção para a fase de testes. Na fase de testes, é provável que não se leve em consideração a carga que a aplicação possa sofrer e o tempo de resposta esperado, pois isto não foi especificado nos requisitos não funcionais. Requisitos como o tempo de resposta de uma determinada operação ou a quantidade de acessos simultâneos. Além disso, temos o desafio de conciliar uma Arquitetura evolutiva juntamente com a Engenharia de desempenho ou performance. Na arquitetura evolutiva temos que ser ágeis e permitir constantes melhorias na arquitetura, deixando as decisões para última hora, enquanto a Engenharia de desempenho se preocupa em identificar possíveis gargalos e definir as tecnologias utilizadas já no início do projeto.

Antes de irmos mais a fundo, vamos rever alguns conceitos importantes sobre desempenho de aplicações.

**Throughput**: É a vazão ou taxa de transferência que sua aplicação consegue dar as requisições. Número de requisições por segundo/minuto ou hora. Quanto maior este número, melhor. Geralmente nas ferramentas de teste de carga, o Throughput é medido da seguinte forma: Throughput = (número de requisições) / (tempo total).

**Latência**:  É o tempo que leva para um pacote de dados ir de um ponto a outro. Ao contrário do Throughput, quanto maior a latência, pior a performance. Em sistemas distribuídos, este pode ser um grande problema.

Sistemas de alta performance tem como requisitos, alta confiabilidade, baixa latência e alta taxa de vazão. Isso parece comum para que vai desenvolver um serviço, porém não é uma tarefa das mais fáceis em manter e melhorar a performance à medida que o sistema vai sendo utilizado, novas funcionalidades são adicionadas, base de dados crescem.

## Agilidade versus Performance

Se a performance é a prioridade no projeto, considere verificar as dependências que esta utilizando ou que irá utilizar. Ao utilizar uma biblioteca para substituir alguma parte de código podemos ter a sensação de que estamos produzindo menos código, código limpo. Quanto menos código, menos bugs, certo? Nem sempre esta afirmação é correta. Você pode estar utilizando uma biblioteca que possui bugs tambem. Muitos desenvolvedores acabam não acompanhando a evolução da linguagem e ficam "amarrados" a certas biblioecas que utilizavam em versões passadas e que na nova versão da linguagem não se faz mais necessário. Bibliotecas podem trazer agilidade no desenvolvimento, mas será que elas degradam a performance ou deixam sua aplicação mais pesada? Avalie os prós e contras de cada dependência que irá utilizar no projeto. Entenda o seu funcionamento e busque pelo historico de bugs e vulnerabilidades.

### Verifique o que esta carregando na bagagem

Em um projeto Java utilizando o Maven por exemplo, podemos verificar a arvore de dependencias utilizando o comando `mvn dependency:tree` e ainda podemos filtrar somente por dependências de compilação, ex: `mvn dependency:tree -Dscope=compile`. 
 
``` asciidoc
 [INFO] --- maven-dependency-plugin:2.8:tree (default-cli) @ testcontainers ---
 [INFO] com.testcontainers:jar:1.0-SNAPSHOT
 [INFO] \- mysql:mysql-connector-java:jar:5.1.47:compile`
```
 
 É muito comum desenvolvermos serviços que são empacotados em um arquivo jar conhecido como _fatjar_, onde todas as dependencias são colocadas dentro do jar. Em muitos casos você não precisará daquela dependencia para rodar o seu serviço. Ela pode ser um dependencia de teste, ou estar provida por um servidor de aplicações. Então é sempre uma boa idéia verificar as dependencias do seu projeto.
 
Cuidado com a performance também quer dizer em muitos casos, implementar mais código em vez de utilizar alguma
 biblioteca que facilita o trabalho e acelera o desenvolvimento, pode lhe custar a performance la na frente.     

## Como medir a performance? 

Existem muitas formas de medir a performance da aplicação. Seja com um monitoramento em tempo real ou um teste de ‘stress’ antes de liberar uma funcionalidade em produção. Tudo vai depender do requisito não funcional solicitado. É claro que todo usuário quer ter sempre a resposta o mais rápido possível. Mas qual o limite aceitável? Esta é a medida que se deve ter em mente até mesmo antes de iniciar a codificação. Por exemplo: o tempo de login não pode ser superior a um segundo. Como posso medir isso?

### Capturando o tempo da requisição.

Neste capítulo, vamos utilizar a ferramenta [jMeter](https://jmeter.apache.org/) uma ferramenta muito utilizada para
 criar diversos tipos de teste de carga e medir o desempenho. O objetivo aqui não é ser um tutorial do jMeter, mas mostrar como é possível gerar e visualizar dados através dele. Abaixo um exemplo simples de medição de tempo de login considerando 10 usuários:
  
![](images/chapter_10_01.png)
 
Ao rodar o teste acima, verificamos no Relatório de Sumário os resultados:

![](images/chapter_10_02.png)

Alguns dados importantes neste momento:

**Amostras:** Número de requisições realizadas

**Média:** Número médio de tempo de todas as requisições

**Min:** Menor tempo dentre todas as requisições

**Max:** Maior tempo dentre todas as requisições

Ainda podemos ter gráficos mais ricos, utilizando o plugin [PerfMon](https://jmeter-plugins.org/wiki/PerfMon/) por exemplo:

![](images/chapter_10_03.png)

No gráfico acima podemos ver que a maioria das requisições ficou entre 600 e 700 milesegundos em um cenário de testes
 com 1000 requisições.
Podemos ter gráficos ainda mais bonitos e em tempo real podendo utilizar o [grafana](https://grafana.com/) como visualizador de
 graficos.

Veja que capturamos o tempo total de um processo de login, porém se o login não está em um tempo adequado ou queremos
 melhorar ainda mais o tempo, precisamos visualizar cada componente separado.

## Entendendo e separando os componentes.

Medimos o tempo total de um login e precisamos melhorar o tempo de resposta. Para isso, precisamos testar separadamente cada componente da arquitetura. É possivel que com apenas uma ferramenta não seja possível medir a performance da sua aplicação. É provável que você utilize uma ferramenta de carga para estressar a aplicação e várias outras para coletar os dados. Como exemplo, podemos ter uma aplicação que tem uma api para o login com acesso ao banco de dados. No entanto, podemos ter cenários bem mais complexos.



## Monitorando a performance em sistemas distribuídos

Sistemas distribuídos atualmente mais populares com a utilização de microsserviços, são complexos e difíceis de
 monitorar a performance.

https://www.jaegertracing.io/


## Mapeamento Objeto Relacional

No mundo Java, utilizamos o JPA para trabalhar com a camada de persistência em bancos de dados relacionais. Neste tópico abordaremos algumas boas praticas para obtermos uma boa performance na utilização deste padrão.

### Estratégia de geração de Id's

O gerador de identificador TABLE é muito ineficiente. Ele é mais genérico e portável para a maioria dos bancos de dados, porém requer uma transação de banco de dados separada, como também uma conexão separada para garantir que o processo de geração do identificador não esteja vinculado à transação que iniciou. Emprega o uso de bloqueios no nível de linha que são pesados em comparação com as usadas pelas estratégias de geração de identificador como IDENTITY ou SEQUENCE.
Portanto, se o banco de dados suportar sequences, é muito mais eficiente usar a estratégia SEQUENCE.

### Enums

https://vladmihalcea.com/the-best-way-to-map-an-enum-type-with-jpa-and-hibernate/

### associação unidirecional ou bidirecional

https://vladmihalcea.com/the-best-way-to-map-a-onetoone-relationship-with-jpa-and-hibernate/

### Problema de consulta N + 1

https://vladmihalcea.com/how-to-detect-the-n-plus-one-query-problem-during-testing/

## Ferramentas de solução de problemas ( troubleshooting tools) e monitoramento




Por fim, não tente resolver todos os problemas ao mesmo tempo. Comece construindo um
lista dos cinco principais contribuidores da hora e da queima da CPU e explore soluções.

O que é performance para você? Se você me perguntar sobre como melhorar a performance do seu serviço, eu vou lhe fazer algumas perguntas e vc talvez saia com a resposta.

Em todo o sistema?
Em um ponto crítico?
Reduzir uso de memória?
Processamento?
