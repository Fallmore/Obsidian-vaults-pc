## Вступление

Итак, мы знаем, что в Java есть потоки, о чём можно прочитать в обзоре "[[Thread'ом Java не испортишь Часть I — потоки]]". 

![Thread'ом Java не испортишь: Часть V — Executor, ThreadPool, Fork Join - 1](https://cdn.javarush.com/images/article/02e6f204-92ca-4955-bcbd-f1e0d387b25f/800.webp)

Давайте ещё раз посмотрим на типовой код:

```java
public static void main(String []args) throws Exception {
	Runnable task = () -> {
		System.out.println("Task executed");
	};
	Thread thread = new Thread(task);
	thread.start();
}
```

Как мы видим, код для запуска задачи довольно типовой, но на каждый новый запуск нам его придётся повторять. Одно из решений — вынести его в отдельный метод, например, `execute(Runnable runnable)`. Но разработчики Java за нас уже побеспокоились и придумали интерфейс `Executor`:

```java
public static void main(String []args) throws Exception {
	Runnable task = () -> System.out.println("Task executed");
	Executor executor = (runnable) -> {
		new Thread(runnable).start();
	};
	executor.execute(task);
}
```

Как видно, код стал лаконичнее и позволил нам просто написать код по запуску `Runnable` в потоке. Здорово, не так ли? Но это только начало: 

![Thread'ом Java не испортишь: Часть V — Executor, ThreadPool, Fork Join - 2](https://cdn.javarush.com/images/article/fe98eee4-5064-4626-bc15-535a728659ca/512.webp)

_https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Executor.html_

Как видно, у интерфейса `Executor` есть интерфейс-наследник `ExecutorService`. В JavaDoc данного интерфейса сказано, что `ExecutorService` является описанием особого `Executor`'а, который предоставляет методы по остановке работы `Executor`'а и позволяет получить `java.util.concurrent.Future`, чтобы отслеживать процесс выполнения. Ранее, в "[[Thread'ом Java не испортишь Часть IV — Callable, Future и друзья]]" мы уже кратко рассмотрели возможности `Future`. Кто забыл или не читал - советую освежить в памяти ;) 

Что ещё интересного в JavaDoc написано? Что у нас есть специальная фабрика `java.util.concurrent.Executors`, которая нам позволяет создавать доступные по умолчанию реализации `ExecutorService`.

## ExecutorService

Ещё раз вспомним. У нас есть `Executor` для execute (т.е. выполнения) некой задачи в потоке, когда реализация создания потока скрыта от нас. У нас есть `ExecutorService` — особый `Executor`, который имеет набор возможностей по управлению ходом выполнения. И у нас есть фабрика `Executors`, которая позволяет создавать `ExecutorService`. Давайте теперь это проделаем сами:

```java
public static void main(String[] args) throws ExecutionException, InterruptedException {
	Callable<String> task = () -> Thread.currentThread().getName();
	ExecutorService service = Executors.newFixedThreadPool(2);
	for (int i = 0; i < 5; i++) {
		Future result = service.submit(task);
		System.out.println(result.get());
	}
	service.shutdown();
}
```

Как мы видим, мы указали фиксированный пул потоков (`Fixed Thread Pool`) размером 2. После чего мы поочередно отправляем в пул задачи. Каждая задача возвращает строку (`String`), содержащую имя потока (`currentThread().getName()`). Важно в самом конце выполнить shutdown для `ExecutorService`, потому что в противном случае наша программа не завершится. 

В фабрике `Executors` есть и другие фабричные методы. Например, мы можем создать пул всего из одного потока — `newSingleThreadExecutor` или пул с кэшированием `newCachedThreadPool`, когда потоки будут убираться из пула, если они простаивают 1 минуту. 

На самом деле, за этими `ExecutorService` прячется **блокирующая очередь**, в которую помещаются задачи и из которой эти задачи выполняются. Подробнее про блокирующие очереди можно посмотреть в видео "[Блокирующая очередь - Collections #5 - Advanced Java](https://www.youtube.com/watch?v=nUYOGkh9XqE)". А так же можно прочитать обзор "[Блокирующие очереди пакета concurrent](http://java-online.ru/concurrent-queue-block.xhtml)" и ответ на вопрос "[When to prefer LinkedBlockingQueue over ArrayBlockingQueue?](https://stackoverflow.com/questions/35967792/when-to-prefer-linkedblockingqueue-over-arrayblockingqueue)". Супер упрощённо — `BlockingQueue` (блокирующая очередь) блокирует поток, в двух случаях:

- поток пытается получить элементы из пустой очереди
- поток пытается положить элементы в полную очередь

Если посмотреть на реализацию фабричных методов, то мы увидим, как они устроены. Например:

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
}
```

или

```java
public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
}
```

Как мы видим, внутри фабричных методов создаются реализации `ExecutorService`. И в основном это `ThreadPoolExecutor`. Меняются только атрибуты, которые и влияют на работу. 

![Thread'ом Java не испортишь: Часть V — Executor, ThreadPool, Fork Join - 3](https://cdn.javarush.com/images/article/decabcf3-8341-429b-9266-a262f8a4152b/800.webp)

_https://en.wikipedia.org/wiki/Thread_pool#/media/File:Thread_pool.svg_

## ThreadPoolExecutor

Как мы ранее увидели, внутри фабричных методов в основном создаётся `ThreadPoolExecutor`. На функциональность влияет то, какие значения переданы в качестве максимума и минимума потоков, а также какая очередь используется. А использоваться может любая реализация интерфейса `java.util.concurrent.BlockingQueue`. 

Говоря о `ThreadPoolExecutor`'ах, стоит отметить интересные особенности при работе. Например, нельзя посылать задачи в `ThreadPoolExecutor`, если там нет места:

```java
public static void main(String[] args) throws ExecutionException, InterruptedException {
	int threadBound = 2;
	ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(0, threadBound,
            0L, TimeUnit.SECONDS, new SynchronousQueue<>());
	Callable<String> task = () -> {
		Thread.sleep(1000);
		return Thread.currentThread().getName();
	};
	for (int i = 0; i < threadBound + 1; i++) {
		threadPoolExecutor.submit(task);
	}
	threadPoolExecutor.shutdown();
}
```

Данный код упадёт с ошибкой вида:

```java
Task java.util.concurrent.FutureTask@7cca494b rejected from java.util.concurrent.ThreadPoolExecutor@7ba4f24f[Running, pool size = 2, active threads = 2, queued tasks = 0, completed tasks = 0]
```

То есть `task` нельзя засабмитить, т.к. `SynchronousQueue` устроена так, что фактически состоит из одного элемента и не позволяет положить туда больше. Как мы видим, `queued tasks` здесь 0, и в этом нет ничего странного, т.к. это специфика `SynchronousQueue` — фактически это очередь в 1 элемент, которая всегда пустая. (!) Когда один поток кладёт в очередь элемент, он будет ждать, пока другой поток не заберёт элемент из очереди. 

Поэтому, мы можем заменить на `new LinkedBlockingQueue<>(1)` и в ошибке изменится то, что будет указано `queued tasks = 1`. Т.к. очередь всего в 1 элемент, то второй мы уже положить не можем. И упадём на этом. 

Продолжая тему очереди, стоит отметить, что класс `ThreadPoolExecutor` обладает дополнительными методами по обслуживанию очереди. Например, метод `threadPoolExecutor.purge()` удалит из очереди все отменённые задачи, чтобы освободить место в очереди. Ещё одной интересной функцией, связанной с очередью, является обработчик непринятых задач:

```java
public static void main(String[] args) {
	ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(1, 1,
            0L, TimeUnit.SECONDS, new SynchronousQueue());
	Callable<String> task = () -> Thread.currentThread().getName();
	threadPoolExecutor.setRejectedExecutionHandler((runnable, executor) -> System.out.println("Rejected"));
	for (int i = 0; i < 5; i++) {
		threadPoolExecutor.submit(task);
	}
	threadPoolExecutor.shutdown();
}
```

Для примера обработчик просто выводит слово `Rejected` на каждый отказ принимать задачу в очередь. Удобно, не правда ли? Кроме того, `ThreadPoolExecutor` имеет интересного наследника — `ScheduledThreadPoolExecutor`, который является `ScheduledExecutorService`. Он предоставляет возможность выполнять задачу по таймеру.

## ScheduledExecutorService

`ExecutorService` типа `ScheduledExecutorService` позволяют запускать задачи по расписанию (schedule). Посмотрим на пример:

```java
public static void main(String[] args) {
	ScheduledExecutorService scheduledExecutorService = Executors.newScheduledThreadPool(4);
	Callable<String> task = () -> {
		System.out.println(Thread.currentThread().getName());
		return Thread.currentThread().getName();
	};
	scheduledExecutorService.schedule(task, 1, TimeUnit.MINUTES);
	scheduledExecutorService.shutdown();
}
```

Тут всё просто. Отправляются задачи, получаем "запланированную задачу" `java.util.concurrent.ScheduledFuture`. С расписанием может быть полезен также и следующий случай:

```java
ScheduledExecutorService scheduledExecutorService = Executors.newScheduledThreadPool(4);
Runnable task = () -> {
	System.out.println(Thread.currentThread().getName());
};
scheduledExecutorService.scheduleAtFixedRate(task, 1, 2, TimeUnit.SECONDS);
```

Здесь мы отправляем `Runnable` задачу на выполнение с фиксированной частотой (Fixed Rate) с определённой задержкой. В данном случае, через 1 секунду каждые 2 секунды начать выполнять задачу. Есть похожий вариант:

```java
scheduledExecutorService.scheduleWithFixedDelay(task, 1, 2, TimeUnit.SECONDS);
```

Но здесь задачи выполняются с заданным промежутком МЕЖДУ выполнением разных задач. То есть задача `task` будет выполнена через 1 секунду. Далее, как только она будет завершена, пройдёт 2 секунды, и тогда новая задача task будет запущена. По данной теме можно прочитать следующие материалы:

- [An introduction to thread pools](https://allegro.tech/2015/05/thread-pools.html)
- [Introduction to Thread Pools](https://www.baeldung.com/thread-pool-java-and-guava)
- [Java Multithreading Steeplechase: Cancelling Tasks In Executors](https://10kloc.wordpress.com/2013/12/24/cancelling-tasks-in-executors/)
- [Picking correct Java executors for background tasks](https://zeroturnaround.com/rebellabs/fixedthreadpool-cachedthreadpool-or-forkjoinpool-picking-correct-java-executors-for-background-tasks)

![Thread'ом Java не испортишь: Часть V — Executor, ThreadPool, Fork Join - 4](https://cdn.javarush.com/images/article/d4ef6ba2-470d-4778-a638-56f0df3150eb/800.webp)

_https://dzone.com/articles/diving-into-java-8s-newworkstealingpools_

## WorkStealingPool

Помимо указанных выше пулов потоков, есть ещё один. Можно сказать, что он немного особенный. Имя ему — Work Stealing Pool. Если кратко, то Work Stealing — это такой алгоритм работы, при котором простаивающие потоки начинают забирать задачи других потоков или задачи из общей очереди. Посмотрим на пример:

```java
public static void main(String[] args) {
	Object lock = new Object();
	ExecutorService executorService = Executors.newCachedThreadPool();
	Callable<String> task = () -> {
		System.out.println(Thread.currentThread().getName());
		lock.wait(2000);
		System.out.println("Finished");
		return "result";
	};
	for (int i = 0; i < 5; i++) {
		executorService.submit(task);
	}
	executorService.shutdown();
}
```

Если мы запустим данный код, то `ExecutorService` нам создаст 5 потоков, т.к. каждый поток будет вставать в wait очередь по локу объекта `lock`. Про мониторы и локи по нему мы уже ранее разбирались в "[[Thread'ом Java не испортишь Часть II — синхронизация]]". 

А теперь мы заменим `Executors.newCachedThreadPool` на `Executors.newWorkStealingPool()`. Что поменяется? 

Мы увидим, что наши задачи выполняются не в 5 потоков, а меньше. Помните, что `cachedThreadPool` создавал на каждую задачу свой поток? Потому что `wait` блокировал поток, а следующие задачи хотели выполнятся и в пуле для них создавались новые потоки. В случае со `StealingPool` потоки не будут вечно простаивать в `wait`, они начнут выполнять соседние задачи. 

Чем так отличается от остальных тредпулов этот `WorkStealingPool`? Тем, что внутри него живёт на самом деле волшебный `ForkJoinPool`:

```java
public static ExecutorService newWorkStealingPool() {
        return new ForkJoinPool
            (Runtime.getRuntime().availableProcessors(),
             ForkJoinPool.defaultForkJoinWorkerThreadFactory,
             null, true);
}
```

На самом деле есть ещё одно отличие. Потоки, которые создаются для `ForkJoinPool` по умолчанию являются демон потоками, в отличие от потоков, созданных через обычный `ThreadPool`. Вообще, стоит помнить про демон-потоки, т.к. например при `CompletableFuture` тоже используются демон-потоки, если не указать свою `ThreadFactory`, которая будет создавать не демон-потоки. Вот такие вот сюрпризы могут ждать в неожиданном месте!)

## Fork/Join Pool

В этой части мы поговорим про тот самый `ForkJoinPool` (его ещё называют fork/join framework), который живёт "под капотом" у `WorkStealingPool`. Вообще, Fork Join Framework появился ещё в Java 1.7. И пусть уже Java 11 на дворе, но вспомнить всё равно стоит. Не самая распространённая задача, но довольно интересная. 

На просторах сети есть хороший обзор на эту тему: "[Fork/Join Framework в Java 7](https://habr.com/post/128985/)". 

`Fork/JoinPool` оперирует в своей работе таким понятием как `java.util.concurrent.RecursiveTask`. Также есть аналог — `java.util.concurrent.RecursiveAction`. RecursiveAction не возвращают результат. Таким образом `RecursiveTask` похож на `Callable`, а `RecursiveAction` похож на `Runnable`. 

Ну и смотря на название мы видим два ключевых метода — `fork` и `join`. Метод `fork` запускает асинхронно в отдельном потоке некоторую задачу. А метод `join` позволяет дождаться завершения выполнения работы. 

Существует несколько способов использования: 

![Thread'ом Java не испортишь: Часть V — Executor, ThreadPool, Fork Join - 5](https://cdn.javarush.com/images/article/d3a97020-34fd-44cf-b0a2-bf85b9a18fe5/800.webp)

Данный картинка — это часть слайда доклада Алексея Шипилёва "[Fork/Join: реализация, использование, производительность](https://shipilev.net/talks/j1-April2012-forkjoin.pdf)". 

Чтобы стало понятнее, стоит посмотреть его доклад на JEE CONF: "[Fork Join особенности реализации](https://www.youtube.com/watch?v=_2ciDWeeXJQ)".

## Подведение итогов

Итак, вот мы и закончили очередную часть обзора. Мы разобрались, что сначала придумали `Executor` для выполнения потоков. Потом решили продолжить идею и придумали `ExecutorService`. `ExecutorService` позволяет отправлять задачи на выполнение при помощи `submit` и `invoke`, а также управлять сервисом, выключая его. Т.к. `ExecutorService`'у нужны реализации, написали класс с фабричными методами и назвали его `Executors`. Он позволяет создавать пулы потоков `ThreadPoolExecutor`'ы. При этом существуют такие пулы потоков, которые позволяют ещё и указать расписание для выполнения, а за `WorkStealingPool` прячется `ForkJoinPool`. 

Надеюсь, Вам было не только интересно выше написанное, но и понятно ) Всегда рад предложениям и замечаниям.