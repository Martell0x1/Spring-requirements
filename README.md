<p align="center">
  <img src="https://img.shields.io/badge/SOLID-Studying%20Repo-blue?style=for-the-badge&logo=read-the-docs&logoColor=white" alt="SOLID Study Badge" />
</p>
<h1 align="center">
  📚 SOLID And More
</h1>
---

This repository covers the essential prerequisites for learning Spring, focusing on key concepts such as **SOLID principles**, **Maven**, and **Apache Tomcat**.

---

## 🔗 Quick Access
- [SOLID Principles](#solid-principles)
- [Maven](#maven)
- [Apache Tomcat](#apache-tomcat)

---

# Maven

Maven is mostly used for the java projects to build web application packages. Maven provides different features which make it easy to build the web-application packages we manage complex projects easily.

1. ****POM Files:**** Project Object Model(POM) Files are XML file that contains information related to the project and configuration information such as dependencies, source directory, plugin, goals etc. used by Maven to build the project. When you should execute a maven command you give maven a POM file to execute the commands. Maven reads pom.xml file to accomplish its configuration and operations.
2. ****Dependencies and Repositories:**** Dependencies are external Java libraries required for Project and repositories are directories of packaged JAR files. The local repository is just a directory on your machine’s hard drive. If the dependencies are not found in the local Maven repository, Maven downloads them from a central Maven repository and puts them in your local repository.
3. ****Build Life Cycles, Phases, and Goals:**** A build life cycle consists of a sequence of build phases, and each build phase consists of a sequence of goals. Maven command is the name of a build lifecycle, phase, or goal. If a lifecycle is requested executed by giving the maven command, all build phases in that life cycle are executed also. If a build phase is requested executed, all build phases before it in the defined sequence are executed too.
4. ****Build Profiles:**** Build profiles a set of configuration values that allows you to build your project using different configurations. For example, you may need to build your project for your local computer, for development and test. To enable different builds you can add different build profiles to your POM files using its profiles elements which are triggered in a variety of ways.
5. ****Build Plugins:**** Build plugins are used to perform a specific goal. you can add a plugin to the POM file. Maven has some standard plugins you can use, and you can also implement your own in Java.
---
# Tomcat
**Apache Tomcat** is a **web server and servlet container**. Think of it as a program that runs **Java web applications**.
- Receives **HTTP requests** (like from a browser or Postman)
    
- Runs your **Java web code** (like servlets or Spring controllers)
    
- Sends back **HTTP responses** (like HTML or JSON)

- It **implements the Java Servlet and JSP specifications**.
    
- That means it knows how to run `.jsp` files and Java servlets.
    
- In Spring Boot, it usually runs **embedded inside your app**—you don't need to install it separately.
---
# SOLID
### Single Responsibility
**Theory:** any software entity must have only one responsibility and have one reason to change

**Examples:**
```java
public class UserManager {
    public void createUser(String username) {
        // Logic to create user
        System.out.println("User created: " + username);
    }

    public void saveToDatabase(String username) {
        // Logic to save user to DB
        System.out.println("Saved " + username + " to database");
    }

    public void sendEmail(String username) {
        // Logic to send email
        System.out.println("Email sent to " + username);
    }
}```
- the previous class has many responsibilities (createUser , saveTodb , sendEmail)
``` java
public class UserCreator {
    public void createUser(String username) {
        System.out.println("User created: " + username);
    }
}

public class UserRepository {
    public void save(String username) {
        System.out.println("Saved " + username + " to database");
    }
}

public class EmailService {
    public void sendWelcomeEmail(String username) {
        System.out.println("Email sent to " + username);
    }
}

```
- we can solve this by simply split each responsibilty to each class
- and then we can use it
``` java
public class UserService {
    private final UserCreator creator = new UserCreator();
    private final UserRepository repository = new UserRepository();
    private final EmailService emailService = new EmailService();

    public void registerUser(String username) {
        creator.createUser(username);
        repository.save(username);
        emailService.sendWelcomeEmail(username);
    }
}

```
- and the RegusterUser here **doesn't violate** the SRP
- The method is part of a **higher-level coordination class**, often called a **"Service"** in layered architecture. Its **single responsibility** is to **orchestrate the user registration process**.

- It **does not implement** the logic of user creation, saving, or emailing.
- It simply **coordinates** already-separated responsibilities.

### Open-Close
- **Theory:** any software entity should be open for extention , but closed for modification

- **Examples:**
```java
public class DiscountCalculator {
    public double calculateDiscount(String customerType, double amount) {
        if (customerType.equals("Regular")) {
            return amount * 0.1;
        } else if (customerType.equals("Premium")) {
            return amount * 0.2;
        } else if (customerType.equals("VIP")) {
            return amount * 0.3;
        }
        return 0;
    }
}
```
- Every time we introduce a new customer type, we have to **modify this class**.
- This violates the **Open-Closed Principle**.
- **Solution:**
``` java
public interface DiscountStrategy {
    double calculate(double amount);
}

public class RegularDiscount implements DiscountStrategy {
    public double calculate(double amount) {
        return amount * 0.1;
    }
}

public class PremiumDiscount implements DiscountStrategy {
    public double calculate(double amount) {
        return amount * 0.2;
    }
}

public class VIPDiscount implements DiscountStrategy {
    public double calculate(double amount) {
        return amount * 0.3;
    }
}

```
- when we use it
``` java
public class DiscountCalculator {
    private DiscountStrategy strategy;

    public DiscountCalculator(DiscountStrategy strategy) {
        this.strategy = strategy;
    }

    public double calculate(double amount) {
        return strategy.calculate(amount);
    }
}
```
- this will keep the class closed for modification (when a new discount strategy is added) , but open for extention(the class is extended to handle more dicount strategies).
### Liskov Substitution
- **Theory:** if A is a subtype of B, then objects of type B in a program may be replaced with objects of type s **Without alternating  any of desirable properties of the program**
- in another words we hope to achieve the following code without case the program to crash
``` java
B obj = new B()
B obj = new A()
```
- **Examples**
```java
public class Bird {
    public void fly() {
        System.out.println("Flying...");
    }
}

public class Ostrich extends Bird {
    @Override
    public void fly() {
        throw new UnsupportedOperationException("Ostriches can't fly!");
    }
}

// main class
public void makeBirdFly(Bird bird) {
    bird.fly();  // May crash for Ostrich!
}
```
- `Ostrich` is a `Bird`, but it **can't fly**.
- If we replace a `Bird` with an `Ostrich`, the code could crash or behave unexpectedly.

- fix:
- **Step one , Separate Capabilities**
``` java
public abstract class Bird {
    public abstract void eat();
}

public interface Flyable {
    void fly();
}

```
- **Step tow ,  Implement properly**
``` java
public class Sparrow extends Bird implements Flyable {
    @Override
    public void eat() {
        System.out.println("Sparrow eats seeds.");
    }

    @Override
    public void fly() {
        System.out.println("Sparrow flying...");
    }
}

public class Ostrich extends Bird {
    @Override
    public void eat() {
        System.out.println("Ostrich eats plants.");
    }
}
```
- and that's it when we use our class like this
``` java
public void makeFly(Flyable bird) {
    bird.fly();  // Only called on birds that can actually fly
}
```
- this is called only for birds that can fly.

### Interface Segregation
- **Theory:** Split interfaces that are very large into smaller and more specific ones , so that client (a class / external api / ..etc) will only have t know about the methods that are of interest to them.
- **Examples:** 
```java
public interface Worker {
    void work();
    void eat();
}```

``` java
public class HumanWorker implements Worker {
    public void work() {
        System.out.println("Human working...");
    }

    public void eat() {
        System.out.println("Human eating...");
    }
}

public class RobotWorker implements Worker {
    public void work() {
        System.out.println("Robot working...");
    }

    public void eat() {
        // Robots don’t eat!
        throw new UnsupportedOperationException("Robot can't eat!");
    }
}

```
- `RobotWorker` is **forced** to implement a method (`eat()`) it doesn't need.
- This breaks ISP by **coupling unrelated behaviors** in a single interface
- solution:
``` java
public interface Workable {
    void work();
}

public interface Eatable {
    void eat();
}

```
``` java
public class HumanWorker implements Workable, Eatable {
    public void work() {
        System.out.println("Human working...");
    }

    public void eat() {
        System.out.println("Human eating...");
    }
}

public class RobotWorker implements Workable {
    public void work() {
        System.out.println("Robot working...");
    }
}

```
### Dependency Inversion
- **Theory:** High-level modules should not depend on low-level modules , both should depend on abstraction , abstraction should not depend on details (concrete implementation) , details should depend on abstraction
- **High-level modules**: Contain business logic (e.g., `OrderService`)(have less details)
- **Low-level modules**: Perform operations (e.g., `MySQLDatabase`, `EmailSender`) (have more details)
- **Abstractions**: Interfaces or abstract classes


- **Examples**: 
```java
public class MySQLDatabase {
    public void save(String data) {
        System.out.println("Saving to MySQL: " + data);
    }
}

public class OrderService {
    private MySQLDatabase database = new MySQLDatabase();

    public void processOrder(String order) {
        // Business logic...
        database.save(order);  // Direct dependency on a concrete class
    }
}
```
- `OrderService` is tightly coupled to `MySQLDatabase`.
- Can't easily switch to PostgreSQL, MongoDB, or mock in tests.

- solution
- **Step One Create Abstraction**
```java
public interface Database {
    void save(String data);
}

```
- **Step tow: Implement the interface**
```java
public class MySQLDatabase implements Database {
    public void save(String data) {
        System.out.println("Saving to MySQL: " + data);
    }
}
```
- **Step three: Depend on the abstraction in high-level module**
```java
public class OrderService {
    private final Database database;

    public OrderService(Database database) {
        this.database = database;
    }

    public void processOrder(String order) {
        // Business logic...
        database.save(order);
    }
}
```
- usage:
```java
public class Main {
    public static void main(String[] args) {
        Database db = new MySQLDatabase();  // You can easily switch this
        OrderService service = new OrderService(db);
        service.processOrder("Order #123");
    }
}

```
---
# SOLI D Practice
- i give you example and you think of all possible solutions for it
```java
package com.codurance.srp;

import java.text.DecimalFormat;
import java.text.DecimalFormatSymbols;
import java.text.MessageFormat;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.util.LinkedList;
import java.util.List;
import java.util.Locale;
import java.util.concurrent.atomic.AtomicInteger;

import static java.util.stream.Collectors.toCollection;

public class AccountService {

    private static final String STATEMENT_HEADER = "DATE | AMOUNT | BALANCE";
    private static final String DATE_FORMAT = "dd/MM/yyyy";
    private static final String AMOUNT_FORMAT = "#.00";

    private TransactionRepository transactionRepository;
    private Clock clock;
    private Console console;

    public AccountService(TransactionRepository transactionRepository, Clock clock, Console console) {
        this.transactionRepository = transactionRepository;
        this.clock = clock;
        this.console = console;
    }

    public void deposit(int amount) {
        transactionRepository.add(transactionWith(amount));
    }


    public void withdraw(int amount) {
        transactionRepository.add(transactionWith(-amount));
    }

    public void printStatement() {
        printHeader();
        printTransactions();
    }


    private void printHeader() {
        printLine(STATEMENT_HEADER);
    }


    private void printTransactions() {
        List<Transaction> transactions = transactionRepository.all();
        final AtomicInteger balance = new AtomicInteger(0);
        transactions.stream()
                .map(transaction -> statementLine(transaction, balance.addAndGet(transaction.amount())))
                .collect(toCollection(LinkedList::new))
                .descendingIterator()
                .forEachRemaining(this::printLine);
    }


    private Transaction transactionWith(int amount) {
        return new Transaction(clock.today(), amount);
    }


    private String statementLine(Transaction transaction, int balance) {
        return MessageFormat.format("{0} | {1} | {2}", formatDate(transaction.date()), formatNumber(transaction.amount()), formatNumber(balance));
    }

    private String formatDate(LocalDate date) {
        DateTimeFormatter dateFormatter = DateTimeFormatter.ofPattern(DATE_FORMAT);
        return dateFormatter.format(date);
    }

    private String formatNumber(int amount) {
        DecimalFormat decimalFormat = new DecimalFormat(AMOUNT_FORMAT, DecimalFormatSymbols.getInstance(Locale.UK));
        return decimalFormat.format(amount);
    }


    private void printLine(String line) {
        console.printLine(line);
    }
}
```
- the class handles 3 different resposibilities 
-  **Business logic**: `deposit()`, `withdraw()`, and transaction creation.
- **Data formatting**: Formatting dates and amounts.
- **UI output**: Printing the statement to the console.

- so it violates SRP , to resolve the problem split the printing logic into another high-level component
- an that's it , what about Data-formatting?
- well , when dealing with SRP we usually follow **pragmatic SRP approach-fix the _clearest violation first_, and only split deeper when needed.**
- the data formatting logic is small to considered a separated high-level component (if the logic gets more complex it must be splited) , but in this example we will split it with printing logic.

- another thing this class has a DIP as it relies  on low-level implementations (console , clock (they are concerete classes))
- resolve this by abstracting the low-level components  and force the high-level one to rely on the absraction.
---

# 🔗 SOLID Practice Repositories on GitHub

- [jahs-es/solid-kata](https://github.com/jahs-es/solid-kata)
- [mikeknep/SOLID](https://github.com/mikeknep/SOLID)
- [hdeiner/SOLID](https://github.com/hdeiner/SOLID)
- [nahidulhasan/solid-principles](https://github.com/nahidulhasan/solid-principles)
- [heykarimoff/solid.python](https://github.com/heykarimoff/solid.python)
- [lucafilippodangelo/OOP-SOLID-Five-Principle](https://github.com/lucafilippodangelo/OOP-SOLID-Five-Principle)
- [harrymt/SOLID-principles](https://github.com/harrymt/SOLID-principles)
- [muratkarakas/solid-principles](https://github.com/muratkarakas/solid-principles)
- [chethanmandya/SOLID-Principles](https://github.com/chethanmandya/SOLID-Principles)
- [INSATunisia/SOLID](https://github.com/INSATunisia/SOLID)
