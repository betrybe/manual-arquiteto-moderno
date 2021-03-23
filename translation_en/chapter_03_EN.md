# Clean code

The great benefits obtained through the use of good coding practices are clear and recognized. Something you can quickly notice when working with clean and fluid code is better code readability and easier maintenance. However, when building applications and discussing architectural practices, there is another point that we can’t ignore: the integrity of the data that will be manipulated. Do good code practices reflect positively on that data integrity? One of the primary topics covered by the book [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship-ebook/dp/B001GSTOAM) is that, unlike structured programming, object orientation exposes behavior by hiding the data. With that in mind, in this chapter, we will discuss the advantages of using good coding practices and the benefits gained from implementing rich models.

## Rich Models

Considering an application that adopts clean code practices, the performance gains are considerable, seeing that, by not running and validating data in databases, there are savings in both network and hardware processing. During development, there are several challenges in "shielding" the code, such as the countless technical concepts that are applied — also, joining business rules with interest in creating a ubiquitous language. How about exploring this concept by making an example?

We are going to create a football player management application. We are going to have the concept of a `team`, and within a `team` the following information:

- The name of the player, represented by the <code>name</code> attribute of the <code>Player</code> class;
- The position (`position` of the `Player` class) of the player (goalkeeper, forward, defender, and midfielder);
- The year the player joined the team, represented by the <code>start</code> attribute of the <code>Player</code> class;
- The year the player left the team, represented by the  <code>end</code> attribute of the <code>Player</code> class;
- The number of goals the player scored on the team, represented by the <code>goals</code> attribute of the <code>Player</code> class;
- The salary of the player, represented by the <code>salary</code> attribute of the <code>Player</code> class;
- The contact email, in the `email` attribute;
- The relationship with the team, represented by the `Team` class;
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

At first glance, we can notice possible code improvements. Note that the `position` attribute is of the type `String`, which does not make sense, since the players’ positions are always the same: goalkeeper, defender, midfielder, and forward. We can improve this scenario by applying the concept of [Value Objects](https://martinfowler.com/bliki/ValueObject.html) through the use of an `Enum`:

```java
public enum Position {
    GOALKEEPER, DEFENDER, MIDFIELDER, FORWARD;
}
```

Once we have defined the initial model, our next step is to analyze the objects’ security and encapsulation. That is one of the bases for good object-oriented coding: the possibility of "hiding" data and exposing only behaviors. The creation of accessor methods (setter) should be considered only as a last resort for accessing the object. Another point is: the need for these methods to be public should be evaluated, i.e., consider creating them as `default` or `protected`, if possible.

From this perspective, let's look at some business rules for our sample application:

- Players do not change their email, name, and position, and it is only possible to score one goal at a time. Note that the creation of setter methods is not essential for these attributes.
- The outgoing year may be empty, but, when filled, it must be after the date of entry;
- Only the team (represented by the `Team` class) is responsible for managing the players (`Player`), i.e., it will be necessary to create methods to add players to the team.

Let’s work in the `Team` class. We will have to create a method to add multiple players (`Player`) to `Team`. We will also need a method (get) that returns players. It is important to validate the players’ entry since it does not make sense to add a null player. We must also ensure that only the `Team` class adds/removes players. To do that, we must ensure this class returns a read-only list; otherwise, we will have encapsulation issues. One solution would be to return a list as in the following example:

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

As far as the `Player` class is concerned, all attributes will have default getters, except for the <code>end</code> attribute, which will have a special treatment: <code>getEnd</code> will return an `Optional`, once <code>end</code> can be null. Another point is the method <code>setEnd</code>, which will only be an integer if the last year is equal to or greater than the player's starting year, i.e., if he started playing in 2004, he could not have finished playing in 2002. Thus, the setter will have to validate at the moment of access.

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
        Objects.requireNonNull(value, "value is mandatory");
        if (!PATTERN.matcher(value).matches()) {
            throw new IllegalArgumentException("invalid email");
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

The second and last step will be to use the builder pattern to validate the rules within this class. By adopting this pattern, we will guarantee the object to be instantiated only when the data is valid, besides placing the responsibility for this creation on a class. This pattern is very interesting because, in addition to guaranteeing single responsibility, it decreases the chance of one of the parameters being accidentally changed.

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

TIP: remember the importance of unit testing throughout the development process!

This example demonstrates that only by using object-oriented concepts will you create fail-safe code. So far, all practices are database-agnostic, i.e., we can use these good practices regardless of the persistence technology that will be adopted.

## Lombok: problem or solution?

In general, the Lombok project is a library famous for reducing the number of lines of code through the use of annotations. This tool has its benefits since code reduction can facilitate reading. An example is the `Builder` annotation, which allows the creation of more intuitive classes and in the builder pattern. On the other hand, we also see some problems in the use of this project. Items that are listed below:

* The code is generated by the Lombok project and is not seen by the IDE. Any exception involving these classes tend to be difficult to find in the stack trace;

* There are annotations like `@Data` that allow the class encapsulation to be broken. By doing that, you will stop programming using the object-oriented programming paradigm. It is important to highlight the information provided in chapter 7 of the Clean Code book: the biggest difference between OOP and structured programming is that in the former, we hide the data to expose the behavior;

* Much of the code such as getters and setters can be generated by the IDE;

* The powers of annotations are very tempting; however, encapsulation is not about having the private attributes and public getters and setters but ensuring that attributes are accessed with the least possible visibility.

We want to clarify that the purpose of this topic is not to classify Lombok and its relationship regarding good programming practices. The intention is to show that, despite having its advantages - shown on several websites -, it is vital to bear in mind the problems caused by adopting this technology.