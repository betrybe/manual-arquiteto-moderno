# Clean code {#chapter_03}

São claros e reconhecidos os grandes benefícios obtidos por meio da utilização de boas práticas no código. Algo que logo se nota ao se trabalhar com um código limpo e fluido é a melhor legibilidade de código e a maior facilidade de manutenção. Mas, ao construir aplicações e discutir práticas de arquitetura, há um outro ponto que não podemos deixar de lado: a integridade dos dados que serão manipulados. Será que as boas práticas de código refletem de forma positiva na integridade desses dados? Um dos tópicos primordiais tópicos cobertos pelo livro [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship-ebook/dp/B001GSTOAM) é que, diferentemente da programação estruturada, a orientação a objetos expõe comportamento escondendo os dados. Com isso em mente, neste capítulo discorreremos sobre os benefícios da utilização de boas práticas de código e as vantagens obtidas ao se implementarem modelos ricos.

## Modelos Ricos

Levando em consideração uma aplicação que adota práticas de clean code, os ganhos de performance são consideráveis, uma vez que, por não trafegar e validar dados em bancos de dados, há economia tanto de processamento de rede quanto de hardware. Durante o desenvolvimento, há vários desafios em se "blindar" o código, como os inúmeros conceitos técnicos que são aplicados e a junção de negócio com o interesse na criação de uma linguagem ubíqua. Que tal aprofundar um pouco mais nesse conceito através da criação de um exemplo? 

Vamos criar uma aplicação de gestão de jogadores de futebol. Teremos o conceito de `time`, e dentro de um `time` teremos as seguintes informações:

- O nome do jogador, representado pelo atributo `name` da classe `Player`;
- A posição (`position` da classe `Player`) do jogador (goleiro, ataque, defesa e meio de campo); 
- O ano em que o jogador entrou no time, representado pelo atributo `start` da classe `Player`; 
- O ano em que o jogador saiu do time, representado pelo atributo `end` da classe `Player`; 
- O número de gols que o jogador realizou no time, representado pelo atributo `goals` da classe `Player`;
- O salário do jogador, representado pelo atributo `salary` da classe `Player`;
- O email para contato, no atributo `email`;
- A relação com o time, representada pela classe `Team`;	
- Lembrando a regra, _um time não deve ter mais que vinte membros_.

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

Uma vez que definimos o modelo inicial, nosso próximo passo é analisar segurança e encapsulamento dos objetos. Esta é umas das bases para um bom código orientado a objetos: a possibilidade de "esconder" os dados e expor apenas os comportamentos. A criação dos métodos assessores (setter) deve ser considerado como último recurso para acesso ao objeto. Um outro ponto é: deve-se avaliar a necessidade de esses métodos serem públicos, ou seja, considere criá-los como `default` ou `protected`, caso seja possível. 

Sob essa perspectiva, vamos analisar algumas regras de negócio da nossa aplicação de exemplo: 

- Os jogadores não mudam de e-mail, nome e de posição, e só é possível marcar um gol por vez. Repare que a criação de métodos setters não é importante para esses atributos. 
- O ano de saída pode estar vazio, porém, quando preenchido, deverá ser posterior à data de entrada; 
- Apenas o time (representado pela classe `Team`) é responsável por gerenciar os jogadores (`Player`), ou seja, será necessário criar métodos para adicionar jogadores ao time. 

Vamos trabalhar na classe `Team`. Teremos de criar um método para adicionar vários players (`Player`) no `Team`. Também precisaremos de um método (get) que retorne os players. É importante validar a entrada de players, uma vez que não faz sentido adicionar um player nulo. Devemos também garantir que apenas a classe `Team` adicione/remova players. Para isso, devemos assegurar que essa classe retorne uma lista apenas de leitura, do contrário teremos problemas com encapsulamento. Uma maneira de resolver isso seria retornar uma lista como no exemplo a seguir:

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

> **INFO**: Muitos frameworks precisam que o construtor padrão exista por questão de realizar a criação de uma instância a partir da API de Reflection. Como o objetivo é desencorajar o uso do construtor padrão ao invés do uso do método de construção, o construtor será anotado com [Deprecated](https://www.baeldung.com/java-deprecated). A anotação Deprecated indica que esse método não deve ser utilizado.

Com relação à classe `Player`, todos os atributos terão getters padrão, com exceção do atributo `end`, que terá um tratamento especial: o `getEnd` retornará um `Optional`, uma vez que o `end`pode ser nulo. Outro ponto é o método `setEnd`, que só será íntegro caso o último ano seja igual ou maior que o ano de início do player, ou seja, se ele começou a jogar em 2004, não faz sentido ele ter terminado de jogar em 2002. Desse modo, o setter terá de fazer a validação no momento do acesso.

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

Uma vez definidos os métodos de acesso, o próximo passo está na criação das instâncias de `Team` e `Player`. Como boa parte das informações é obrigatória para se criar uma instância válida, o primeiro movimento natural seria a criação de um método construtor. Isso é válido com objetos simples, como o `Team`, porém a class `Player` tem mais complexidades, como:

- A quantidade de parâmetros: pode gerar um construtor [polyadic](https://medium.com/coding-skills/clean-code-101-meaningful-names-and-functions-bf450456d90c) (construtor com mais de três argumentos).
- A complexidade das validações: não faz sentido um player começar a jogar antes de 1863, uma vez que o esporte nasceu nesse ano.

Para resolver esses problemas, executaremos dois passos: utilização de tipos customizados e aplicação do padrão Builder.

### Tipos customizados

A primeira estratégia é a criação de um tipo. Essa estratégia faz sentido quando um objeto tem grande complexidade, como por exemplo objetos que lidam com dinheiro e data. Trazer essa complexidade para a entidade pode quebrar o princípio da responsabilidade única. Existe um artigo muito bom escrito por Martin Fowler, [When to Make a Type](https://martinfowler.com/ieeeSoftware/whenType.pdf), que explica as vantagens de tais recursos. Também não queremos reinventar a roda, portanto, para representar ano e dinheiro utilizaremos as APIs de Date/Time que nasceram do Java 8 e a Money-API. O único tipo que precisaremos criar é o tipo `e-mail`, como mostra o código a seguir:

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

O segundo e último passo será utilizar o padrão Builder para fazer com que as regras de validação estejam dentro dessa classe. Ao adotar esse padrão, garantiremos que o objeto só será instanciado quando os dados forem realmente válidos, além de colocar a responsabilidade dessa criação em uma classe. Esse padrão é muito interessante porque, além de garantir a responsabilidade única, diminui a chance de um dos parâmetros ser trocado acidentalmente.	 	 

Um ponto importante é que muitos dos frameworks de mapeamento, como o Hibernate, OpenJPA etc., requerem getters e setters, além do construtor padrão. Uma solução para isso seria criar um construtor com todos os parâmetros necessários em privado e um outro como default e com a anotação Deprecated, deixando claro para a pessoa desenvolvedora que aquele construtor não é bem-visto para uso. Veja no exemplo abaixo que o Builder fica como inner class da classe `Player`:

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

{pagebreak}

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

> **TIP**: Lembre-se da importância dos testes de unidade em todo o processo de desenvolvimento!

Com a criação desse exemplo, demonstramos que apenas ao utilizar conceitos de orientação a objetos você estará criando um código à prova de falhas. Até esse momento, todas as práticas funcionam de maneira agnóstica ao banco de dados, ou seja, podemos utilizar essas boas práticas independentemente da tecnologia de persistência que será adotada.  

## Lombok: problema ou solução?

De uma maneira geral, o projeto Lombok é uma biblioteca famosa e conhecida por reduzir a quantidade de linhas de código através da utilização de anotações. Essa ferramenta tem seus benefícios, uma vez que a redução de código pode facilitar a leitura. Um exemplo é a anotação `Builder`, que permite a criação de classes mais intuitivas e no padrão Builder. Por outro lado, também vemos alguns problemas no uso desse projeto, itens que estão listados a seguir:

* O código é gerado pelo projeto Lombok e não é visualizado pela IDE. Qualquer exceção que envolva essas classes tenderá a ser difícil de ser seguida pela pilha de exceção;

* Existem anotações como `@Data` que permitem a quebra do encapsulamento das classes. Com isso, você deixará de programar utilizando o paradigma de programação orientada a objetos. É importante salientar as informações trazidas no capítulo 7 do livro Clean Code: a maior diferença entre OOP e programação estruturada é que, na primeira opção, nós escondemos os dados para expor o comportamento;

* Boa parte dos códigos como getters e setters podem ser gerados pela IDE;

* Os poderes das anotações são muito tentadores, porém encapsulamento não é sobre ter o atributo privado e com getter e setter públicos, mas sim sobre garantir que atributos sejam acessados com a menor visibilidade possível.

Queremos deixar claro que o objetivo deste tópico não é classificar o Lombok e seu relacionamento ou não com as boas práticas de programação. A intenção é mostrar que, apesar de possuir suas vantagens - expostas em diversos sites -, é importante ter em mente os problemas acarretados com a adoção dessa tecnologia.

