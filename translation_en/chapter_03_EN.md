PT

# Clean code

São claros e reconhecidos os grandes benefícios obtidos por meio da utilização de boas práticas no código. Algo que logo se nota ao se trabalhar com um código limpo e fluido é a melhor legibilidade de código e a maior facilidade de manutenção. Mas, ao construir aplicações e discutir práticas de arquitetura, há um outro ponto que não podemos deixar de lado: a integridade dos dados que serão manipulados. Será que as boas práticas de código refletem de forma positiva na integridade desses dados? Um dos primordiais tópicos cobertos pelo livro [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship-ebook/dp/B001GSTOAM) é que, diferentemente da programação estruturada, a orientação a objetos expõe comportamento escondendo os dados. Com isso em mente, neste capítulo discorreremos sobre os benefícios da utilização de boas práticas de código e as vantagens obtidas ao se implementarem modelos ricos.

## Modelos Ricos

Levando em consideração uma aplicação que adota práticas de clean code, os ganhos de performance são consideráveis, uma vez que, por não trafegar e validar dados em bancos de dados, há economia tanto de processamento de rede quanto de hardware. Durante o desenvolvimento, há vários desafios em se "blindar" o código, como os inúmeros conceitos técnicos que são aplicados, e junção de negócio com o interesse na criação de uma linguagem ubíqua. Que tal aprofundar um pouco mais nesse conceito através da criação de um exemplo? 

Vamos criar uma aplicação de gestão de jogadores de futebol. Teremos o conceito de `time`, e dentro de um `time` teremos as seguintes informações:

- O nome do jogador, representado pelo atributo <code>name</code> da classe <code>Player</code>;
- A posição (`position` da classe `Player`) do jogador (goleiro, ataque, defesa e meio de campo); 
- O ano em que o jogador entrou no time, representado pelo atributo <code>start</code> da classe <code>Player</code>; 
- O ano em que o jogador saiu do time, representado pelo atributo <code>end</code> da classe <code>Player</code>; 
- O número de gols que o jogador realizou no time, representado pelo atributo <code>goals</code> da classe <code>Player</code>;
- O salário do jogador, representado pelo atributo <code>salary</code> da classe <code>Player</code>;
- O email para contato, no atributo `email`;
- A relação com o time, representada pela classe `Team`;	
- Lembrando a regra que diz que _um time não deve ter mais que vinte membros_.

Com base nas informações citadas, a primeira versão do modelo é mostrada a seguir:

```java
import java.math.BigDecimal;

public class Player {

    String name;

    Integer start;

    Integer end;

    String email;

    String position;

    Integer goals;

    BigDecimal salary;
}

public class Team {

    String name;

    List<Player> players;
}

```

À primeira vista, podemos notar possíveis melhorias no código. Note que o atributo `position` é do tipo `String`, o que não faz sentido, uma vez que as posições de jogadores serão sempre as mesmas: goleiro (goalkeeper), defesa (defender), meio de campo (midfielder) e atacante (forward). Podemos melhorar esse cenário aplicando o conceito de [Value Objects](https://martinfowler.com/bliki/ValueObject.html) através da utilização de um `Enum`:

```java
public enum Position {
    GOALKEEPER, DEFENDER, MIDFIELDER, FORWARD;
}
```

Uma vez que definimos o modelo inicial, nosso próximo passo é analisar segurança e encapsulamento dos objetos. Esta é umas das bases para um bom código orientado a objetos: a possibilidade de "esconder" os dados e expor apenas os comportamentos. A criação dos métodos assessores (setter) devem ser considerados como último recurso para acesso ao objeto. Um outro ponto é: deve-se avaliar a necessidade de esses métodos serem públicos, ou seja, considere criá-los como `default` ou `protected`, caso seja possível. 

Sob essa perspectiva, vamos analisar algumas regras de negócio da nossa aplicação de exemplo: 

- Os jogadores não mudam de e-mail, nome e de posição, e só é possível marcar um gol por vez. Repare que a criação de métodos setters não são importantes para esses atributos. 
- O ano de saída pode estar vazio, porém, quando preenchido, deverá ser posterior à data de entrada; 
- Apenas o time (representado pela classe `Team`) é responsável por gerenciar os jogadores (`Player`), ou seja, será necessário criar métodos para adicionar jogadores ao time. 

Vamos trabalhar na classe `Team`. Teremos de criar de um método para adicionar vários players (`Player`) no `Team`. Também precisaremos de um método (get) que retorne os players. É importante validar a entrada de players, uma vez que não faz sentido adicionar um player nulo. Devemos também garantir que apenas a classe `Team` adicione/remova players. Para isso, devemos garantir que essa classe retorne uma lista apenas de leitura, do contrário, teremos problemas com encapsulamento. Uma maneira de resolver isso seria retornar uma lista como no exemplo a seguir:

Let’s work in the class `Team`. We will have to create a method to add multiple players (`Player`) to `Team`. We will also need a method (get) that returns players. It is important to validate the entry of players since it does not make sense to add a null player. We must also ensure that only the class `Team` adds/removes players. To do that, we must ensure this class returns a read-only list; otherwise, we will have encapsulation issues. One solution would be to return a list as in the following example:

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Objects;

public class Team {

    private static final int SIZE = 20;

    private String name;

    private List<Player> players = new ArrayList<>();

    @Deprecated
    Team() {
    }

    private Team(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void add(Player player) {
        Objects.requireNonNull(player, "player is required");

        if (players.size() == SIZE) {
            throw new IllegalArgumentException("The team is full");
        }
        this.players.add(player);
    }

    public List<Player> getPlayers() {
        return Collections.unmodifiableList(players);
    }

    public static Team of(String name) {
        return new Team(Objects.requireNonNull(name, "name is required"));
    }
}

```

INFO: Muitos frameworks precisam que o construtor padrão exista por questão de realizar a criação de uma instância a partir da API de Reflection. Como o objetivo é desencorajar o uso do construtor padrão ao invés do uso do método de construção, o construtor será anotado com [Deprecated](https://www.baeldung.com/java-deprecated). A anotação Deprecated indica que esse método não deve ser utilizado.

Com relação à classe `Player`, todos os atributos terão getters padrão, com exceção do atributo <code>end</code>, que terá um tratamento especial: o <code>getEnd</code> retornará um `Optional`, uma vez que o <code>end</code> pode ser nulo. Outro ponto é o método <code>setEnd</code>, que só será íntegro caso o último ano seja igual ou maior que o ano de início do player, ou seja, se ele começou a jogar em 2004, não faz sentido ele ter terminado de jogar em 2002. Desse modo, o setter terá de fazer a validação no momento do acesso.

```java
import java.math.BigDecimal;
import java.util.Objects;
import java.util.Optional;

public class Player {

    private String name;

    private Integer start;

    private Integer end;

    private String email;

    private Position position;

    private BigDecimal salary;

    private int goal = 0;


    public String getName() {
        return name;
    }

    public Integer getStart() {
        return start;
    }

    public String getEmail() {
        return email;
    }

    public Position getPosition() {
        return position;
    }

    public BigDecimal getSalary() {
        return salary;
    }

    public Optional<Integer> getEnd() {
        return Optional.ofNullable(end);
    }

    public void setEnd(Integer end) {
        if (end != null && end <= start) {
            throw new IllegalArgumentException("the last year of a player must be equal or higher than the start.");
        }
        this.end = end;
    }
}

    public int getGoal() {
        return goal;
    }

public void goal() {
       goal++;
}

```

Uma vez definidos os métodos de acesso, o próximo passo está na criação das instâncias de `Team` e `Player`. Como boa parte das informações são obrigatórias para se criar uma instância válida, o primeiro movimento natural seria a criação de um método construtor. Isso é válido com objetos simples, como o `Team`, porém a class `Player` tem mais complexidades, como:

- A quantidade de parâmetros: pode gerar um construtor [polyadic](https://medium.com/coding-skills/clean-code-101-meaningful-names-and-functions-bf450456d90c) (construtor com mais de três argumentos) .
- A complexidade das validações: não faz sentido um player começar a jogar antes de 1863, uma vez que o esporte nasceu nesse ano.

Para resolver esses problemas, executaremos dois passos: utilização de tipos customizados e aplicação do padrão Builder.

### Tipos customizados

A primeira estratégia é a criação de um tipo. Essa estratégia faz sentido quando um objeto tem grande complexidade, como por exemplo objetos que lidam com dinheiro e data. Trazer essa complexidade para entidade pode quebrar o princípio da responsabilidade única. Existe um artigo muito bom escrito por Martin Fowler, [When Make a Type](https://martinfowler.com/ieeeSoftware/whenType.pdf), que explica as vantagens de tais recursos. Também não queremos reinventar a roda, portanto, para representar ano e dinheiro utilizaremos as APIs de Date/Time que nasceram do Java 8 e a Money-API. O único tipo que precisaremos criar é o tipo `e-mail`, como mostra o código a seguir:

```java
import java.util.Objects;
import java.util.function.Supplier;
import java.util.regex.Pattern;

public final class Email implements Supplier<String> {

    private static final String EMAIL_PATTERN =
            "^[_A-Za-z0-9-\\+]+(\\.[_A-Za-z0-9-]+)*@"
                    + "[A-Za-z0-9-]+(\\.[A-Za-z0-9]+)*(\\.[A-Za-z]{2,})$";

    private static final Pattern PATTERN = Pattern.compile(EMAIL_PATTERN);

    private final String value;

    @Override
    public String get() {
        return value;
    }

    private Email(String value) {
        this.value = value;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }
        Email email = (Email) o;
        return Objects.equals(value, email.value);
    }

    @Override
    public int hashCode() {
        return Objects.hashCode(value);
    }

    @Override
    public String toString() {
        return value;
    }

    public static Email of(String value) {
        Objects.requireNonNull(value, "o valor é obrigatório");
        if (!PATTERN.matcher(value).matches()) {
            throw new IllegalArgumentException("Email nao válido");
        }

        return new Email(value);
    }
}

```

Agora iremos utilizar esses tipos na classe `Player`. Ela ficará com o seguinte formato:

```java
import javax.money.MonetaryAmount;
import java.time.Year;
import java.util.Objects;
import java.util.Optional;

public class Player {

    private String id;

    private String name;

    private Year start;

    private Year end;

    private Email email;

    private Position position;

    private MonetaryAmount salary;

//...

}

```

### Builder Pattern 

O segundo e último passo será utilizar o padrão Builder para fazer com que as regras de validação estejam dentro dessa classe. Ao adotar esse padrão, garantiremos que o objeto só será instanciado quando os dados forem realmente válidos, além de colocar a responsabilidade dessa criação em uma classe. Esse padrão é muito interessante porque, além de garantir a responsabilidade única, diminui a chance de um dos parâmetros serem trocados acidentalmente.	 	 

Um ponto importante é que muitos dos frameworks de mapeamento, como o Hibernate, OpenJPA etc., requerem getters e setters, além do construtor padrão. Uma solução para isso seria criar um construtor com todos os parâmetros necessários em privado e um outro como default e com a anotação Deprecated, deixando claro para o desenvolvedor que aquele construtor não é bem-visto para uso. Veja no exemplo abaixo que o Builder fica como inner class da classe `Player`:

```java
import javax.money.MonetaryAmount;
import java.time.Year;
import java.util.Objects;
import java.util.Optional;

public class Player {

    static final Year SOCCER_BORN = Year.of(1863);

    //hide

    private Player(String name, Year start, Year end, Email email, Position position, MonetaryAmount salary) {
        this.name = name;
        this.start = start;
        this.end = end;
        this.email = email;
        this.position = position;
        this.salary = salary;
    }

    @Deprecated
    Player() {
    }

    public static PlayerBuilder builder() {
        return new PlayerBuilder();
    }

    public static class PlayerBuilder {

        private String name;

        private Year start;

        private Year end;

        private Email email;

        private Position position;

        private MonetaryAmount salary;

        private PlayerBuilder() {
        }

        public PlayerBuilder withName(String name) {
            this.name = Objects.requireNonNull(name, "name is required");
            return this;
        }

        public PlayerBuilder withStart(Year start) {
            Objects.requireNonNull(start, "start is required");
            if (Year.now().isBefore(start)) {
                throw new IllegalArgumentException("you cannot start in the future");
            }
            if (SOCCER_BORN.isAfter(start)) {
                throw new IllegalArgumentException("Soccer was not born on this time");
            }
            this.start = start;
            return this;
        }

        public PlayerBuilder withEnd(Year end) {
            Objects.requireNonNull(end, "end is required");

            if (start != null && start.isAfter(end)) {
                throw new IllegalArgumentException("the last year of a player must be equal or higher than the start.");
            }

            if (SOCCER_BORN.isAfter(end)) {
                throw new IllegalArgumentException("Soccer was not born on this time");
            }
            this.end = end;
            return this;
        }

        public PlayerBuilder withEmail(Email email) {
            this.email = Objects.requireNonNull(email, "email is required");
            return this;
        }

        public PlayerBuilder withPosition(Position position) {
            this.position = Objects.requireNonNull(position, "position is required");
            return this;
        }

        public PlayerBuilder withSalary(MonetaryAmount salary) {
            Objects.requireNonNull(salary, "salary is required");
            if (salary.isNegativeOrZero()) {
                throw new IllegalArgumentException("A player needs to earn money to play; otherwise, it is illegal.");
            }
            this.salary = salary;
            return this;
        }

        public Player build() {
            Objects.requireNonNull(name, "name is required");
            Objects.requireNonNull(start, "start is required");
            Objects.requireNonNull(email, "email is required");
            Objects.requireNonNull(position, "position is required");
            Objects.requireNonNull(salary, "salary is required");

            return new Player(name, start, end, email, position, salary);
        }

    }
}

```

Dessa forma, teremos a certeza de que, quando a instância de um jogador (`Player`) for criada, ela será consistente e à prova de falhas.

```java
     CurrencyUnit usd = Monetary.getCurrency(Locale.US);
     MonetaryAmount salary = Money.of(1 _000_000, usd);
     Email email = Email.of("marta@marta.com");
     Year start = Year.now();
     Year end = start.plus(-1, ChronoUnit.YEARS);

     Player marta = Player.builder().withName("Marta")
         .withEmail(email)
         .withSalary(salary)
         .withStart(start)
         .withPosition(Position.FORWARD)
         .build();

```

Podemos aplicar esse mesmo princípio na criação de um time (`Team`):

```java
  Team bahia = Team.of("Bahia");
  Player marta = Player.builder().withName("Marta")
      .withEmail(email)
      .withSalary(salary)
      .withStart(start)
      .withPosition(Position.FORWARD)
      .build();

  bahia.add(marta);

```


### Utilizando Bean Validation

Uma outra maneira de garantir a validação dos dados é utilizando recursos do [Bean Validation](https://beanvalidation.org/). O Bean Validation é uma especificação Java cujo objetivo é garantir que os atributos dentro da classe sejam válidos através da utilização exclusiva de anotações, ou seja, de uma maneira simples e reaproveitável. 

É importante salientar que todas as regras citadas anteriormente continuam válidas com o uso dessa API e que é importante que sejam mantidas todas as boas práticas de orientação a objetos, como encapsulamento. Assim, o uso do Bean Validation pode ser visto como uma dupla verificação ou para que o Builder execute as validações oriundas do framework, de modo que ele só retorne a instância caso todas as validações sejam bem-sucedidas.

```java
import javax.money.MonetaryAmount;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.PastOrPresent;
import javax.validation.constraints.PositiveOrZero;
import java.time.Year;
import java.util.Objects;
import java.util.Optional;

public class Player {

    static final Year SOCCER_BORN = Year.of(1863);

    @NotBlank
    private String name;

    @NotNull
    @PastOrPresent
    private Year start;

    @PastOrPresent
    private Year end;

    @NotNull
    private Email email;

    @NotNull
    private Position position;

    @NotNull
    private MonetaryAmount salary;

    @PositiveOrZero
    private int goal = 0;

    //continue

}

import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Objects;

public class Team {

    static final int SIZE = 20;

    @NotBlank
    private String name;

    @NotNull
    @Size(max = SIZE)
    private List<Player> players = new ArrayList<>();

    //continue

}

```

O código fonte desse exemplo está disponível no repositório: [https://github.com/soujava/bulletproof](https://github.com/soujava/bulletproof).

TIP: Lembre-se da importância dos testes de unidade em todo o processo de desenvolvimento!

Com a criação desse exemplo, demonstramos que apenas ao utilizar conceitos de orientação a objetos você estará criando um código à prova de falhas. Até esse momento, todas as práticas funcionam de maneira agnóstica ao banco de dados, ou seja, podemos utilizar essas boas práticas independentemente da tecnologia de persistência que será adotada.  

## Lombok: problema ou solução?

De uma maneira geral, o projeto Lombok é uma biblioteca famosa e conhecida por reduzir a quantidade de linhas de código através da utilização de anotações. Essa ferramenta tem seus benefícios, uma vez que a redução de código pode facilitar a leitura. Um exemplo é a anotação `Builder`, que permite a criação de classes mais intuitiva e no padrão Builder. Por outro lado, também vemos alguns problemas no uso desse projeto, itens estes que estão listados a seguir:

* O código é gerado pelo projeto Lombok e não é visualizado pela IDE. Qualquer exceção que envolva essas classes tenderá a ser difícil de ser seguida pela pilha de exceção;

* Existem anotações como `@Data` que permitem a quebra do encapsulamento das classes. Com isso, você deixará de programar utilizando o paradigma de programação orientada a objetos. É importante salientar as informações trazidas no capítulo 7 do livro Clean Code: a maior diferença entre OOP e programação estruturada é que, na primeira opção, nós escondemos os dados para expor o comportamento;

* Boa parte dos códigos como getters e setters podem ser gerados pela IDE;

* Os poderes das anotações são muito tentadores, porém, encapsulamento não é sobre ter o atributo privado e com getter e setter públicos, mas sim sobre garantir que atributos sejam acessados com a menor visibilidade possível.

Queremos deixar claro que o objetivo deste tópico não é classificar o Lombok e seu relacionamento ou não com as boas práticas de programação. A intenção é mostrar que, apesar de possuir suas vantagens - expostas em diversos sites -, é importante ter em mente os problemas acarretados com a adoção dessa tecnologia.

EN

# Clean code

The great benefits obtained through the use of good coding practices are clear and recognized. Something you can quickly notice when working with clean and fluid code is better code readability and easier maintenance. However, when building applications and discussing architectural practices, there is another point that we can’t ignore: the integrity of the data that will be manipulated. Do good code practices reflect positively on that data integrity? One of the primary topics covered by the book [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship-ebook/dp/B001GSTOAM) is that, unlike structured programming, object orientation exposes behavior by hiding the data. With that in mind, in this chapter, we will discuss the advantages of using good coding practices and the benefits gained from implementing rich models.

## Rich Models

Considering an application that adopts clean code practices, the performance gains are considerable, seeing that, by not running and validating data in databases, there are savings in both network and hardware processing. During development, there are several challenges in "shielding" the code, such as the countless technical concepts that are applied — also, joining business rules with interest in creating a ubiquitous language. How about exploring this concept by making an example?

We are going to create a football player management application. We are going to have the concept of a `team`, and within a `team` the following information:

- The name of the player, represented by the attribute <code>name</code> of the class <code>Player</code>;
- The position (`position` of the class `Player`) of the player (goalkeeper, forward, defender, and midfielder);
- The year the player joined the team, represented by the attribute <code>start</code> of the class <code>Player</code>;
- The year the player left the team, represented by the attribute  <code>end</code> of the class <code>Player</code>;
- The number of goals the player scored on the team, represented by the attribute <code>goals</code> of the class<code>Player</code>;
- The salary of the player, represented by the attribute <code>salary</code> of the class <code>Player</code>;
- The contact email, in the attribute `email`;
- The relationship with the team, represented by the class `Team`;
- Keeping in mind the rule that says _a team should not have more than twenty members_.

Based on the information above, the first version of the model is shown below:

```java
import java.math.BigDecimal;

public class Player {

    String name;

    Integer start;

    Integer end;

    String email;

    String position;

    Integer goals;

    BigDecimal salary;
}

public class Team {

    String name;

    List<Player> players;
}

```

At first glance, we can notice possible code improvements. Note that the attribute `position` is of the type `String`, which does not make sense, since the players’ positions are always the same: goalkeeper, defender, midfielder, and forward. We can improve this scenario by applying the concept of [Value Objects](https://martinfowler.com/bliki/ValueObject.html) through the use of an `Enum`:

```java
public enum Position {
    GOALKEEPER, DEFENDER, MIDFIELDER, FORWARD;
}
```

Once we have defined the initial model, our next step is to analyze the objects’ security and encapsulation. That is one of the bases for good object-oriented coding: the possibility of "hiding" data and exposing only behaviors. The creation of accessor methods (setter) should be considered only as a last resort for accessing the object. Another point is: the need for these methods to be public should be evaluated, i.e., consider creating them as `default` or `protected`, if possible.

From this perspective, let's look at some business rules for our sample application:

- Players do not change their email, name, and position, and it is only possible to score one goal at a time. Note that the creation of setter methods is not essential for these attributes.
- The outgoing year may be empty, but, when filled, it must be after the date of entry;
- Only the team (represented by the class `Team`) is responsible for managing the players (`Player`), i.e., it will be necessary to create methods to add players to the team.

Let’s work in the class `Team`. We will have to create a method to add multiple players (`Player`) to `Team`. We will also need a method (get) that returns players. It is important to validate the players’ entry since it does not make sense to add a null player. We must also ensure that only the class `Team` adds/removes players. To do that, we must ensure this class returns a read-only list; otherwise, we will have encapsulation issues. One solution would be to return a list as in the following example:

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Objects;

public class Team {

    private static final int SIZE = 20;

    private String name;

    private List<Player> players = new ArrayList<>();

    @Deprecated
    Team() {
    }

    private Team(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void add(Player player) {
        Objects.requireNonNull(player, "player is required");

        if (players.size() == SIZE) {
            throw new IllegalArgumentException("The team is full");
        }
        this.players.add(player);
    }

    public List<Player> getPlayers() {
        return Collections.unmodifiableList(players);
    }

    public static Team of(String name) {
        return new Team(Objects.requireNonNull(name, "name is required"));
    }
}

```

INFO: Many frameworks need the default constructor to exist for the sake of creating an instance from the reflection API. Since the goal is to discourage using the default constructor instead of the construction method, the constructor will be noted with [Deprecated](https://www.baeldung.com/java-deprecated). The Deprecated annotation indicates that this method should not be used.

As far as the class `Player` is concerned, all attributes will have default getters, except for the attribute <code>end</code>, which will have a special treatment: <code>getEnd</code> will return an `Optional`, once <code>end</code> can be null. Another point is the method <code>setEnd</code>, which will only be an integer if the last year is equal to or greater than the player's starting year, i.e., if he started playing in 2004, he could not have finished playing in 2002. Thus, the setter will have to validate at the moment of access.

```java
import java.math.BigDecimal;
import java.util.Objects;
import java.util.Optional;

public class Player {

    private String name;

    private Integer start;

    private Integer end;

    private String email;

    private Position position;

    private BigDecimal salary;

    private int goal = 0;


    public String getName() {
        return name;
    }

    public Integer getStart() {
        return start;
    }

    public String getEmail() {
        return email;
    }

    public Position getPosition() {
        return position;
    }

    public BigDecimal getSalary() {
        return salary;
    }

    public Optional<Integer> getEnd() {
        return Optional.ofNullable(end);
    }

    public void setEnd(Integer end) {
        if (end != null && end <= start) {
            throw new IllegalArgumentException("the last year of a player must be equal or higher than the start.");
        }
        this.end = end;
    }
}

    public int getGoal() {
        return goal;
    }

public void goal() {
       goal++;
}

```

Once the access methods are defined, the next step is to create `Team` and `Player` instances. As much of the information is required to create a valid instance, the first natural move would be to create a constructor method. This is valid with simple objects, such as `Team`; however, the `Player` class has more complexities, such as:

- The number of parameters: can generate a [polyadic](https://medium.com/coding-skills/clean-code-101-meaningful-names-and-functions-bf450456d90c) constructor (a constructor with more than three arguments).
- The validations’ complexity: it makes no sense for a player to start playing before 1863, since the sport was created that year.

In order to tackle that, two steps will be taken: using custom types and implementing the builder pattern.

### Custom types

The first strategy is to create a type. This strategy makes sense when an object has great complexity, such as objects that handle money and dates. Bringing this complexity to an entity can break the single responsibility principle. There is an excellent article written by Martin Fowler, [When to Make a Type](https://martinfowler.com/ieeeSoftware/whenType.pdf), which explains such features’ advantages. We don't want to reinvent the wheel either, so in order to represent year and money, we will use the Date/Time APIs born out of Java 8 and the Money API. The only type we will need to create is the `email` type, as shown in the following code:

```java
import java.util.Objects;
import java.util.function.Supplier;
import java.util.regex.Pattern;

public final class Email implements Supplier<String> {

    private static final String EMAIL_PATTERN =
            "^[_A-Za-z0-9-\\+]+(\\.[_A-Za-z0-9-]+)*@"
                    + "[A-Za-z0-9-]+(\\.[A-Za-z0-9]+)*(\\.[A-Za-z]{2,})$";

    private static final Pattern PATTERN = Pattern.compile(EMAIL_PATTERN);

    private final String value;

    @Override
    public String get() {
        return value;
    }

    private Email(String value) {
        this.value = value;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }
        Email email = (Email) o;
        return Objects.equals(value, email.value);
    }

    @Override
    public int hashCode() {
        return Objects.hashCode(value);
    }

    @Override
    public String toString() {
        return value;
    }

    public static Email of(String value) {
        Objects.requireNonNull(value, "o valor é obrigatório");
        if (!PATTERN.matcher(value).matches()) {
            throw new IllegalArgumentException("Email nao válido");
        }

        return new Email(value);
    }
}

```

We will now use these types in the `Player` class. It will look like this:

```java
import javax.money.MonetaryAmount;
import java.time.Year;
import java.util.Objects;
import java.util.Optional;

public class Player {

    private String id;

    private String name;

    private Year start;

    private Year end;

    private Email email;

    private Position position;

    private MonetaryAmount salary;

//...

}

```

### Builder Pattern 

The second and last step will be to use the Builder pattern to validate the rules within this class. By adopting this pattern, we will guarantee the object to be instantiated only when the data is valid, besides placing the responsibility for this creation on a class. This pattern is very interesting because, in addition to guaranteeing single responsibility, it decreases the chance of one of the parameters being accidentally changed.

An important detail is that many of the mapping frameworks, such as Hibernate, OpenJPA, etc., require getters and setters, in addition to the default constructor. One solution would be to create a constructor with all the necessary parameters in private and another one as default and with the Deprecated annotation, making it clear to the developer that that constructor is not well-regarded for use. See in the example below that the builder is the inner class of the `Player` class:

```java
import javax.money.MonetaryAmount;
import java.time.Year;
import java.util.Objects;
import java.util.Optional;

public class Player {

    static final Year SOCCER_BORN = Year.of(1863);

    //hide

    private Player(String name, Year start, Year end, Email email, Position position, MonetaryAmount salary) {
        this.name = name;
        this.start = start;
        this.end = end;
        this.email = email;
        this.position = position;
        this.salary = salary;
    }

    @Deprecated
    Player() {
    }

    public static PlayerBuilder builder() {
        return new PlayerBuilder();
    }

    public static class PlayerBuilder {

        private String name;

        private Year start;

        private Year end;

        private Email email;

        private Position position;

        private MonetaryAmount salary;

        private PlayerBuilder() {
        }

        public PlayerBuilder withName(String name) {
            this.name = Objects.requireNonNull(name, "name is required");
            return this;
        }

        public PlayerBuilder withStart(Year start) {
            Objects.requireNonNull(start, "start is required");
            if (Year.now().isBefore(start)) {
                throw new IllegalArgumentException("you cannot start in the future");
            }
            if (SOCCER_BORN.isAfter(start)) {
                throw new IllegalArgumentException("Soccer was not born on this time");
            }
            this.start = start;
            return this;
        }

        public PlayerBuilder withEnd(Year end) {
            Objects.requireNonNull(end, "end is required");

            if (start != null && start.isAfter(end)) {
                throw new IllegalArgumentException("the last year of a player must be equal or higher than the start.");
            }

            if (SOCCER_BORN.isAfter(end)) {
                throw new IllegalArgumentException("Soccer was not born on this time");
            }
            this.end = end;
            return this;
        }

        public PlayerBuilder withEmail(Email email) {
            this.email = Objects.requireNonNull(email, "email is required");
            return this;
        }

        public PlayerBuilder withPosition(Position position) {
            this.position = Objects.requireNonNull(position, "position is required");
            return this;
        }

        public PlayerBuilder withSalary(MonetaryAmount salary) {
            Objects.requireNonNull(salary, "salary is required");
            if (salary.isNegativeOrZero()) {
                throw new IllegalArgumentException("A player needs to earn money to play; otherwise, it is illegal.");
            }
            this.salary = salary;
            return this;
        }

        public Player build() {
            Objects.requireNonNull(name, "name is required");
            Objects.requireNonNull(start, "start is required");
            Objects.requireNonNull(email, "email is required");
            Objects.requireNonNull(position, "position is required");
            Objects.requireNonNull(salary, "salary is required");

            return new Player(name, start, end, email, position, salary);
        }

    }
}

```

That way, we will be sure that when a player instance (`Player`) is created, it will be consistent and fail-safe.

```java
     CurrencyUnit usd = Monetary.getCurrency(Locale.US);
     MonetaryAmount salary = Money.of(1 _000_000, usd);
     Email email = Email.of("marta@marta.com");
     Year start = Year.now();
     Year end = start.plus(-1, ChronoUnit.YEARS);

     Player marta = Player.builder().withName("Marta")
         .withEmail(email)
         .withSalary(salary)
         .withStart(start)
         .withPosition(Position.FORWARD)
         .build();

```

We can apply this same principle when creating a team (`Team`):

```java
  Team bahia = Team.of("Bahia");
  Player marta = Player.builder().withName("Marta")
      .withEmail(email)
      .withSalary(salary)
      .withStart(start)
      .withPosition(Position.FORWARD)
      .build();

  bahia.add(marta);

```

### Using Bean Validation

Another way to guarantee data validation is by using resources from [Bean Validation](https://beanvalidation.org/). Bean Validation is a Java specification whose objective is to ensure that the attributes within the class are valid through the exclusive use of annotations, i.e., in a simple and reusable way.

It is important to emphasize that all the rules mentioned above are still valid using this API and that all good object-oriented practices, such as encapsulation, are maintained. Thus, the use of Bean Validation can be seen as a double-check; or for the builder to run the validations from the framework so that it only returns the instance if all validations are successful.

```java
import javax.money.MonetaryAmount;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.PastOrPresent;
import javax.validation.constraints.PositiveOrZero;
import java.time.Year;
import java.util.Objects;
import java.util.Optional;

public class Player {

    static final Year SOCCER_BORN = Year.of(1863);

    @NotBlank
    private String name;

    @NotNull
    @PastOrPresent
    private Year start;

    @PastOrPresent
    private Year end;

    @NotNull
    private Email email;

    @NotNull
    private Position position;

    @NotNull
    private MonetaryAmount salary;

    @PositiveOrZero
    private int goal = 0;

    //continue

}

import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Objects;

public class Team {

    static final int SIZE = 20;

    @NotBlank
    private String name;

    @NotNull
    @Size(max = SIZE)
    private List<Player> players = new ArrayList<>();

    //continue

}

```

The source code for this example is available in the repository:
[https://github.com/soujava/bulletproof](https://github.com/soujava/bulletproof).

TIP: Remember the importance of unit testing throughout the development process!

This example demonstrates that only by using object-oriented concepts will you create fail-safe code. So far, all practices are database-agnostic, i.e., we can use these good practices regardless of the persistence technology that will be adopted.

## Lombok: problem or solution?

In general, the Lombok project is a library famous for reducing the number of lines of code through the use of annotations. This tool has its benefits since code reduction can facilitate reading. An example is the `Builder` annotation, which allows the creation of more intuitive classes and in the Builder pattern. On the other hand, we also see some problems in the use of this project. Items that are listed below:

* The code is generated by the Lombok project and is not seen by the IDE. Any exception involving these classes tend to be difficult to find in the stack trace;

* There are annotations like `@Data` that allow the class encapsulation to be broken. By doing that, you will stop programming using the object-oriented programming paradigm. It is important to highlight the information provided in chapter 7 of the Clean Code book: the biggest difference between OOP and structured programming is that in the former, we hide the data to expose the behavior;

* Much of the code such as getters and setters can be generated by the IDE;

* The powers of annotations are very tempting; however, encapsulation is not about having the private attributes and public getters and setters but ensuring that attributes are accessed with the least possible visibility.

We want to clarify that the purpose of this topic is not to classify Lombok and its relationship regarding good programming practices. The intention is to show that, despite having its advantages - shown on several websites -, it is vital to bear in mind the problems caused by adopting this technology.

* attribute, class after
* traduzir comentários nos códigos
* builder or Builder
