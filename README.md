# Manual do Arquiteto Moderno

## Proposta
  
## Objetivo

Esta proposta é para a criação de um material que trás pontos de vista práticos propostos partir idéias existentes em renomados livros do mundo de desenvolvimento de software. Com base nestes livros e em resultados da aplicação destes conceitos na prática, será exposta a visão de arquitetos e engenheiros sêniors .
Um ponto importante sobre esse livro é que não há intenção de se introduzir conceitos iniciais, tendo isto em mente, o publico alvo é um público já experimentado no mundo do software. É recomendado, previamente, a leitura dos seguintes livros:

-   Java Efetivo;   
-   Clean Code;
-   DDD;
-   Clean Architecture;
-   Building Microservices;
-   The Twelve Factors;
  
Alinhando teoria e prática, este livro trará visões arquiteturais, motivações que levam a determinadas escolhas e seus trade-offs. Não serão detalhados tutoriais ou usos de frameworks específicos.

## Capítulos

A seguir está a lista de tópicos discutidos no decorrer deste livro. Foram selecionados tópicos atuais e relevantes que irão te ajudar a tomar melhores decisões dia a dia como arquiteto.

### Domain Driven Design (DDD)

* Analisando problemas de encapsulamento
* Quando criar Tipos
* Definindo APIs a prova de bala
* Java DSL e API fluente
* Builders: uma maneira intituiva e segura para se instânciar objetos

### Clean Code

* Dando adeus ao excesso de getters e setters
* SOLID na prática
* Lombok: problema ou solução?
* Clean Code não é bala de prata.

### Clean Architecture

* Design de código: segregue a lógica de negócio
* Testes
	* Testando com mock 
	* Teste de Unidades e de integração, qual a quantidade correta?
	* Teste lógica de negócio, não de frameworks.


### Refatoração

* Como negociar uma refatoração?
* Como lidar com o legado
* Estratégia do escoteiro
* Abandonar todo o código e fazer um novo?


### Microservices

* Pontos de atenção
* Vantagens e desvantagens em relação ao monolito
* Planejando uma migração de um monolito para uma arquitetura de microservices
* Teorema do CAP e seus impactos na arquitetura
* DTO ou não DTO?
* Melhores práticas para se definir camadas da aplicação
* Segurança
	* BASIC
	* Auth
	* Auth 2
* Paginação


### Banco de dados

* NoSQL vs SQL
* Dicas de modelagem relacional e não relacional
* Validação de dados é responsabilidade do banco ou da aplicação?
* Lidando com dados em uma arquitetura de microservicos


### Cloud

* IaaS, PaaS e o SaaS e os riscos de implantação
* O que é de fato uma aplicação cloud-native
* Kubernetes - Quando usar e quando não usar
* Service Mesh
	* Arquitetura
	* Orquestração
	* Ferramentas


### A importância de aprender os conceitos ao invés de novos frameworks

#### Precisamos falar sobre atualizações

* Os problemas de se permanecer em uma versão fixa de JVM
* Ciclo de atualizações dos frameworks
* Usando provas de conceito (POC) no ciclo de desenvolvimento
* Definindo a tecnologia do próximo projeto
* Balanceando a necessidade vs hype


### Destrinchando performance de aplicações

* Conceitos
* Testes de carga
* Logging

## Livro Colaborativo

A proposta é que este seja um livro colaborativo, onde haja uma revisão profissional de linguagem semântica e práticas de escrita sobre o conteúdo entregue. O grupo de autores será inicialmente composto por Otávio Santana, Karina Varela e Sérgio Lopes, sendo possível que novos colaboradores submetam conteúdo de sua expertise ao longo do processo de criação.


  
