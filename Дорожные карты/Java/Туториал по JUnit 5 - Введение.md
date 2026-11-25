В этом **туториале** **по JUnit 5** рассказывается о том, как JUnit адаптировал стиль кодирования [Java 8](https://howtodoinjava.com/java-8-tutorial/) и некоторые другие функции. Узнайте, чем JUnit 5 отличается от JUnit 4.

JUnit 5 — наиболее широко используемая среда тестирования для приложений Java. JUnit долгое время отлично справлялся со своей задачей.

Между тем, JDK 8 привнес в java интересные функции и, в первую очередь, [лямбда-выражения](https://howtodoinjava.com/java8/lambda-expressions/). JUnit 5 был нацелен на адаптацию стиля программирования Java 8; вот почему Java 8 является минимально необходимой версией для создания и выполнения тестов в JUnit 5 (хотя можно запускать тесты, написанные с помощью JUnit 3 или JUnit 4 для обратной совместимости).

**Оглавление**

1. Архитектура JUnit 5
    
2. Установка
    
3. Аннотации JUnit 5
    
4. Написание тестов
    
5. Написание наборов тестов
    
6. Утверждения
    
7. Предположения
    
8. Обратная совместимость для JUnit 4
    
9. Заключение
    

### 1. Архитектура JUnit 5

По [сравнению с JUnit 4](https://howtodoinjava.com/junit5/junit-5-vs-junit-4/), JUnit 5 состоит из нескольких разных модулей из трех разных подпроектов:

> _JUnit 5 = Платформа JUnit + JUnit Jupiter + JUnit Vintage_

- **JUnit Jupiter**: включает новые модели программирования и расширения для написания тестов. В нем есть все новые аннотации junit и `TestEngine` реализация для запуска тестов, написанных с этими аннотациями.
    
- **Платформа JUnit**: чтобы иметь возможность запускать тесты junit, IDE, инструменты сборки или плагины должны включать и расширять API платформы. Он определяет `TestEngine` API для разработки новых фреймворков тестирования, работающих на платформе. Он также предоставляет средство запуска консоли для запуска платформы из командной строки и создания подключаемых модулей для Gradle и Maven.
    
- **JUnit Vintage**: его основная цель - поддерживать выполнение на платформе JUnit 5 тестов, написанных для JUnit 3 и JUnit 4. Это есть обратная совместимость.
    

![Архитектура JUnit 5](https://habrastorage.org/r/w1560/getpro/habr/upload_files/d87/277/e29/d87277e291a08d3488681f512d1ac31a.png "Архитектура JUnit 5")

Архитектура JUnit 5

### 2. Установка

Вы можете использовать JUnit 5 в своем проекте Maven или Gradle, включив как минимум следующие зависимости:

- **junit-jupiter-api**: это основной модуль, в котором расположены все основные аннотации, такие как @Test, аннотации и [утверждения](https://howtodoinjava.com/junit5/junit-5-assertions-examples/) [метода жизненного цикла](https://howtodoinjava.com/junit5/junit-5-test-lifecycle/).
    
- **junit-jupiter-engine**: он имеет реализацию тестового движка, которая требуется во время выполнения для выполнения тестов.
    
- **junit-platform-suite**: поддержка [@Suite,](https://howtodoinjava.com/junit5/junit5-test-suites-examples/) предоставляемая этим модулем, чтобы сделать средство запуска JUnitPlatform устаревшим.
    

**pom.xml**

```html
<properties>
    <junit.jupiter.version>5.8.1</junit.jupiter.version>
    <junit.platform.version>1.8.1</junit.platform.version>
</properties>
<dependencies>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-engine</artifactId>
        <version>${junit.jupiter.version}</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-api</artifactId>
        <version>${junit.jupiter.version}</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-params</artifactId>
        <version>${junit.jupiter.version}</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.junit.platform</groupId>
        <artifactId>junit-platform-suite</artifactId>
        <version>${junit.platform.version}</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

**build.gradle**

```java
build.gradledependencies {
    testRuntime("org.junit.jupiter:junit-jupiter-api:5.8.1")
    testRuntime("org.junit.jupiter:junit-jupiter-engine:5.8.1")
    testRuntime("org.junit.jupiter:junit-jupiter-params:5.8.1")
    testRuntime("org.junit.platform:junit-platform-suite:1.8.1")
}
test {
    useJUnitPlatform()
}
```

![Модули JUNit 5](https://habrastorage.org/r/w1560/getpro/habr/upload_files/198/af7/eb5/198af7eb5239b11abdfa309b15c6deb0.jpeg "Модули JUNit 5")

Модули JUNit 5

> Подробнее: [пример Maven](https://howtodoinjava.com/junit5/junit5-maven-dependency/) | [Пример Gradle](https://howtodoinjava.com/junit5/junit-5-gradle-dependency-build-gradle-example/)

### 3. Аннотации JUnit 5

JUnit 5 предлагает следующие аннотации для написания тестов.

|**Аннотации**|**Описание**|
|---|---|
|`@BeforeEach`|Аннотированный метод будет запускаться перед каждым тестовым методом в тестовом классе.|
|`@AfterEach`|Аннотированный метод будет запускаться после каждого тестового метода в тестовом классе.|
|`@BeforeAll`|Аннотированный метод будет запущен перед всеми тестовыми методами в тестовом классе. Этот метод должен быть статическим.|
|`@AfterAll`|Аннотированный метод будет запущен после всех тестовых методов в тестовом классе. Этот метод должен быть статическим.|
|`@Test`|Он используется, чтобы пометить метод как тест junit.|
|`@DisplayName`|Используется для предоставления любого настраиваемого отображаемого имени для тестового класса или тестового метода|
|`@Disable`|Он используется для отключения или игнорирования тестового класса или тестового метода из набора тестов.|
|`@Nested`|Используется для создания вложенных тестовых классов|
|`@Tag`|Пометьте методы тестирования или классы тестов тегами для обнаружения и фильтрации тестов.|
|`@TestFactory`|Отметить метод - это тестовая фабрика для динамических тестов.|

### 4. Написание тестов

Между JUnit 4 и JUnit 5 нет больших различий в стилях написания тестов. Вот образцы тестов с их методами [жизненного цикла](https://howtodoinjava.com/junit5/junit-5-test-lifecycle/).

Обратите внимание, что все аннотации взяты из пакета _org.junit.jupiter.api_.

**JUnit 5 Tests**

```java
import org.junit.jupiter.api.AfterAll;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

import com.howtodoinjava.junit5.examples.Calculator;

public class AppTest {

    @BeforeAll
    static void setup(){
        System.out.println("@BeforeAll executed");
    }

    @BeforeEach
    void setupThis(){
        System.out.println("@BeforeEach executed");
    }

    @Tag("DEV")
    @Test
    void testCalcOne()
    {
        System.out.println("======TEST ONE EXECUTED=======");
        Assertions.assertEquals( 4 , Calculator.add(2, 2));
    }

    @Tag("PROD")
    @Disabled
    @Test
    void testCalcTwo()
    {
        System.out.println("======TEST TWO EXECUTED=======");
        Assertions.assertEquals( 6 , Calculator.add(2, 4));
    }

    @AfterEach
    void tearThis(){
        System.out.println("@AfterEach executed");
    }

    @AfterAll
    static void tear(){
        System.out.println("@AfterAll executed");
    }
}
```

### 5. Написание наборов тестов

Используя **наборы тестов JUnit 5**, вы можете запускать тесты, распределенные по нескольким тестовым классам и различным пакетам. JUnit 5 предоставляет эти аннотации для создания наборов тестов.

- @Suite
    
- _@SelectClasses_
    
- _@SelectPackages_
    
- _@IncludePackages_
    
- _@ExcludePackages_
    
- _@IncludeClassNamePatterns_
    
- _@ExcludeClassNamePatterns_
    
- _@IncludeTags_
    
- _@ExcludeTags_
    

Для выполнения пакета вам необходимо использовать `@Suite` аннотацию и включить _модуль junit-platform-suite_ в зависимости проекта.

```java
@Suite
@SelectPackages("com.howtodoinjava.junit5.examples")
public class JUnit5TestSuiteExample
{
}
```

### 6. Assertions

Assertions (утверждения) позволяют сравнить ожидаемый результат с фактическим результатом теста.

Для того чтобы держать вещи простыми, все утверждения JUnit Jupiter являются `static` методы в класса [org.junit.jupiter.Assertions](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/Assertions.html), например `assertEquals()`, `assertNotEquals()`.

```java
void testCase()
{
    //Test will pass
    Assertions.assertNotEquals(3, Calculator.add(2, 2));

    //Test will fail
    Assertions.assertNotEquals(4, Calculator.add(2, 2), "Calculator.add(2, 2) test failed");

    //Test will fail
    Supplier<String> messageSupplier  = () -> "Calculator.add(2, 2) test failed";
    Assertions.assertNotEquals(4, Calculator.add(2, 2), messageSupplier);
}
```

> Подробнее: [JUnit 5 Assertions](https://howtodoinjava.com/junit5/junit-5-assertions-examples/)

### 7. Assumptions

Класс [Assumptions](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/Assumptions.html) (предположения) предоставляет `static`методы для поддержки выполнения условного теста на основе предположений. Неуспешное предположение приводит к прерыванию теста.

Предположения обычно используются всякий раз, когда нет смысла продолжать выполнение данного метода тестирования. В отчете о тестировании эти тесты будут отмечены как пройденные.

Предположения класс имеет три таких методов: `assumeFalse()`, `assumeTrue()` и `assumingThat()`

```java
public class AppTest {
    @Test
    void testOnDev()
    {
        System.setProperty("ENV", "DEV");
        Assumptions.assumeTrue("DEV".equals(System.getProperty("ENV")), AppTest::message);
    }

    @Test
    void testOnProd()
    {
        System.setProperty("ENV", "PROD");
        Assumptions.assumeFalse("DEV".equals(System.getProperty("ENV")));
    }

    private static String message () {
        return "TEST Execution Failed :: ";
    }
}
```

> Подробнее: [JUnit 5 Assumptions](https://howtodoinjava.com/junit5/junit-5-assumptions-examples/)

### 8. Обратная совместимость с JUnit 4

JUnit 4 существует уже довольно давно, и на junit 4 написано множество тестов. JUnit Jupiter также должен поддерживать эти тесты. Для этого был разработан подпроект _JUnit Vintage_.

JUnit Vintage предоставляет `TestEngine` реализацию для запуска тестов на основе JUnit 3 и JUnit 4 на платформе JUnit 5.

### 9. Заключение

JUnit 5 кажется таким захватывающим и многофункциональным. И теперь он открыт для расширения сторонними инструментами и API. Как автор тестов, вы можете не чувствовать очень большой разницы, но, когда вы используете расширение или попытаетесь разработать плагин для IDE, вы его оцените.

Вы также можете рассмотреть возможность [добавления тестовых шаблонов в Eclipse](https://howtodoinjava.com/junit5/code-test-templates-eclipse/) IDE, чтобы повысить скорость разработки как разработчика.

Хорошего изучения!!!

→ [Скачать исходный код](https://github.com/lokeshgupta1981/Junit5Examples/tree/master/JUnit5Examples)

Переведены следующие части туториала:

- [Жизненный цикл JUnit 5 теста](https://habr.com/ru/post/590699/)
    
- [Аннотация @BeforeAll](https://habr.com/ru/post/590715/)
    
- [Аннотация @BeforeEach](https://habr.com/ru/post/590741/)
    
- [Аннотация @AfterEach](https://habr.com/ru/post/590757/)
    
- [Аннотация @AfterAll](https://habr.com/ru/post/590909/)
    
- [Аннотация @RepeatedTest](https://habr.com/ru/post/590927/)
    
- [Аннотация @Disabled](https://habr.com/ru/post/590965/)
    
- [Аннотация @ParameterizedTest](https://habr.com/ru/post/591007/)
    
- [Ожидаемое исключение JUnit 5](https://habr.com/ru/post/591305/)
    
- [Примеры утверждений JUnit 5](https://habr.com/ru/post/591587/)
    
- [Отчеты о покрытии кода JUnit тестами с помощью Maven плагина JaCoCo](https://habr.com/ru/post/591553/)
    
- [JUnit — создание отчетов в формате HTML](https://habr.com/ru/post/590199/)