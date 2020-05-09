# Microservices



Microservices, ou em portugues, Microsserviços, são um novo jeito de construirmos nossas aplicações nos dias de hoje, pregam ser independentes e modeladas para o dominio de negócios (o que faz o DDD ser muito importante). Essas aplicações se comunicam entre si via protocolos de rede, e tem uma arquitetura que pode resolver problemas que você tenha. Os microsserviços são um dos tipos do SOA (service-oriented architeture), e te o foco de colocar fronteiras bem especificas de negocios e promover a entrega independente destes serviços, trazendo a vantagem de pode ser agnóstico de tecnologia. Resumindo no ponto de vista de tecnologia, são capacidades de negócios encapsuladas em um ou mais endpoints, mas seguindos os seguintes pilares:



* Flexibilidade
* Escalabidade
* Simplicidade



Neste cenário criamos aplicações que são desacopladas em pequenos serviços e cada um deles representando um objetivo de negócio. Onde eles podem ser desenvolvidos e fácilmente mantidos de maneira individual, e podendo usar diferentes tipos de linguagem de programação.



![Microservice](/home/sergiolopes/Documentos/LivroArquitetura/manual-arquiteto-moderno/images/chapter_04/Microservice.png)

​												*Arquitetura de Microserviços*





1.  Pontos de Atenção
    

Claro que não estamos falando de uma “bala de prata” que vai resolver todos os seus problemas. E um dos principais desafios quando se migra para o cenário de microserviços é a comunicação entre os mesmos.

A comunicação entre eles não é instantânea, então um ponto de preocupação que você terá que colocar em seus desenhos e soluções será a latência. E quanto mais seu ambiente crescer em complexidade, mais isso pode se tornar um problema. Lidar com o fato de que sua rede pode e vai falhar causa muita dor de cabeça.

Outro ponto é olhar a estratégia de fazer micro-serviços apenas no backend e esquecer que o Front também é parte disso. Ou seja, deixar a sua interface como um monolito gigante não vai lhe trazer vantagem nenhuma mesmo que todo o server-side esteja distrubuído.

Umas das vantagens dos micro-serviços que é ser agnóstico de tecnologia pode se transformar no seu calvário depois. Não adianta deixar que seu ambiente seja completamente poliglota, se depois ninguém vai saber manter e operar, e ainda começar a ter problemas de latência por uma linguagem mal utilizada. É preciso ser conciso e usar tecnologias que seu time e equipes de suporte dominem.

E aquela máxima então, que todos já devem ter ouvido: “Mas qual é o tamanho que o microserviço precisa ter?”. E a maioria das pessoas confunde o “Micro” com o tamanho e acha que ele precisa ser do menor tamanho possível, acha que realmente tem relação com a quantidade de bytes que ele ocupa.

Mas como é medido esse tamanho? Linhas de código? Tamanho em bytes? Nada disso, é o principio da responsabilidade única, esse é o tamanho que você precisa respeitar.

Outro ponto de atenção importante é quem será o dono do microserviço, pode até parecer bobeira, mas em ambientes altamente distribuídos, um serviço que está rodando em produção e não apresenta problema pode acabar ficando sem um dono e na hora de um problema ou possível manutenção, fica dificil achar quem é que assume a responsabilidade, então é preciso ter processos de governança bem fortes.

  

2.  Vantagens e desvantagens em relação ao monolito









## DTO ou não DTO?





O Data Transfer Object, conhecido como DTO, é alvo de grandes discussões, quando falamos sobre o desenvolvimento de aplicações Java. O DTO nasceu no mundo Java no EJB2 com dois propósitos: primeiro, contornar o problema de serialização do EJB e; segundo, definir implicitamente uma fase de montagem, na qual todos os dados que serão usados para apresentação passam por uma ordenação antes de irem efetivamente para a camada de apresentação. Porém, atualmente o EJB não é mais utilizado em larga escala, portanto, os DTOs também podem ser descartados? 

Existe uma grande discussão sobre os DTOs, afinal, num ambiente onde existem vários tópicos novos, como cloud e microservices, essa tal camada faz sentido? Quando falamos em uma boa arquitetura de software a resposta é praticamente unânime: depende do quão acoplado desejamos que a entidade esteja com a camada de visualização.



Pensando numa arquitetura básica em camadas e dividindo-a em três partes interconectadas, encontramos o famoso [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller).



![](images/chapter_01/image1.png)



Um ponto importante é que a arquitetura da camada MVC não é exclusiva para aplicações Web, como: Spring MVC ou JSF. Por exemplo, em uma aplicação RESTful as informações expostas como JSON funcionam como view, mesmo não tendo uma interface amigável.

Após explicarmos de maneira resumida o MVC, falaremos sobre as vantagens e desvantagens do uso do DTO. Pensando em aplicações em camadas, o DTO tem como objetivo, separar o model da view, sendo assim, as desvantagens do DTO são:

* Aumento a complexidade;
* Existe grande possibilidade do código ficar duplicado.



Adicionar uma nova camada causando impacto no layout, deixando-o mais lento, ou seja, cria problemas relacionados a desempenho.

Assim, sistemas simples e dos quais não precisam de um model rico como premissa, não utilizar o DTO traz grandes benefícios para a aplicação. Um ponto interessante é que muitos frameworks de serialização acabam obrigando que os atributos tenham métodos de acesso (getter e setter) sempre presentes e públicos, assim, em algum momento isso significará um impacto no encapsulamento e na segurança da aplicação.

A outra opção está em adicionar a camada DTO garantindo algumas vantagens, como o desacoplamento da view e do model como mencionado anteriormente:



* Deixa explícito quais campos irão para a camada da view. Sim, existem várias anotações em diversos frameworks que indicam quais campos não irão para a visualização. Porém, caso esqueçamos de fazer as anotações, podemos exportar um campo crítico de maneira acidental como a senha do usuário;
* Facilita o desenho em relação a orientação de objeto. Um dos pontos que o clean code deixa claro sobre orientação a objetos é que, o [POO](https://en.wikipedia.org/wiki/Object-oriented_programming) esconde os dados para expor o comportamento e, o encapsulamento, ajuda com isso;
* [Facilita o atualização do banco de dados](https://dzone.com/articles/orms-heroes-or-villains-indoors-the-architecture). Muitas vezes é importante refatorar ou migrar o banco de dados sem que essa alteração impacte o cliente. Essa separação facilita otimizações, modificações no banco de dados sem que isso impacte a visualização;
* Versionamento e retrocompatibilidade são pontos importantes, principalmente, quando se tem uma API de uso público e com vários clientes, assim é possível ter um DTO para cada versão e evoluir o modelo de negócio sem se preocupar;
* Outro benefício é a facilidade de trabalhar com o model rico e na criação de uma API que é [a prova de balas](https://dzone.com/articles/designing-a-bulletproof-code). Por exemplo, dentro do model podemos usar uma API do tipo money, porém, dentro da camada da view, exporto como sendo um simples objeto com apenas o valor monetário, ou seja, o bom e velho String no Java;
* [CQRS](https://martinfowler.com/bliki/CQRS.html): a Segregação de Responsabilidade de Consulta de Comando (Command Query Responsibility Segregation) separa a responsabilidade da escrita e da leitura de dados. Como fazer isso sem os DTOs?



No geral, adicionar uma camada significa desacoplar e facilitar a manutenção em detrimento de ter mais classes e mais complexidade. Uma vez que também temos que pensar na operação de conversão entre essas camadas. Essa é a razão, por exemplo, da existência do MVC, assim, é muito importante entender que tudo se baseia em impacto e tradeoffs em uma determinada aplicação ou situação. A não existência dessas camadas pode ser péssimo, acarretando um padrão [Highlander, there can be only one](https://www.youtube.com/watch?v=sqcLjcSloXs), no qual existe uma classe que detém todas as responsabilidades. Do mesmo jeito que o excesso de camadas torna o padrão similar a uma cebola, onde o desenvolvedor se arrepende ao passar por cada camada.



Uma crítica mais frequente ao DTOs se encontra no trabalho para realizar a conversão. A boa notícia é que existem diversos frameworks especializados neste trabalho, ou seja, não é necessário realizar a mudança manualmente. Nesse artigo escolheremos o framework [modelmapper](http://modelmapper.org/).



```xml
<dependency>
    <groupId>org.modelmapper</groupId>
    <artifactId>modelmapper</artifactId>
    <version>${modelmapper.version}</version>
</dependency>

```



Para ilustrar o conceito do DTO, criaremos uma aplicação utilizando JAX-RS conectado ao MongoDB, tudo isso, graças ao Jakarta EE, utilizando o Payara como servidor. Basicamente, iremos gerenciar um User com nickname, salary, birthday e lista de idiomas que o usuário fala (languages). Como trabalharemos com MongoDB no Jakarta EE, utilizaremos o Jakarta NoSQL.

Para mapear a entidade para persistir no banco de dados, as anotações que o Jakarta NoSQL utiliza é bastante semelhante ao JPA, com exceção do nome do pacote. Assim, temos as anotações `Entity`, `Id`, `Column` para identificar que a classe é uma entidade, o campo é uma chave e os campos que serão persistidos respectivamente.





```java
import jakarta.nosql.mapping.Column;
import jakarta.nosql.mapping.Convert;
import jakarta.nosql.mapping.Entity;
import jakarta.nosql.mapping.Id;
import my.company.infrastructure.MonetaryAmountAttributeConverter;

import javax.money.MonetaryAmount;
import java.time.LocalDate;
import java.util.Collections;
import java.util.List;
import java.util.Map;
import java.util.Objects;

@Entity
public class User {

    @Id
    private String nickname;

    @Column
    @Convert(MonetaryAmountAttributeConverter.class)
    private MonetaryAmount salary;

    @Column
    private List<String> languages;

    @Column
    private LocalDate birthday;

    @Column
    private Map<String, String> settings;

   // somente os métodos getter
}

```



Uma coisa importante é que o MongoDB não tem suporte para persistir o tipo `MonetaryAmount`, assim, criamos também uma classe que realiza a conversão de/para String de modo que criamos o `MonetaryAmountAttributeConverter` para realizar essa conversão de/para o banco MongoDB.



```java
import jakarta.nosql.document.Document;
import jakarta.nosql.mapping.AttributeConverter;
import org.javamoney.moneta.Money;

import javax.money.CurrencyUnit;
import javax.money.Monetary;
import javax.money.MonetaryAmount;
import java.math.BigDecimal;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class MonetaryAmountAttributeConverter implements AttributeConverter<MonetaryAmount, String> {

    @Override
    public String convertToDatabaseColumn(MonetaryAmount attribute) {
        if (attribute == null) {
            return null;
        }
        return attribute.toString();
    }

    @Override
    public MonetaryAmount convertToEntityAttribute(String dbData) {
        if (dbData == null) {
            return null;
        }
        return Money.parse(dbData);
    }
}

```



De uma maneira geral, não faz sentido fazer com que as entidades tenham getter e setter para todos os atributos, afinal, isso seria o mesmo que deixar o atributo público de maneira direta. Como o foco do artigo não é sobre DDD ou sobre o model rico, omitiremos os detalhes dessa entidade. Para o DTO, teremos todos os campos que a entidade possui, porém, para o view o `MonetaryAmount` será uma `String` e a birthday seguirá a mesma linha.



```java
import java.util.List;
import java.util.Map;

public class UserDTO {

    private String nickname;

    private String salary;

    private List<String> languages;

    private String birthday;

    private Map<String, String> settings;

    //getter e setter
}

```



O grande benefício do mapper é que não precisamos nos preocupar em fazer isso manualmente. O único ponto a salientar é que os tipos especiais, por exemplo, o `[MonetaryAmount](https://javamoney.github.io/apidocs/javax/money/MonetaryAmount.html)` do [money-api](https://javamoney.github.io/api.html) precisarão criar um converter para virar `String` e vice-versa.



```java
import org.modelmapper.AbstractConverter;

import javax.money.MonetaryAmount;

public class MonetaryAmountStringConverter extends AbstractConverter<MonetaryAmount, String> {

    @Override
    protected String convert(MonetaryAmount source) {
        if (source == null) {
            return null;
        }
        return source.toString();
    }
}


import org.javamoney.moneta.Money;
import org.modelmapper.AbstractConverter;

import javax.money.MonetaryAmount;

public class StringMonetaryAmountConverter extends AbstractConverter<String, MonetaryAmount> {

    @Override
    protected MonetaryAmount convert(String source) {
        if (source == null) {
            return null;
        }
        return Money.parse(source);
    }
}

```



Os converters estão prontos, o próximo passo é instanciar a classe que realiza a conversão do `ModelMapper`. A partir de agora, toda a aplicação poderá utilizar o mesmo mapper, sendo necessário utilizar apenas a anotação `Inject` como veremos a frente.



```java
import org.modelmapper.ModelMapper;

import javax.annotation.PostConstruct;
import javax.enterprise.context.ApplicationScoped;
import javax.enterprise.inject.Produces;
import java.util.function.Supplier;

import static org.modelmapper.config.Configuration.AccessLevel.PRIVATE;

@ApplicationScoped
public class MapperProducer implements Supplier<ModelMapper> {
    private ModelMapper mapper;

    @PostConstruct
    public void init() {
        this.mapper = new ModelMapper();
        this.mapper.getConfiguration()
                .setFieldMatchingEnabled(true)
                .setFieldAccessLevel(PRIVATE);
        this.mapper.addConverter(new StringMonetaryAmountConverter());
        this.mapper.addConverter(new MonetaryAmountStringConverter());
        this.mapper.addConverter(new StringLocalDateConverter());
        this.mapper.addConverter(new LocalDateStringConverter());
        this.mapper.addConverter(new UserDTOConverter());
    }

    @Override
    @Produces
    public ModelMapper get() {
        return mapper;
    }
}

```



Uma das grandes vantagens do uso do Jakarta NoSQL está na facilidade entre integrar o banco de dados. Por exemplo, nesse artigo utilizaremos o conceito de repositório, no qual criaremos uma interface e o Jakarta NoSQL irá se encarregar da implementação.



```java
import jakarta.nosql.mapping.Repository;

import javax.enterprise.context.ApplicationScoped;
import java.util.stream.Stream;

@ApplicationScoped
public interface UserRepository extends Repository<User, String> {
    Stream<User> findAll();
}

```



Por fim, faremos o recurso com o JAX-RS. Um ponto importantíssimo é a exposição de dados, que será feita a partir do DTO, ou seja, é possível realizar toda a modificação dentro da entidade sem que o cliente saiba, graças ao DTO. Como foi dito anteriormente, o mapper foi injetado e o método `map` facilita bastante essa integração entre o DTO e a entidade, sem necessidade de muito código adicional.





```Java
import javax.inject.Inject;
import javax.ws.rs.Consumes;
import javax.ws.rs.DELETE;
import javax.ws.rs.GET;
import javax.ws.rs.POST;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.Produces;
import javax.ws.rs.WebApplicationException;
import javax.ws.rs.core.MediaType;
import javax.ws.rs.core.Response;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;

@Path("users")
@Consumes(MediaType.APPLICATION_JSON)
@Produces(MediaType.APPLICATION_JSON)
public class UserResource {

    @Inject
    private UserRepository repository;

    @Inject
    private ModelMapper mapper;

    @GET
    public List<UserDTO> getAll() {
        Stream<User> users = repository.findAll();
        return users.map(u -> mapper.map(u, UserDTO.class))
                .collect(Collectors.toList());
    }

    @POST
    public void insert(UserDTO dto) {
        User map = mapper.map(dto, User.class);
        repository.save(map);
    }

    @POST
    @Path("id")
    public void update(@PathParam("id") String id, UserDTO dto) {
        User user = repository.findById(id).orElseThrow(() ->
                new WebApplicationException(Response.Status.NOT_FOUND));
        User map = mapper.map(dto, User.class);
        user.update(map);
        repository.save(map);
    }

    @DELETE
    @Path("id")
    public void delete(@PathParam("id") String id) {
       repository.deleteById(id);
    }
}

```

