Исключения (Exceptions) в Java — это механизм для обработки ошибок и непредвиденных ситуаций, возникающих во время выполнения программы. Для их обработки используется конструкция try-catch-finally, где в блоке try находится потенциально опасный код, в catch — код для обработки ошибки, а finally выполняется в любом случае. В повседневной жизни иногда возникают ситуации, которые мы не планировали. Например, встаешь утром на работу, ищешь зарядное устройство к телефону — а его нет. Идешь в ванную, чтобы умыться – отключили воду. Сел в машину – не заводится. Но человек в состоянии довольно легко справиться с такими непредвиденными ситуациями. А как с ними справляются Java-программы, постараемся разобраться в этой статье.

## Что такое исключения (exceptions java)

В мире программирования возникновение ошибок и непредвиденных ситуаций при выполнении программы называют исключением (exception). В программе исключения могут возникать в результате неправильных действий пользователя, отсутствии необходимого ресурса на диске, или потери соединения с сервером по сети. Причинами исключений при выполнении программы также могут быть ошибки программирования или неправильное использование API. В отличие от нашего мира, программа должна четко знать, как поступать в такой ситуации. Для этого в Java предусмотрен механизм исключений.

## Кратко о ключевых словах try, catch, finally, throws

Обработка исключений в Java основана на использовании в программе следующих ключевых слов:

- try – определяет блок кода, в котором может произойти исключение;
- catch – определяет блок кода, в котором происходит обработка исключения;
- finally – определяет блок кода, который является необязательным, но при его наличии выполняется в любом случае независимо от результатов выполнения блока try.

Эти ключевые слова используются для создания в программном коде специальных обрабатывающих конструкций: try{}catch, try{}catch{}finally, try{}finally{}.

- throw – используется для возбуждения исключения;
- throws – используется в сигнатуре методов для предупреждения, о том что метод может выбросить исключение.

Пример использования ключевых слов в Java-программе:

```java
import java.util.Scanner;
import java.util.InputMismatchException;

// Метод считывает число с клавиатуры
public int readNumber() throws InvalidInputException {
    Scanner scanner = new Scanner(System.in);
    int number = 0;

    // В блок try заключаем код, в котором может произойти исключение
    // Scanner может выбросить InputMismatchException, если введено не число
    try {
        System.out.print("Введите число: ");
        number = scanner.nextInt();

        // Проверяем валидность введенного числа
        if (number < 0) {
            throw new InvalidInputException("Число не может быть отрицательным!");
        }

    } catch (InputMismatchException e) {
        // Обрабатываем ситуацию, когда введено не число
        System.out.println("Ошибка: введено не число!");
        throw new InvalidInputException("Некорректный ввод данных");

    } finally {
        // Блок finally выполняется всегда
        scanner.close();
        System.out.println("Ввод данных завершен");
    }

    return number;
}
```

## Для чего нужен механизм исключений?

Посмотрим на пример из реального мира. Представьте, что на автомобильной дороге есть участок с аварийным мостом с ограниченной грузоподъемностью. Если по нему поедет автомобиль с массой, превышающей грузоподъемность моста, он может разрушиться, и ситуация для водителя может стать, мягко говоря, исключительной. Чтобы этого не произошло, дорожная служба заблаговременно устанавливает предупредительные знаки на дороге. Водитель автомобиля, глядя на предупреждающий знак, будет сравнивать массу своего автомобиля с разрешенной для проезда по мосту. Если она превышает ее – он поедет по объездному пути. Благодаря действиям дорожной службы водители грузового транспорта, во-первых, получили возможность заблаговременно изменять свой путь, во-вторых, предупреждены об опасности на основном пути, и, наконец, предупреждены о невозможности использования моста при определенных условиях.

![Исключения в Java - 2](https://cdn.javarush.com/images/article/9943b02d-8a5c-4278-8472-09a4ed14eaed/512.webp)

Возможность предупреждения и разрешения исключительной ситуации в программе для ее продолжения – одна из причин использования исключений в Java. Механизм исключений также позволяет защитить написанный вами код (программный интерфейс) от неправильного использования пользователем за счет валидации (проверки) входящих данных. Давайте теперь на секунду побудем дорожной службой. Во-первых, вы должны знать места, где автомобилистов могут ждать неприятности. Во-вторых, вам нужно заготовить и установить предупредительные знаки. И, наконец, вам нужно предусмотреть объездные маршруты в случае опасности на основном пути. В Java механизм исключений работает похожим образом. На стадии разработки программы мы «ограждаем» опасные участки кода в отношении исключений с помощью блока try{}, предусматриваем «запасные» пути с помощью блока catch{}, в блоке finally{} мы пишем код, который выполняется в программе при любом исходе. В случаях, когда мы не можем предусмотреть «запасной путь» или намеренно хотим предоставить право его выбора пользователю, мы должны, по крайней мере, предупредить его об опасности. Почему? А вы только вообразите негодование водителя, который доедет до аварийного моста, по которому нельзя проехать, не встретив по дороге ни одного предупреждающего знака! В программировании при написании своих классов и методов мы не всегда можем предвидеть контекст их использования другими разработчиками в своих программах, поэтому не можем предвидеть на 100% правильный путь для разрешения исключительной ситуации. В то же время, правило хорошего тона — предупредить пользователей нашего кода о возможности исключительной ситуации. Механизм исключений Java позволяет нам сделать это с помощью throws – по сути, объявления общего поведения нашего метода, заключающееся в выбрасывании исключения, и предоставляя, таким образом, написание кода по обработке исключения в Java пользователю метода.

## Предупреждение о «неприятностях»

Когда вы не планируете обрабатывать исключение в своем методе, но хотите предупредить пользователей метода о возможных исключительных ситуациях — используйте ключевое слово throws. Это ключевое слово в сигнатуре метода означает, что при определенных условиях метод, может выбросить исключение. Такое предупреждение является частью интерфейса метода и предоставляет право пользователю на собственный вариант реализации обработчика исключения. После throws мы указываем тип выбрасываемого исключения. Обычно это наследники класса Exception Java. Поскольку Java является объектно-ориентированным языком, все исключения в Java представляют собой объекты.

![Исключения в Java - 3|598](https://cdn.javarush.com/images/article/8fc76f74-5d07-43a1-a16b-7a4b77b10879/512.webp)

## Иерархия исключений Java

При возникновении ошибки в процессе выполнения программы исполняющая среда JVM создает объект нужного типа из иерархии исключений Java – множества возможных исключительных ситуаций, унаследованных от общего «предка» – класса Throwable. Исключительные ситуации, возникающие в программе, можно разделить на две группы:

1. Ситуации, при которых восстановление дальнейшей нормальной работы программы невозможно
2. Восстановление возможно.

К первой группе относят ситуации, когда возникают исключения, унаследованные из класса Error. Это ошибки, возникающие при выполнении программы в результате сбоя работы JVM, переполнения памяти или сбоя системы. Обычно они свидетельствуют о серьезных проблемах, устранить которые программными средствами невозможно. Такой вид исключений в Java относится к неконтролируемым (unchecked) на стадии компиляции. К этой группе также относят RuntimeException – исключения, наследники класса Exception, генерируемые JVM во время выполнения программы. Часто причиной возникновения их являются ошибки программирования. Эти исключения также являются неконтролируемыми (unchecked) на стадии компиляции, поэтому написание кода по их обработке не является обязательным. Ко второй группе относят исключительные ситуации, предвидимые еще на стадии написания программы, и для которых должен быть написан код обработки. Такие исключения являются контролируемыми (checked). Основная часть работы разработчика на Java при работе с исключениями – обработка таких ситуаций.

## Создание исключения

При исполнении программы исключение генерируется JVM или вручную, с помощью оператора throw. При этом в памяти создается объект исключения и выполнение основного кода программы прерывается, а обработчик исключений JVM пытается найти способ обработать исключение.

## Обработка исключения

Создание блоков кода, для которых мы предусматриваем обработку исключений в Java, производится в программе с помощью конструкций try{}catch, try{}catch{}finally, try{}finally{}.

![Исключения в Java - 4](https://cdn.javarush.com/images/article/2cb82932-4d46-4565-88a7-73d4d869329b/512.webp)

При возбуждении исключения в блоке try обработчик исключения ищется в следующем за ним блоке catch. Если в catch есть обработчик данного типа исключения – управление переходит к нему. Если нет, то JVM ищет обработчик этого типа исключения в цепочке вызовов методов до тех пор, пока не будет найден подходящий catch. После выполнения блока catch управление передается в необязательный блок finally. В случае, если подходящий блок catch не найден, JVM останавливает выполнение программы, и выводит стек вызовов методов – stack trace, выполнив перед этим код блока finally при его наличии. Пример обработки исключений:

```java
import java.util.Arrays;
import java.util.List;

public class Print {

    void print(String s) {
        if (s == null) {
            throw new NullPointerException("Exception: s is null!");
        }
        System.out.println("Inside method print: " + s);
    }

    public static void main(String[] args) {
        Print print = new Print();
        List list = Arrays.asList("first step", null, "second step");

        for (String s : list) {
            try {
                print.print(s);
            } catch (NullPointerException e) {
                System.out.println(e.getMessage());
                System.out.println("Exception was processed. Program continues");
            } finally {
                System.out.println("Inside block finally");
            }
            System.out.println("Go program....");
            System.out.println("-----------------");
        }
    }
}
```

Результаты работы метода main:

```java
Inside method print: first step
Inside bloсk finally
Go program....
-----------------
Exception: s is null!
Exception was processed. Program continues
Inside bloсk finally
Go program....
-----------------
Inside method print: second step
Inside bloсk finally
Go program....
-----------------
```

Блок `finally` обычно используется для того, чтобы закрыть открытые в блоке try потоки или освободить ресурсы. Однако при написании программы не всегда возможно уследить за закрытием всех ресурсов. Для облегчения нашей жизни разработчики Java предложили нам конструкцию `try-with-resources`, которая автоматически закрывает ресурсы, открытые в блоке try.

### Try-with-resources: современный подход

Начиная с Java 7, появилась конструкция try-with-resources, которая автоматически закрывает ресурсы. Это намного чище и безопаснее, чем явное закрытие в finally:

```java
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.io.IOException;

public class FileReader {

    // Старый подход (до Java 7) - НЕ рекомендуется
    public String readFileOldWay(String filename) throws IOException {
        java.io.BufferedReader reader = null;
        try {
            reader = new java.io.BufferedReader(
                new java.io.FileReader(filename));
            return reader.readLine();
        } finally {
            if (reader != null) {
                reader.close(); // может выбросить IOException!
            }
        }
    }

    // Современный подход с try-with-resources
    public String readFileModernWay(String filename) throws IOException {
        Path path = Paths.get(filename);

        // Files.readString автоматически закрывает все ресурсы
        return Files.readString(path);
    }

    // Если нужно построчное чтение
    public List readAllLines(String filename) throws IOException {
        Path path = Paths.get(filename);

        // Читаем все строки за один раз
        return Files.readAllLines(path);
    }

    // Для больших файлов - потоковое чтение
    public void processLargeFile(String filename) throws IOException {
        Path path = Paths.get(filename);

        // Stream автоматически закроется после блока try
        try (var lines = Files.lines(path)) {
            lines.filter(line -> !line.isEmpty())
                 .forEach(System.out::println);
        }
    }
}
```

### Множественные ресурсы в try-with-resources:

```java
import java.util.Scanner;
import java.nio.file.Files;
import java.nio.file.Path;
import java.io.PrintWriter;

public void copyFileContent(String source, String destination) throws IOException {
    Path sourcePath = Paths.get(source);
    Path destPath = Paths.get(destination);

    // Можно открыть несколько ресурсов, разделяя их точкой с запятой
    try (Scanner scanner = new Scanner(Files.newInputStream(sourcePath));
         PrintWriter writer = new PrintWriter(Files.newOutputStream(destPath))) {

        while (scanner.hasNextLine()) {
            writer.println(scanner.nextLine());
        }
        // Оба ресурса автоматически закроются в обратном порядке
    }
}
```

## Multi-catch: обработка нескольких исключений

Начиная с Java 7, можно обрабатывать несколько типов исключений в одном блоке catch, разделяя их вертикальной чертой (|). Это делает код более компактным и избегает дублирования логики обработки.

```java
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.io.IOException;
import java.nio.file.NoSuchFileException;
import java.nio.charset.StandardCharsets;

public void readAndProcessFile(String filename) {
    Path path = Paths.get(filename);

    try {
        // Современный способ чтения файла
        String content = Files.readString(path, StandardCharsets.UTF_8);

        // Обработка содержимого
        int number = Integer.parseInt(content.trim());
        System.out.println("Число из файла: " + number);

    } catch (NoSuchFileException | SecurityException e) {
        // Обработка проблем с доступом к файлу
        System.out.println("Не удалось получить доступ к файлу: " + e.getMessage());

    } catch (NumberFormatException e) {
        // Обработка ошибки преобразования
        System.out.println("Файл не содержит корректное число");

    } catch (IOException e) {
        // Обработка других ошибок ввода-вывода
        System.out.println("Ошибка при чтении файла: " + e.getMessage());
    }
}
```

Важно : в multi-catch блоке нельзя указывать исключения, которые находятся в отношении наследования друг к другу. Например, нельзя написать catch (Exception | IOException e), так как IOException наследуется от Exception.

## Создание собственных исключений

Иногда стандартных исключений Java недостаточно для описания специфических ситуаций в вашем приложении. В таких случаях можно создать собственные типы исключений, наследуясь от Exception (для checked) или RuntimeException (для unchecked).

```java
// Checked исключение - требует обязательной обработки
public class InvalidUserAgeException extends Exception {
    private int age;

    public InvalidUserAgeException(String message, int age) {
        super(message);
        this.age = age;
    }

    public int getAge() {
        return age;
    }
}

// Unchecked исключение - обработка необязательна
public class InsufficientBalanceException extends RuntimeException {
    private double balance;
    private double requiredAmount;

    public InsufficientBalanceException(double balance, double requiredAmount) {
        super("Недостаточно средств. Баланс: " + balance + ", требуется: " + requiredAmount);
        this.balance = balance;
        this.requiredAmount = requiredAmount;
    }

    public double getBalance() {
        return balance;
    }

    public double getRequiredAmount() {
        return requiredAmount;
    }
}
```

Пример использования:

```java
public class UserService {
    public void registerUser(String name, int age) throws InvalidUserAgeException {
        if (age < 18) {
            throw new InvalidUserAgeException("Пользователь должен быть старше 18 лет", age);
        }
        // регистрация пользователя
    }
}

public class BankAccount {
    private double balance;

    public void withdraw(double amount) {
        if (amount > balance) {
            throw new InsufficientBalanceException(balance, amount);
        }
        balance -= amount;
    }
}
```

Выбор между checked и unchecked зависит от ситуации: - Используйте checked исключения для ошибок, которые можно предвидеть и от которых можно восстановиться (невалидные данные пользователя, отсутствие файла) - Используйте unchecked исключения для программных ошибок или ситуаций, где восстановление невозможно (нарушение бизнес-логики, невалидное состояние объекта)

## Частые вопросы об исключениях (FAQ)

Этот раздел отвечает на самые популярные вопросы об исключениях, которые задают разработчики. 1. Почему переменные из блока try нельзя использовать в catch или finally? Рассмотрим пример:

```java
try {
    Path path = Paths.get("data.txt");
    String content = Files.readString(path);
    int number = Integer.parseInt(content);
} catch (IOException e) {
    e.printStackTrace();
    System.out.println(number); // ОШИБКА КОМПИЛЯЦИИ!
}
```

Проблема в том, что неизвестно, в какой момент блока try могло возникнуть исключение. Возможно, исключение произошло до того, как переменная s была объявлена. В нашем примере FileNotFoundException может возникнуть на второй строке, когда переменная s еще не создана. Решение: объявляйте переменные вне блока try, если они нужны в catch или finally.

```java
int number = 0;
try {
    Path path = Paths.get("data.txt");
    String content = Files.readString(path);
    number = Integer.parseInt(content);
} catch (IOException e) {
    e.printStackTrace();
    System.out.println(number); // теперь работает, number = 0
}
```

2. Можно ли поймать несколько разных исключений в одном блоке catch? Да, начиная с Java 7 можно использовать multi-catch синтаксис (см. пример выше в статье). Также можно поймать несколько исключений, указав их общий родительский класс:

```java
try {
    // код
} catch (IOException e) {
    // поймает FileNotFoundException, NoSuchFileException и другие наследники IOException
}
```

3. Может ли конструктор выбрасывать исключения? Да, конструктор — это особый вид метода, и он может выбрасывать исключения точно так же:

```java
public class User {
    private String email;

    public User(String email) throws InvalidEmailException {
        if (!email.contains("@")) {
            throw new InvalidEmailException("Некорректный email: " + email);
        }
        this.email = email;
    }
}

// Использование
try {
    User user = new User("invalid-email");
} catch (InvalidEmailException e) {
    System.out.println(e.getMessage());
}
```

4. Можно ли использовать return в блоке finally? Технически да, но это плохая практика. return в finally переопределит return из блока try или catch, что может привести к неожиданному поведению:

```java
public int getValue() {
    try {
        return 1;
    } finally {
        return 2; // вернется 2, а не 1!
    }
}
```

Избегайте return, break и continue в блоке finally. 5. Можно ли выбрасывать исключения в блоке finally? Да, можно, но это усложняет читаемость кода. Лучше выносить такой код в отдельный метод:

```java
// Плохо
try {
    operation1();
} finally {
    try {
        operation2(); // может выбросить исключение
    } catch (Exception e) {
        e.printStackTrace();
    }
}

// Хорошо
try {
    operation1();
} finally {
    cleanupResources(); // метод с собственной обработкой исключений
}
```

6. Почему Double.parseDouble(null) и Integer.parseInt(null) выбрасывают разные исключения?

```java
Integer.parseInt(null); // java.lang.NumberFormatException: null
Double.parseDouble(null); // java.lang.NullPointerException
```

Это особенность реализации JDK. Методы были написаны разными разработчиками в разное время. Не стоит полагаться на конкретный тип исключения в таких случаях — просто проверяйте входные данные на null. 7. Какие основные Runtime исключения нужно знать? Вот самые распространенные unchecked исключения: NullPointerException — попытка использовать null как объект ArrayIndexOutOfBoundsException — выход за границы массива IllegalArgumentException — передан некорректный аргумент в метод IllegalStateException — объект находится в неподходящем состоянии NumberFormatException — ошибка преобразования строки в число ClassCastException — некорректное приведение типов ArithmeticException — арифметическая ошибка (например, деление на 0) Эти исключения можно использовать для валидации:

```java
public void setAge(int age) {
    if (age < 0 || age > 150) {
        throw new IllegalArgumentException(
            "Возраст должен быть от 0 до 150. Получено: " + age
        );
    }
    this.age = age;
}
```

8. В чем разница между throw и throws? throw — это оператор, который непосредственно выбрасывает исключение в коде throws — это объявление в сигнатуре метода, предупреждающее о возможном исключении

```java
throw new IllegalArgumentException("error"); // выбрасываем исключение

public void method() throws IOException { // предупреждаем об исключении
    // код, который может выбросить IOException
}
```

9. Что происходит, если исключение не обработано? Если для исключения не найден подходящий блок catch во всей цепочке вызовов методов: 1. Выполняется блок finally (если есть) 2. JVM останавливает выполнение потока 3. Выводится stack trace — трассировка стека вызовов 4. В случае main потока — программа завершается 10. Когда использовать checked, а когда unchecked исключения? Checked (наследники Exception): — Ситуация предсказуема и может быть обработана — Ошибка вызвана внешними факторами (файл не найден, сеть недоступна) — Вызывающий код должен знать о возможной проблеме Unchecked (наследники RuntimeException): — Ошибка программирования (неправильные аргументы, нарушение контракта) — Восстановление невозможно или бессмысленно — Проблема должна быть исправлена в коде, а не обработана в runtime

#### Итоги

Использование исключений в Java позволяет повысить отказоустойчивость программы за счет использования «запасных» путей, отделить логику основного кода от кода обработки исключительных ситуаций за счет использования блоков catch, а также дает нам возможность переложить обработку исключений на пользователя нашего кода с помощью throws.