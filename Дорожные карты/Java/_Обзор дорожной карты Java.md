- [[Справочник по Java Collections Framework]]
- [[Collectors]]
- [[Stream API]] или [Stream Doc](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/stream/Stream.html)
- [[Optional]]
- [[Как обрабатывать исключения в Java (Java Exception)]]

## ☕ Что будем изучать в этом модуле?

Мы пройдём путь от основ языка до production-ready микросервисов на Spring-экосистеме.

- **Урок 5.2 (Junior):** Синтаксис Java 21+, ООП, коллекции, Streams, исключения, Maven/Gradle, JUnit, Git, основы HTTP/REST.
- **Урок 5.3 (Middle):** Spring Boot, JPA/Hibernate, PostgreSQL, Redis, Kafka/RabbitMQ, Docker, CI/CD, REST/gRPC, тесты, логирование, мониторинг, облачные паттерны.
- **Урок 5.4 (Ресурсы):** Русскоязычные книги, курсы, сайты, библиотеки и AI-промпты для Java.

## 🏁 Чек-лист понимания

- [ ] Я понимаю, что Java — это платформа (JVM), а не только язык
- [ ] Я вижу разницу между Spring Boot (универсальный стандарт) и Quarkus/Micronaut (cloud-native, быстрый старт)
- [ ] Я осознаю, что виртуальные потоки и Records меняют подход к многопоточности и моделям данных
- [ ] Я готов к долгому, но стабильному пути в Enterprise/FinTech с чёткими процессами и код-ревью




## 📜 1. Синтаксис и современная Java 21+

|       | Что учить                                                 | Почему важно                                                   | Как практиковать                                             |
| ----- | --------------------------------------------------------- | -------------------------------------------------------------- | ------------------------------------------------------------ |
| ==+== | Примитивы vs Обёртки, `var`, приведение типов             | Понимание `null`, autoboxing и когда использовать явные типы   | Напишите калькулятор с проверкой типов и `var` где уместно   |
| ==+== | `switch`-выражения, Pattern Matching for `instanceof`     | Читаемость, отказ от boilerplate-кода                          | Рефакторинг старых `if-else` под современные конструкции     |
| ==+== | **Records**                                               | Неизменяемые DTO без геттеров/сеттеров и `equals` по умолчанию | Создайте `UserRecord(String id, String email, Role role)`    |
| ==+== | Text Blocks, String API (`isBlank`, `strip`, `formatted`) | Удобная работа с SQL/JSON-строками и форматированием           | Сформируйте JSON-ответ через `record` и `String.formatted()` |

## 🏗️ 2. ООП и архитектурная база

Java строится на объектах. Junior обязан уверенно проектировать модели.

- [x] Классы, конструкторы, модификаторы доступа (`public`, `private`, `protected`, package-private)
- [x] Наследование, полиморфизм, инкапсуляция, абстракция
- [x] Интерфейсы vs Абстрактные классы, `default` и `static` методы
- [x] **Sealed classes/interfaces** (Java 17+): ограниченный набор наследников
- [x] Контракты объектов: `equals()`, `hashCode()`, `toString()`, `Comparable<T>`
    
    > 💡 _Практика:_ Спроектирована доменная модель платёжной системы: `Account`, `Transaction`, `PaymentMethod`. Используйте `enum`, `sealed interface` для статусов транзакций, переопределите `equals/hashCode`.
    

## 📦 3. Коллекции и Streams API

Обработка данных в Java сегодня = Streams + Optional.

|       | Концепция                | Что изучить                                                                                |
| ----- | ------------------------ | ------------------------------------------------------------------------------------------ |
| ==+== | **Интерфейсы коллекций** | `List`, `Set`, `Map`, `Queue`, `Deque`                                                     |
| ==+== | **Реализации**           | `ArrayList`, `LinkedList`, `HashSet`, `TreeSet`, `HashMap`, `TreeMap`, `ConcurrentHashMap` |
| ==+== | **Итераторы и циклы**    | `forEach`, enhanced for-loop, `Iterator` vs `ListIterator`                                 |
| ==+== | **Stream API**           | `filter`, `map`, `flatMap`, `reduce`, `collect`, `groupingBy`, `partitioningBy`            |
| ==+== | **Optional<T>**          | Избегание `NullPointerException`, цепочки `map`, `orElseThrow`                             |

> 💡 _Практика:_ Загрузите список `Transaction` из CSV. Отфильтруйте успешные, сгруппируйте по валюте, посчитайте сумму через `Collectors.summingBigDecimal()`. Оберните результат в `Optional`.

## ⚠️ 4. Исключения и работа с файлами

|Тема|Что изучить|
|---|---|
|Checked vs Unchecked исключения|`Exception` vs `RuntimeException`, когда что бросать|
|`try-catch-finally`, **try-with-resources**|Автоматическое закрытие `AutoCloseable` ресурсов|
|Кастомные исключения, цепочки (`initCause`)|Семантическая обработка ошибок вместо `e.printStackTrace()`|
|Java NIO.2: `Path`, `Files`, `BufferedReader`, `Files.readString`|Современное чтение/запись без boilerplate `InputStream`|

## ⚡ 5. Многопоточность (Базовый уровень)

Java 21+ изменила подход к конкурентности. Junior обязан понимать разницу.

- `Thread`, `Runnable`, `Callable`, `Future`
- **Virtual Threads (Loom, Java 21+)**: `Thread.startVirtualThread()`, `Executors.newVirtualThreadPerTaskExecutor()`
- `synchronized`, `volatile`, `ReentrantLock`, `java.util.concurrent` пакеты
- `CompletableFuture` (базовое понимание композиции асинхронных задач)
    
    > 💡 _Практика:_ Напишите HTTP-клиент, который параллельно опрашивает 5 API-эндпоинтов через виртуальные потоки. Агрегируйте ответы и замерьте время.
    

## 🛠️ 6. Сборка, тесты и инструменты

|Инструмент|Зачем|Современный стандарт 2026|
|---|---|---|
|**Maven / Gradle**|Управление зависимостями, сборка, жизненный цикл|Maven для старта (проще), Gradle для сложных проектов|
|**JUnit 5**|Тестирование: `@Test`, `@BeforeEach`, `@ParameterizedTest`, assertions|`org.junit.jupiter.api.*`, `assertThrows`, `assertAll`|
|**Mockito**|Мокирование зависимостей, `@Mock`, `@InjectMocks`, `verify`|Стандарт для unit-тестов сервисного слоя|
|**IntelliJ IDEA**|Отладка, рефакторинг, навигация, live templates|Community/Ultimate, `Run/Debug Configurations`, Breakpoints|

## 🐙 7. Git и основы HTTP/REST

- `init`, `clone`, `status`, `add`, `commit`, `push`, `pull`
- `.gitignore` для Java: `target/`, `*.class`, `*.jar`, `.idea/`, `*.iml`, `.env`
- GitHub/GitLab: PR, code review, защита `main`
- HTTP: методы, статус-коды, заголовки, JSON (`jackson-databind` или `gson`)
- REST: ресурсы, идемпотентность, stateless, контракт API
    
    > 💡 _Практика:_ Инициализируйте Maven-проект, добавьте JUnit 5, напишите тест, залейте на GitHub. Отправьте `POST` запрос через Postman, разберите JSON-ответ.





## 🎯 План практики на этот этап

1. **Проект 1:** Консольное приложение `CurrencyConverter`. Парсинг CSV/JSON, `Records` для моделей, Streams для расчётов, `Optional` для безопасного доступа.
2. **Проект 2:** Многопоточный загрузчик данных. 10 Virtual Threads, `ExecutorService`, агрегация результатов, обработка ошибок через `CompletableFuture`.
3. **Покрытие:** Напишите 5–7 unit-тестов на JUnit 5 + Mockito для сервисного слоя обоих проектов.
4. **Инфраструктура:** Настройте `pom.xml`/`build.gradle`, добавьте JUnit 5, Mockito, `maven-surefire-plugin`. Залейте код на GitHub с `.gitignore` и `README.md`.

## ✅ Чек-лист Junior Java Backend

- [ ] Пишу код на Java 21+, использую `var`, `switch`-выражения, Pattern Matching, Records, Text Blocks
- [ ] Проектирую классы с корректной инкапсуляцией, наследованием и полиморфизмом
- [ ] Переопределяю `equals()`, `hashCode()`, `toString()` по контракту
- [ ] Уверенно работаю с коллекциями: выбираю `List/Set/Map`, понимаю `O(1)` vs `O(n)`
- [ ] Обрабатываю данные через Streams API: `filter`, `map`, `collect`, `groupingBy`, `Optional`
- [ ] Корректно обрабатываю исключения: `try-with-resources`, кастомные ошибки, checked vs unchecked
- [ ] Понимаю разницу между Platform Threads и Virtual Threads (Java 21+)
- [ ] Создаю Maven/Gradle проект, управляю зависимостями, запускаю `mvn clean test`
- [ ] Пишу unit-тесты на JUnit 5, использую Mockito для моков зависимостей
- [ ] Владею базовым Git: commit, branch, merge, PR, `.gitignore` для Java
- [ ] Понимаю HTTP-методы, статус-коды, структуру JSON и принципы REST
- [ ] Отлаживаю код в IntelliJ IDEA: breakpoints, evaluate expression, watch