Принципы SOLID — это пять основных правил объектно-ориентированного проектирования, сформулированных Робертом Мартином. Их цель — создание понятного, гибкого и легко поддерживаемого кода. Акроним включает: Принцип единственной ответственности (S), Открытости/закрытости (O), Подстановки Барбары Лисков (L), Разделения интерфейса (I) и Инверсии зависимостей (D).

Классы — это блоки, из которых строится приложение. Так же, как кирпичи в здании. Плохо написанные классы однажды могут принести проблемы. Чтобы понять, правильно ли написан класс, можно свериться со "стандартами качества". В Java это так называемые принципы SOLID.

SOLID — это акроним, образованный из заглавных букв первых пяти принципов ООП и проектирования. Принципы придумал Роберт Мартин в начале двухтысячных, а аббревиатуру позже ввел в обиход Майкл Фэзерс.

1. Single Responsibility Principle (Принцип единственной ответственности).
2. Open Closed Principle (Принцип открытости/закрытости).
3. Liskov's Substitution Principle (Принцип подстановки Барбары Лисков).
4. Interface Segregation Principle (Принцип разделения интерфейса).
5. Dependency Inversion Principle (Принцип инверсии зависимостей).

Цель принципов — проектировать модули, которые способствуют изменениям, легко понимаемы и могут быть повторно использованы..

## 1. S: Принцип единственной ответственности (Single Responsibility Principle, SRP)

Основная идея: Каждый класс должен иметь только одну ответственность или задачу, и эта задача должна быть полностью инкапсулирована в пределах класса. Проще говоря, у класса должна быть только одна причина для изменения.

Такие классы всегда будет просто изменять, если это понадобится, потому что понятно, за что класс отвечает, а за что — нет. А еще подобный код гораздо проще тестировать.

Простой пример:
Представьте класс, который генерирует отчет и сразу же его печатает. Он нарушает SRP, так как у него две ответственности. Правильное решение — разделить эту логику на два разных класса.

```java
// Правильное решение
class ReportGenerator {
    public void generateReport() {
        // генерация отчета
    }
}

class ReportPrinter {
    public void printReport() {
        // вывод отчета
    }
}
```

Здесь каждый класс выполняет только одну задачу.

Практический пример:
Рассмотрим класс, который обрабатывает заказы. Он сохраняет заказ в базу данных и высылает письмо для подтверждения.

```java
// Неправильное решение
public class OrderProcessor {

    public void process(Order order){
        if (order.isValid() && save(order)) {
            sendConfirmationEmail(order);
        }
    }

    private boolean save(Order order) {
        MySqlConnection connection = new MySqlConnection("database.url");
        // сохраняем заказ в базу данных
        return true;
    }

    private void sendConfirmationEmail(Order order) {
        String name = order.getCustomerName();
        String email = order.getCustomerEmail();
        // Шлем письмо клиенту
    }
}
```

Такой модуль может измениться по трем причинам: изменение логики обработки заказа, способа его сохранения или способа отправки письма. Принцип единственной обязанности подразумевает, что это три разные обязанности и они должны находиться в разных классах.

```java
// Правильное решение
public class MySQLOrderRepository {
    public boolean save(Order order) {
        MySqlConnection connection = new MySqlConnection("database.url");
        // сохраняем заказ в базу данных
        return true;
    }
}

public class ConfirmationEmailSender {
    public void sendConfirmationEmail(Order order) {
        String name = order.getCustomerName();
        String email = order.getCustomerEmail();
        // Шлем письмо клиенту
    }
}

public class OrderProcessor {
    public void process(Order order){
        MySQLOrderRepository repository = new MySQLOrderRepository();
        ConfirmationEmailSender mailSender = new ConfirmationEmailSender();
        if (order.isValid() && repository.save(order)) {
            mailSender.sendConfirmationEmail(order);
        }
    }
}
```

## 2. O: Принцип открытости/закрытости (Open/Closed Principle, OCP)

Основная идея: Программные сущности (классы, модули, функции) должны быть открыты для расширения, но закрыты для модификации. Это означает, что при добавлении новой функциональности не следует изменять существующий код, а лучше расширять его. Новая функциональность внедряется либо через наследование, либо через использование абстрактных интерфейсов.

Простой пример:
Предположим, у нас есть система для рисования фигур. Вместо того чтобы изменять существующий код каждый раз при добавлении новой фигуры, мы можем создать абстрактный класс Shape.

```java
abstract class Shape {
    abstract void draw();
}

class Circle extends Shape {
    @Override
    void draw() {
        // рисование круга
    }
}

class Square extends Shape {
    @Override
    void draw() {
        // рисование квадрата
    }
}
```

Теперь, если нам нужно добавить новый тип фигуры, мы просто создаем новый подкласс Shape, не изменяя существующие классы.

Практический пример:
Вернемся к нашему классу для логирования ошибок. Если нам понадобится записывать логи в другой файл, мы можем изменить название в существующем классе, но тогда все логи будут записываться в новый файл. Как же изменить наш логгер, не изменяя сам класс?

```java
// Решение с помощью наследования
open class FileLogger{
    open fun logError(error: String) {
        val file = File("error.txt")
        file.appendText(text = error)
    }
}

class CustomErrorFileLogger : FileLogger() {
    override fun logError(error: String) {
        val file = File("my_custom_error_file.txt")
        file.appendText(text = error)
    }
}
```

Помечаем класс FileLogger и функцию logError как open, создаем новый класс CustomErrorFileLogger и пишем новую реализацию логирования. В итоге наш класс доступен для расширения функциональности, но закрыт для модификации.

## 3. L: Принцип подстановки Барбары Лисков (Liskov Substitution Principle, LSP)

Основная идея: Объекты должны быть заменяемы экземплярами их подклассов без нарушения корректности программы. Необходимо, чтобы подклассы могли бы служить заменой для своих суперклассов. Цель этого принципа в том, чтобы классы-наследники могли бы использоваться вместо родительских, не нарушая работу программы. Если в коде проверяется тип класса, значит принцип подстановки нарушается.

Простой пример (нарушения принципа):
Класс Penguin является наследником класса Bird, но нарушает принцип, так как не может выполнять операцию fly().

```java
class Bird {
    public void fly() {
        // логика полета
    }
}

class Penguin extends Bird {
    @Override
    public void fly() {
        throw new UnsupportedOperationException("Пингвины не летают!");
    }
}
```

Чтобы это исправить, можно ввести базовый класс, разделяющий летающих и не летающих птиц.

Практический пример (нарушения принципа):

У нас есть класс OrderStockValidator, который проверяет, все ли товары заказа есть на складе, и возвращает true или false. Мы расширяем его классом OrderStockAndPackValidator, который проверяет еще и упаковку.

```java
public class OrderStockAndPackValidator extends OrderStockValidator {
    @Override
    public boolean isValid(Order order) {
        for (Item item : order.getItems()) {
            if ( !item.isInStock() || !item.isPacked() ){
                throw new IllegalStateException(
                     String.format("Order %d is not valid!", order.getId())
                );
            }
        }
        return true;
    }
}
```

Здесь мы нарушили принцип LSP, так как вместо того, чтобы вернуть false, наш метод бросает исключение IllegalStateException. Клиенты данного кода не рассчитывают на такое и ожидают возвращения true или false.

## 4. I: Принцип разделения интерфейса (Interface Segregation Principle, ISP)

Основная идея: Клиенты не должны зависеть от интерфейсов, которые они не используют. Лучше иметь несколько специфичных интерфейсов, чем один универсальный, который заставляет реализовать ненужные методы. Слишком «толстые» интерфейсы необходимо разделять на более мелкие и специфические.

Простой пример (нарушения принципа):
Рассмотрим интерфейс Worker, у которого есть методы work() и eat().

```java
interface Worker {
    void work();
    void eat();
}

class Robot implements Worker {
    @Override
    public void work() {
        // работает
    }

    @Override
    public void eat() {
        // роботу не нужно есть, но метод реализовать обязан
    }
}
```

Это пример нарушения ISP. Лучше разделить интерфейсы на Worker (для работы) и Eater (для еды). 

Практический пример: 
Сделаем наш FileLogger интерфейсом и добавим в него еще одну функцию printLogs().

```java
interface FileLogger{
    fun printLogs()
    fun logError(error: String) {
        val file = File("errors.txt")
        file.appendText(text = error)
    }
}

class CustomErrorFileLogger : FileLogger{
    override fun printLogs() {
        // пустая реализация, так как она не нужна
    }
    override fun logError(error: String) {
        val file = File("my_custom_error_file.txt")
        file.appendText(text = error)
    }
}
```

Теперь все наследники будут обязаны реализовать функцию printLogs, даже если она им не нужна, что приводит к появлению пустых функций. Вместо этого можно сделать дефолтную реализацию в интерфейсе, и тогда переопределять функцию нужно будет только в тех классах, где это необходимо.

```java
// Правильное решение
interface FileLogger{
    fun printLogs() {
        //какая-то дефолтная реализация
    }
    fun logError(error: String) {
        val file = File("errors.txt")
        file.appendText(text = error)
    }
}

class CustomErrorFileLogger : FileLogger{
    override fun logError(error: String) {
        val file = File("my_custom_error_file.txt")
        file.appendText(text = error)
    }
}
```

## 5. D: Принцип инверсии зависимостей (Dependency Inversion Principle, DIP)

Основная идея: Высокоуровневые модули не должны зависеть от низкоуровневых; оба должны зависеть от абстракций. Абстракции не должны зависеть от деталей. Детали должны зависеть от абстракций.

Простой пример:
Класс DataHandler не должен напрямую зависеть от класса MySQLDatabase. Вместо этого он должен зависеть от абстракции — интерфейса Database.

```java
interface Database {
    void saveData(String data);
}

class MySQLDatabase implements Database {
    public void saveData(String data) {
        // сохранение данных в MySQL
    }
}

class DataHandler {
    private Database database;
    public DataHandler(Database database) {
        this.database = database;
    }

    public void save(String data) {
        database.saveData(data);
    }
}
```

Здесь DataHandler зависит от интерфейса Database, что позволяет легко менять базу данных (например, с MySQL на PostgreSQL) без изменения основной логики программы.

Практический пример:
Вернемся к MainRepository. Он напрямую зависит от конкретного класса FirebaseAuth. Что будет, если нам надо будет изменить авторизацию и логиниться не через Firebase, а, например, по API запросу? Придется изменять много кода.

Для решения этой проблемы создаем интерфейс Authenticator — это и есть наша абстракция.

```java
interface Authenticator{
    fun signInWithEmailAndPassword(email: String, password: String)
}

// Конкретные реализации
class FirebaseAuthenticator : Authenticator{
    override fun signInWithEmailAndPassword(email: String, password: String) {
        FirebaseAuth.getInstance().signInWithEmailAndPassword(email, password)
    }
}

class CustomApiAuthenticator : Authenticator{
    override fun signInWithEmailAndPassword(email: String, password: String) {
        //другой способ логина
    }
}
```

И теперь в классе MainRepository нет зависимости от конкретной реализации, а только от абстракции.

```java
class MainRepository (
    private val auth: Authenticator,
    private val fileLogger: FileLogger,
) {
    suspend fun loginUser(email: String, password: String) {
        try{
            auth.signInWithEmailAndPassword(email, password)
        }catch(e: Exception) {
            fileLogger.logError(e.message.toString())
        }
    }
}
```

Теперь, чтобы изменить способ авторизации, нам надо изменить только одну строчку в классе модуля. 

Мы рассмотрели SOLID — принципы проектирования. Больше об ООП в целом, основах программирования — нескучно и с сотнями часами практики — на [курсах программирования](https://javarush.com/courses) от JavaRush. Пора решить несколько задач :)