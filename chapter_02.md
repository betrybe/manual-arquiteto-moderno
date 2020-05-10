# Clean code





Não existe nenhuma dúvida sobre os benefícios das boas práticas, por exemplo, a legibilidade de código e a facilidade de manutenção a partir de um código limpo e fluido. Porém, será que as boas práticas de código também ajudam na integridade dos dados? Um dos primordiais tópicos cobertos pelo livro [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship-ebook/dp/B001GSTOAM) é que a orientação a objetos expõe comportamento escondendo os dados, que difere, de uma programação estruturada. O objetivo desse artigo é falar sobre os benefícios das boas práticas de código e as vantagens de um modelo rico.



Como já mencionando, um bom código tem garantia também na parte de performance para uma aplicação, não sendo necessário enviar e validar os dados no banco, economizando tanto rede quanto processamento. Porém, o grande desafio em blindar o código são os inúmeros conceitos técnicos que são aplicados, sem falar na junção de negócio com o interesse na criação de uma linguagem ubíqua. Para facilitar tal entendimento, será criado um simples exemplo de gestão de jogadores de futebol, dentro de um time e terá as seguintes informações e regras:



Nome do jogador representado pelo atributo <code>name</code> da classe <code>Player</code>, posição (position da classe Player) que o jogador pertence (Goleiro, ataque, defesa e meio de campo), ano que o jogador entrou no time representado pelo atributo <code>start</code> da classe <code>Player</code>, ano que o jogador saiu do time representado pelo atributo <code>end</code> da classe <code>Player</code>, o número de gols que o jogador realizou no time representado pelo atributo <code>goals</code> da classe <code>Player</code>, o salário do jogador representado pelo atributo <code>salary</code> da classe <code>Player</code>, email para contato, relação com o time e um time não ter mais que 20 membros.	



Com base nas informações citadas, a primeira versão do modelo é mostrado a seguir:



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



À primeira vista o código pode ser melhorado, uma vez que as posições são fixas, permitir que o atributo <code>position</code> seja do tipo String não faz muito sentido. Para resolver esse problema, será utilizado o conceito de [Value Objects](https://martinfowler.com/bliki/ValueObject.html) com um Enum.



```java
public enum Position {
    GOALKEEPER, DEFENDER, MIDFIELDER, FORWARD;
}

```



Uma vez definido o modelo inicial, o próximo passo é falar sobre segurança e encapsulamento dos objetos. Essa é umas das bases para um bom código orientado a objetos: a possibilidade de esconder os dados para expor o comportamento.A criação dos métodos assessores devem ser considerados como último recurso para acesso ao objeto, um outro ponto, eles não necessariamente precisam ser públicos, ou seja, podem ser criados como default ou protected caso não seja necessário a criação de um método público. Com base nisso, é possível entrar nos detalhes das regras de negócio:No sistema os jogadores não mudam de e-mail, nome e de posição, e só é possível marcar um gol por vez, de modo que os setters também não são importantes para esses atributos;O ano de saída pode estar vazio, porém, quando preenchido deverá ser posterior a data de entrada;Apenas o time (representado pela classe Team) é responsável por gerenciar os jogadores, ou seja, é necessário criar métodos para adicionar um jogador dentro do time. 



Uma vez definido o modelo inicial, o próximo passo é falar sobre segurança e encapsulamento dos objetos. Essa é umas das bases para um bom código orientado a objetos: a possibilidade de esconder os dados para expor o comportamento.

A criação dos métodos assessores devem ser considerados como último recurso para acesso ao objeto, um outro ponto, eles não necessariamente precisam ser públicos, ou seja, podem ser criados como default ou protected caso não seja necessário a criação de um método público. Com base nisso, é possível entrar nos detalhes das regras de negócio:

* No sistema os jogadores não mudam de e-mail, nome e de posição, e só é possível marcar um gol por vez, de modo que os setters também não são importantes para esses atributos;
* O ano de saída pode estar vazio, porém, quando preenchido deverá ser posterior a data de entrada;
* Apenas o time (representado pela classe Team) é responsável por gerenciar os jogadores, ou seja, é necessário criar métodos para adicionar um jogador dentro do time.



Começando pelo Team, será necessário a criação de um método para adicionar players no Team e um getter para retornar os players, é importante validar a entrada de players uma vez que não faz sentido adicionar um player nulo e garantir que apenas o Team adicione/remove players fazendo com que essa classe retorne uma lista de apenas leitura, do contrário, teremos problemas com o encapsulamento. Uma maneira de resolver isso seria retornar uma lista como no exemplo a seguir:



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





Muitos frameworks precisam que o construtor padrão exista por questão de realizar a criação de uma instância a partir da API de reflection. Como o objetivo é desencorajar o uso do construtor padrão ao invés do uso método de construção, o construtor será anotado com [Deprecated](https://www.baeldung.com/java-deprecated). A anotação Deprecated indica que esse método não deve ser utilizado.

Com relação a classe Player, todos atributos terão getters padrão, com exceção do atributo <code>end</code> que terá um tratamento especial; o <code>getEnd</code> retornará um Optional, uma vez que o <code>end</code> pode ser nulo. Outro ponto é o método <code>setEnd</code>, ele só é integro uma vez que o último ano seja igual ou maior que o ano de início do player, ou seja, se ele começou a jogar em 2004 não faz sentido ele ter terminado de jogar em 2002, de modo que o setter terá que fazer a validação no momento do acesso.



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



Uma vez definido os métodos de acesso, o próximo passo está na criação das instâncias de Team e Player. Como boa parte das informações são obrigatórias para se criar uma instância válida, o primeiro movimento natural seria a criação de um método construtor. Isso é válido com objetos simples como o Team, porém, a class Player tem mais complexidades como:

1.  O primeiro deles é a quantidade de parâmetros uma vez que seria um construtor [polyadic](https://medium.com/coding-skills/clean-code-101-meaningful-names-and-functions-bf450456d90c) (construtor com mais de três argumentos) .
2.  O segundo deles está na complexidade das validações, por exemplo, não faz sentido um player começar a jogar antes de 1863, uma vez que o esporte nasceu esse ano.



Para resolver esses problemas serão feitos dois passos:

A primeira estratégia é a criação de um tipo, isso faz sentido quando um objeto tem uma grande complexidade como dinheiro e data, trazer essa complexidade para entidade quebraria o princípio da responsabilidade única. Existe um artigo muito bom do [When Make a Type](https://martinfowler.com/ieeeSoftware/whenType.pdf) que explica as vantagens sobre tais recursos. Um outro ponto é que não faz sentido reinventar a roda, assim para representar ano e dinheiro serão utilizadas as API de Date/Time que nasceu do Java 8 e Money-API respectivamente. O único tipo que será criado é o tipo e-mail como mostrar o código a seguir:



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



Como base nesses tipos a classe `Player` terá o seguinte formado:



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



O segundo e último passo seria utilizar o padrão Builder para fazer com que as regras de validação estejam dentro dessa classe, assim o objeto instanciará quando os dados estiveram realmente validados além de colocar a responsabilidade da criação em uma classe. Esse padrão é muito interessante porque além de garantir responsabilidade única diminui a chance de um dos parâmetros serem trocados acidentalmente.	 	 

Um ponto importante é que muitos dos frameworks de mapeamento como o Hibernate, OpenJPA, etc, requerem getters e setters além do construtor padrão. Uma solução para isso, seria criar um construtor com todos os parâmetros necessários em privado e um outro como default e com a anotação Deprecated, deixando claro para o desenvolvedor que aquele construtor não é bem-visto para uso e o Builder ficar como inner class de jogador como demonstrado no código a seguir:



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



Dessa forma, existirá uma certeza de quando a instância de jogador for criada, ela estará consistente e a prova de falhas.



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



Esse mesmo princípio é utilizado com a criação do time:



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



Uma outra maneira de garantir a validação dos dados é utilizando dos recursos muito interessante do [Bean Validation](https://beanvalidation.org/). O Bean Validation é uma especificação Java cujo objetivo é garantir que os atributos dentro da classe sejam válidos, tudo isso utilizando apenas anotação, ou seja, algo de uma maneira bastante simples e reaproveitável. É importante salientar que todas as regras citadas, anteriormente, continuam válidas com o uso dessa API, ou seja, é importante que sejam mantidos todas as boas práticas de orientação a objetos como o encapsulamento. Assim, o uso do Bean Validation pode ser utilizado como uma dupla verificação ou fazer com que o Builder execute as validações oriundas do framework de modo que ele só retorne a instância se todas as validações forem feitas devidamente.



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



Com isso foi demonstrado as vantagens da criação de um código a prova de falhas apenas utilizando os conceitos de orientação a objetos. Um ponto importante é que até esse momento todas práticas funcionam de maneira agnóstica ao banco de dados, ou seja, se pode usar essas boas práticas indiferente da tecnologia de persistência que será utilizada. Uma coisa que não foi mencionada, por motivo de não sair do escopo do artigo, é da importância dos testes de unidade em todo o processo de desenvolvimento. Na segunda parte desse artigo o objetivo será a integração com um banco de dados.



O código fonte está disponível em [https://github.com/soujava/bulletproof](https://github.com/soujava/bulletproof).



## Lombok: problema ou solução?



De uma maneira geral o projeto Lombok é uma biblioteca bastante famosa conhecida por reduzir o número de código através de anotações. Essa ferramenta traz alguns benefícios uma vez que a redução de código pode facilitar a leitura, a anotação “Builder” permite que criar uma classe no padrão Builder com o intuito de criar uma classe de maneira mais intuitiva dentre outras vantagens que  são mostrados no site do projeto. Porém, como existem alguns problemas no uso desse projeto que serão listados a seguir:



* Uma vez que o código será gerado pelo projeto e não será visualizado pela IDE exceção que envolve essas classes tende a ser difícil de ser seguida pela pilha de exceção.

* Existem anotações como `@Data` que é uma maneira de quebrar o encapsulamento das classes. E você deixará de programar em programação orientada a objetos. É importante salientar o que diz no capítulo sete do clean code que deixa claro que a maior diferença entre OOP e estruturado é que na primeira opção nós escondemos os dados para expor o comportamento.

* Boa parte dos códigos como getter e setter são gerados pela IDE.

* O poder das anotações são muito tentadores, porém, encapsulamento não é sobre ter o atributo privado e com getter e setter públicos é sobre garantir que atributos sejam acessado com a menor visibilidade possível.



O objetivo desse tópico não é falar sobre se o Lombok está relacionado ou não a boa prática de programação. Ele está bem mais relacionado a escolha do time. Ele tem diversas vantagens que são mostrados em diversos sites e conteúdos pela internet e não precisamos citar aqui, porém, é importante lembrar que existem sim problemas na adoção dessa tecnologia.

