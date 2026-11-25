## Вступление

Мы уже рассматривали в [первой части](https://javarush.com/groups/posts/2047-threadom-java-ne-isportishjh--chastjh-i---potoki), как создаются потоки. Ещё раз вспомним. 

![Thread'ом Java не испортишь: Часть IV — Callable, Future и друзья - 1](https://cdn.javarush.com/images/article/2e89a4c6-8d22-4ca5-95d2-9151e6ca180b/800.webp)

Поток — это `Thread`, в нём что-то запускается `run`, поэтому воспользуемся [tutorialspoint java online compiler](https://www.tutorialspoint.com/compile_java_online.php)'ом и выполним следующий код:

```java
public class HelloWorld {

    public static void main(String []args){
        Runnable task = () -> {
            System.out.println("Hello World");
        };
        new Thread(task).start();
    }
}
```

Единтсвенный ли это вариант запуска задачи в потоке?

## java.util.concurrent.Callable

Оказывается, у [java.lang.Runnable](https://docs.oracle.com/javase/8/docs/api/java/lang/Runnable.html) есть брат и зовут его [java.util.concurrent.Callable](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Callable.html) и появился он на свет в Java 1.5. В чём же различия? Если приглядеться к JavaDoc этого интерфейса, мы видим, что в отличие от `Runnable`, новый интерфейс объявляет метод `call`, который возвращает результат. Кроме того, по умолчанию он throws Exception. То есть избавляет нас от необходимости на проверяемые исключения писать `try-catch` блоки. Уже неплохо, правда? Теперь у нас есть вместо `Runnable` новый task:

```java
Callable task = () -> {
	return "Hello, World!";
};
```

Но что с ним делать? Зачем нам вообще задача, выполняемая в потоке, которая возвращает результат? Очевидно, что в дальнейшем мы рассчитываем получить результат действий, которыев в будущем будут выполнены. Будущее по-английский — Future. И интерфейс есть с точно таким же именем: `java.util.concurrent.Future`

## java.util.concurrent.Future

Интерфейс [java.util.concurrent.Future](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html) описывает API для работы с задачами, результат которых мы планируем получить в будущем: методы получения результата, методы проверки статуса. 

Для `Future` нас интересует его реализация [java.util.concurrent.FutureTask](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/FutureTask.html). То есть это `Task`, который будет выполнен во `Future`. Чем эта реализация ещё интересна, так это тем, что она реализует и `Runnable`. Можно считать это своего рода адаптером старой модели работы с задачами в потоках и новой модели (новой в том смысле, что она появилась в java 1.5). Вот пример:

```java
import java.util.concurrent.Callable;
import java.util.concurrent.FutureTask;

public class HelloWorld {

    public static void main(String []args) throws Exception {
        Callable task = () -> {
            return "Hello, World!";
        };
        FutureTask<String> future = new FutureTask<>(task);
        new Thread(future).start();
        System.out.println(future.get());
    }
}
```

Как видно из примера, мы получаем при помощи метода `get` результат из задачи `task`. 

==(!)Важно==, что в момент получения результата при помощи метода `get` выполнение становится синхронным. Как вы думаете, какой механизм тут будет использован? Правильно, нет блока синхронизации — поэтому [WAITING](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.State.html#WAITING) в JVisualVM мы увидим не как `monitor` или `wait`, а как тот самый `park` (т.к. используется механизм `LockSupport`).

## Функциональные интерфейсы

Дальше пойдёт речь про классы из Java 1.8, поэтому не лишним будет сделать краткое введение. Посмотрим на следующий код:

```java
Supplier<String> supplier = new Supplier<String>() {
	@Override
	public String get() {
		return "String";
	}
};
Consumer<String> consumer = new Consumer<String>() {
	@Override
	public void accept(String s) {
		System.out.println(s);
	}
};
Function<String, Integer> converter = new Function<String, Integer>() {
	@Override
	public Integer apply(String s) {
		return Integer.valueOf(s);
	}
};
```

Как же много лишнего кода, не правда ли? Каждый из объявляемых классов выполняет какую-то одну функцию, но для её описания мы используем кучу лишнего вспомогательного кода. И разработчики Java так же подумали. Поэтому, они ввели набор "функциональных интерфейсов" (`@FunctionalInterface`) и решили, что теперь Java сама будет "додумывать" за нас всё, кроме важного:

```java
Supplier<String> supplier = () -> "String";
Consumer<String> consumer = s -> System.out.println(s);
Function<String, Integer> converter = s -> Integer.valueOf(s);
```

`Supplier` — поставщик. Он не имеет параметров, но возвращает что-то, то есть поставляет это. 

`Consumer` — потребитель. Он принимает на вход что-то (параметр s) и с этим что-то что-то делает, то есть потребляет что-то. 

Есть ещё функция. Она принимает на вход что-то (параметр `s`), что-то делает и возвращает что-то. Как мы видим, активно используются дженерики. В случае неуверенности можно вспомнить про них и прочитать "[Теория дженериков в Java или как на практике ставить скобки](https://javarush.com/groups/posts/2004-teorija-dzhenerikov-v-java-ili-gde-na-praktike-stavitjh-skobki)".

## CompletableFuture

Шло время, и в Java 1.8 появился новый класс, который зовётся `CompletableFuture`. Он реализует интерфейс `Future`, то есть наши `task` будут выполнены в будущем, и мы сможем выполнить `get` и получить результат. Но ещё он реализует некоторый `CompletionStage`. Из перевода уже понятно его назначение: это некий этап (Stage) каких-то вычислений. С кратким введением в тему можно ознакомиться в обзоре "[Introduction to CompletionStage and CompletableFuture](https://web.archive.org/web/20170609111659/http://millross-consultants.com/completion-stage-future-introduction.html)". 

Давайте перейдём сразу к делу. Посмотрим на список доступных статических методов, которые нам помогут начать: 

![Thread'ом Java не испортишь: Часть IV — Callable, Future и друзья - 2](https://cdn.javarush.com/images/article/231e6b36-edb8-42b1-b8bb-04ed8b95c034/800.webp)

Вот варианты их использования:

```java
import java.util.concurrent.CompletableFuture;
public class App {
    public static void main(String []args) throws Exception {
        // CompletableFuture уже содержащий результат
        CompletableFuture<String> completed;
        completed = CompletableFuture.completedFuture("Просто значение");
        // CompletableFuture, запускающий (run) новый поток с Runnable, поэтому он Void
        CompletableFuture<Void> voidCompletableFuture;
        voidCompletableFuture = CompletableFuture.runAsync(() -> {
            System.out.println("run " + Thread.currentThread().getName());
        });
        // CompletableFuture, запускающий новый поток, результат которого возьмём у Supplier
        CompletableFuture<String> supplier;
        supplier = CompletableFuture.supplyAsync(() -> {
            System.out.println("supply " + Thread.currentThread().getName());
            return "Значение";
        });
    }
}
```

Если мы выполним этот код, то увидим, что создание `CompletableFuture` подразумевает запуск и всей цепочки. Поэтому при некоторой схожести со SteamAPI из Java8 в этом отличие этих подходов. Например:

```java
List<String> array = Arrays.asList("one", "two");
Stream<String> stringStream = array.stream().map(value -> {
	System.out.println("Executed");
	return value.toUpperCase();
});
```

Это пример Java 8 Stream Api (подробнее можно с ним ознакомиться здесь "[Руководство по Java 8 Stream API в картинках и примерах](https://annimon.com/article/2778)"). Если запустить этот код, то `Executed` не отобразится. То есть при создании стрима в Java стрим не запускается сразу, а ждёт, когда из него захотят значение. А вот `CompletableFuture` запускает цепочку на выполнение сразу, не дожидаясь того, что у него попросят посчитанное значение. Считаю важным это понимать. 

Итак, у нас есть CompletableFuture. Как же мы можем составить цепочку и какие у нас есть средства? 

Вспомним про функциональные интерфейсы, о которых мы писали ранее.

- У нас есть функция (`Function`), которая принимает А и возвращает Б. Имеет единственный метод — `apply` (применить).
- У нас есть потребитель (`Consumer`), которая принимает А и ничего не возвращает (Void). Имеет единственный метод — `accept` (принять).
- У нас есть запускаемый в потоке код `Runnable`, который не принимает и не возвращает. Имеет единственный метод — `run` (запустить).

Второе, что надо помнить, что `CompletalbeFuture` в своей работе использует `Runnable`, потребителей и функции. Учитывая это, вы всегда сможете вспомнить, что с `CompletableFuture` можно делать так:

```java
public static void main(String []args) throws Exception {
        AtomicLong longValue = new AtomicLong(0);
        Runnable task = () -> longValue.set(new Date().getTime());
        Function<Long, Date> dateConverter = (longvalue) -> new Date(longvalue);
        Consumer<Date> printer = date -> {
            System.out.println(date);
            System.out.flush();
        };
        // CompletableFuture computation
        CompletableFuture.runAsync(task)
                         .thenApply((v) -> longValue.get())
                         .thenApply(dateConverter)
                         .thenAccept(printer);
}
```

У методов `thenRun`, `thenApply` и `thenAccept` есть версии `Async`. Это значит, что эти стадии будут выполнены в новом потоке. Он будет взят из особого пула, поэтому заранее неизвестно, какой поток будет, новый или прежний. Всё зависит от того, на сколько тяжёлые задачи. 

Помимо этих методов есть ещё три интересные возможности. 

Для наглядности представим, что у нас есть некий сервис, который получает какое-то сообщение откуда-то и на это требуется время:

```java
public static class NewsService {
	public static String getMessage() {
		try {
			Thread.currentThread().sleep(3000);
			return "Message";
		} catch (InterruptedException e) {
			throw new IllegalStateException(e);
		}
	}
}
```

Теперь, давайте посмотрим на другие возможности, которые предоставляет `CompletableFuture`. Мы можем объединять результат `CompletableFuture` с результатом другого `CompletableFuture`:

```java
Supplier newsSupplier = () -> NewsService.getMessage();

CompletableFuture<String> reader = CompletableFuture.supplyAsync(newsSupplier);
CompletableFuture.completedFuture("!!")
				 .thenCombine(reader, (a, b) -> b + a)
				 .thenAccept(result -> System.out.println(result))
				 .get();
```

Тут стоить обратить внимание, что по умолчанию потоки будут демон-потоками, поэтому для наглядности мы используем `get`, чтобы дождаться результат. А ещё мы можем не только объединить (combine), но и возвращать `CompletableFuture`:

```java
CompletableFuture.completedFuture(2L)
				.thenCompose((val) -> CompletableFuture.completedFuture(val + 2))
                               .thenAccept(result -> System.out.println(result));
```

Тут хочется отметить, что для краткости использован метод `CompletableFuture.completedFuture`. Данный метод не создаёт новый поток, поэтому остальная цепочка будет выполнена в том же потоке, в котором был вызван `completedFuture`. 

Также есть метод `thenAcceptBoth`. Он очень похож на `accept`, но если `thenAccept` принимает `consumer`, то `thenAcceptBoth` принимает на вход ещё один `CompletableStage` + `BiConsumer`, то есть `consumer`, который на вход принимает 2 источника, а не один. 

Есть ещё интересная возможность со словом `Either`: 

![Thread'ом Java не испортишь: Часть IV — Callable, Future и друзья - 3](https://cdn.javarush.com/images/article/36a07217-c968-48f8-8ef6-d82b0c09bf4e/800.webp)

Данные методы принимают альтернативный `CompletableStage` и будут выполнены на том `CompletableStage`, который первее выполнится. И закончить этот обзор хочется ещё одной интересной возможностью `CompletableFuture` — обработкой ошибок.

```java
CompletableFuture.completedFuture(2L)
				 .thenApply((a) -> {
					throw new IllegalStateException("error");
				 }).thenApply((a) -> 3L)
				 //.exceptionally(ex -> 0L)
				 .thenAccept(val -> System.out.println(val));
```

Данный код ничего не сделает, т.к. упадёт исключение и ничего не будет. Но если мы раскомментируем `exceptionally`, то мы определим поведение. На тему `CompletableFuture` советую так же посмотреть следующее видео:

- [CompletableFuture. Хочется взять и применить (2015)](https://www.youtube.com/watch?v=hqR41XVx3kM)
- [CompletableFuture in Java 8, asynchronous processing done right](https://www.youtube.com/watch?v=-MBPQ7NIL_Y)

По моему скромному мнению, данные видео — одни из самых наглядных на просторах интернета. Из них должно быть понятно, как это всё работает, какой у нас есть арсенал и зачем это всё нужно.

## Заключение

Надеюсь, теперь стало понятно, как можно использовать потоки для получения вычислений после того, как они будут высчитаны. Дополнительный материал:

- [Пишем асинхронный код с CompletableFuture](https://kurspc.com.ua/node/424)
- [Introduction to CompletionStage and CompletableFuture](https://web.archive.org/web/20170609111659/http://millross-consultants.com/completion-stage-future-introduction.html)
- [Java 8 CompletableFuture. Часть 2](https://web.archive.org/web/20220701165420/https://kurspc.com.ua/node/424)
- [Guide To CompletableFuture](https://www.baeldung.com/java-completablefuture)