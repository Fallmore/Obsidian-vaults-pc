Этот материал — часть цикла “Введение в Enterprise-разработку”. Предыдущие статьи:

- [о сети](https://javarush.com/groups/posts/2514-vvedenie-v-enterprise-razrabotku);
- [об архитектуре ПО](https://javarush.com/groups/posts/2519-chastjh-2-pogovorim-nemnogo-ob-arkhitekture-po);
- [о протоколах HTTP/HTTPS](https://javarush.com/groups/posts/2521-chastjh-3-protokolih-httphttps).

Maven — инструмент для управления и сборки проектов — настоящий помощник Java-программиста.![Часть 4. Основы Maven - 1](https://cdn.javarush.com/images/article/2e744a00-6e59-4eef-bc0f-9dd4f6f28d2e/800.webp)

Он облегчает жизнь девелоперу на всех стадиях работы: от создания структуры проекта и подключения необходимых библиотек до развертывания продукта на сервере. При работе с любым фреймворком придется использовать Maven. Поэтому давай сегодня разберем его основные функции и посмотрим, как их нужно использовать.

## Пошаговая установка Maven

1. Для начала нужно установить Maven, скачав его по [этой ссылке](https://maven.apache.org/download.cgi).
    
2. Далее необходимо распаковать скачанный архив и установить переменной окружения M2_HOME ссылку на местонахождение распакованного архива. Например, C:\Program Files\maven\
    
3. Чтобы проверить, что все установилось, в командной строке пропиши:
    
    _mvn -version_
    
4. Если отобразится информация о версии Maven, Java и т.д., все готово к работе.
    
5. Теперь открой IntelliJIDEA и создай новый проект. В первом же окне выбери Maven:
    
    ![Часть 4. Основы Maven - 2](https://cdn.javarush.com/images/article/c8d42550-6e0a-47fc-b8ea-da0a4ef4098f/800.webp)
6. Нажми Next и заполни следующее диалоговое окно:
    
    ![Часть 4. Основы Maven - 3](https://cdn.javarush.com/images/article/211509da-b9e2-44a8-9225-0e12738e69e8/800.webp)
7. Далее стандартно создай проект в необходимом месте.
    
    После того, как проект создался, обрати внимание на его структуру:
    
    ![Часть 4. Основы Maven - 4](https://cdn.javarush.com/images/article/314e7ce9-a88e-4633-9bbe-cff5718b8467/256.webp)

Это cтандартная структура для Maven-проекта:

- в папке _src/main/java_ содержатся java-классы;
- в _src/main/resources_ — ресурсы, которые использует наше приложение (HTML-страницы, картинки, таблицы стилей и тд);
- _src/test_ — для тестов.

Еще обрати внимание на файл под название pom.xml. Это и есть главный файл для управления Мавеном. Все описание проекта содержится здесь. Пока там не слишком много информации, но сейчас мы будем ее добавлять.

## Управление зависимостями в Maven

Возможно, ты встречал словосочетание “менеджер зависимостей” или “dependency manager”. Мавен все это умеет. Благодаря Мавену тебе не нужно тратить кучу времени на поиски нужной библиотеки в интернете, качать ее, а потом подключать к проекту: достаточно добавить необходимую в список зависимостей Мавена. Зависимости записываются в XML-ноду dependencies Скажем, тебе в проекте нужна библиотека Apache Commons IO для упрощенной работы с файлами. Чтобы добавить библиотеку, напиши пять строк в pom.xml:

```html
<dependency>
   <groupId>commons-io</groupId>
   <artifactId>commons-io</artifactId>
   <version>2.6</version>
</dependency>
```

Теперь твой pom.xml должен выглядеть так:

```html
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>

   <groupId>example.com</groupId>
   <artifactId>example</artifactId>
   <version>1.0-SNAPSHOT</version>

   <dependencies>
       <dependency>
           <groupId>commons-io</groupId>
           <artifactId>commons-io</artifactId>
           <version>2.6</version>
       </dependency>
   </dependencies>
</project>
```

После этого разреши IntelliJ IDEA импортировать зависимость (диалоговое окно должно появиться в правом нижнем углу). Теперь библиотека готова к использованию:

```java
import org.apache.commons.io.FileUtils;

import java.io.File;

public class TestMaven {
   public static void main(String[] args) {
       File tempDirectory = FileUtils.getTempDirectory();
   }
}
```

Все последующие зависимости тоже должны записываться внутри тега \<dependencies>. У тебя может возникнуть вопрос: как узнать информацию, которую нужно указывать о библиотеке внутри тега \<dependency>? Тут все просто. В таких случаях всегда задаются три параметра: groupId, artifactId и version. Узнать эти параметры можно двумя способами:

1. На сайте библиотеки. Если нам нужен Apache Commons IO, переходим на официальный сайт, выбираем вкладку Dependency Information. [Здесь](https://commons.apache.org/proper/commons-io/dependency-info.html) есть вся необходимая информация — ее можно просто скопировать и добавить в наш раздел dependencies.
    
2. В [Maven-репозитории](https://mvnrepository.com/). В поиске введи “apache commons io”, и ты увидишь все доступные версии библиотеки. После выбора нужной, просто скопируй:
    
    ```html
    <dependency>
               <groupId>commons-io</groupId>
               <artifactId>commons-io</artifactId>
               <version>2.6</version>
           </dependency>
    ```
    
    и добавь в свой pom.xml.
    

## Виды Maven-репозиториев

Стоит еще раз упомянуть Мавен-репозиторий, потому что их на самом деле у нас два — [внешний](https://mvnrepository.com/) (глобальный) и локальный, у тебя на компьютере. Все библиотеки, которые ты добавляешь в свои проекты, сохраняются в локальном репозитории. Когда Maven добавляет необходимую зависимость в проект, он сначала проверяет локальный репозиторий на наличие такой библиотеки, и только если не находит ее там — обращается к внешнему. Как видишь, можно использовать Мавен для добавления зависимостей, но это не все, что он умеет делать.

## Сборка Java-проекта с помощью Maven

Возможно, новичку эта возможность покажется бессмысленной. Зачем это нужно, если есть IDE? Но нет! Во-первых, на сервере, на котором придется собирать приложение, может не быть не то что среды разработки, но и графического интерфейса. Во-вторых, на больших проектах Мавен лучше справляется с задачами по сборке проекта. Поэтому не будем томить себя ожиданием, а рассмотрим процесс сборки приложения с использованием Maven.

### Фазы

Процесс построения приложения называют жизненным циклом Maven-проекта, и состоит он из фаз (phase). Посмотреть на них ты можешь в IDEA, нажав на Maven>example>Lifecycle в правом верхнем углу:
![Часть 4. Основы Maven - 5](https://cdn.javarush.com/images/article/c9ae0e2d-85bf-4af2-8136-529635e1542e/256.webp)
Как видишь, существует 9 фаз:

1. clean — удаляются все скомпилированные файлы из каталога target (место, в котором сохраняются готовые артефакты);
2. validate — идет проверка, вся ли информация доступна для сборки проекта;
3. compile — компилируются файлы с исходным кодом;
4. test — запускаются тесты;
5. package — упаковываются скомпилированные файлы (в jar, war и т.д. архив);
6. verify — выполняются проверки для подтверждения готовности упакованного файла;
7. install — пакет помещается в локальный репозиторий. Теперь он может использоваться другими проектами как внешняя библиотека;
8. site — создается документация проекта;
9. deploy — собранный архив копируется в удаленный репозиторий.

Все фазы выполняются последовательно: нельзя запустить, скажем, четвертую фазы, пока не завершены фазы 1-3. Выполнить запуск фазы можно двумя способами:

- через командную строку:
    
    _mvn package_ <br/>
    
    ![Часть 4. Основы Maven - 6](https://cdn.javarush.com/images/article/3b91f9a6-46bd-4398-b4ab-209a529c12fa/256.webp)
- используя Intellij IDEA:<br/>
    
    ![Часть 4. Основы Maven - 7](https://cdn.javarush.com/images/article/a0b55764-2fdf-43c0-b27c-ba47085c12a9/256.webp)<br/>
    
    До запуска package выполняются фазы validate, compile, test. Исключение — фаза clean. Ее желательно вызывать перед каждой новой сборкой проекта. Фазы можно перечислить через пробел:
    
    _mvn clean package._
    

Также у каждой фазы есть пре- и пост-фазы: например, pre-deploy, post-deploy, pre-clean, post-clean, но используются они довольно редко. Кроме этого, у каждой фазы есть цели (goal). Стандартные цели заложены по умолчанию, дополнительные добавляются Maven-плагинами. Иногда во время какой-то фазы нужно выполнить дополнительный функционал. Для этого существуют Maven-плагины. Список официальных плагинов можно посмотреть [на сайте Мавена](https://maven.apache.org/plugins/). Но знай, что еще есть много пользовательских плагинов, которые можно найти на сторонних ресурсах. Ну и естественно, при какой-то экзотической необходимости, у тебя всегда остается возможность написать такой плагин самому.

### Плагины

Чтобы добавить Мавен-плагин в проект, его описание, аналогично зависимостям, нужно поместить в pom.xml в тег <build> и <plugins>. Например, нам нужен плагин для проверки того, что все наши внешние библиотеки используют самые последнии версии. Немного поискав интернете, можно найти [этот плагин](https://github.com/khmarbaise/uptodate-maven-plugin) с инструкцией использования. Установим его groupId, artifactId и version. Пропишем, какие цели он должен выполнить и на какой фазе. В нашем случае проверка зависимостей в текущем pom.xml установлена на фазе validate. Теперь наш “помник” выглядит так:

```html
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>

   <groupId>example.com</groupId>
   <artifactId>example</artifactId>
   <version>1.0-SNAPSHOT</version>

   <build>
       <plugins>
           <plugin>
               <groupId>com.soebes.maven.plugins</groupId>
               <artifactId>uptodate-maven-plugin</artifactId>
               <version>0.2.0</version>
               <executions>
                   <execution>
                       <goals>
                           <goal>dependency</goal>
                       </goals>
                       <phase>validate</phase>
                   </execution>
               </executions>
           </plugin>
       </plugins>
   </build>

   <dependencies>
       <dependency>
           <groupId>commons-io</groupId>
           <artifactId>commons-io</artifactId>
           <version>2.6</version>
       </dependency>
   </dependencies>
</project>
```

Мы все так же можем продолжать работу над нашим проектом. Но давай попробуем поменять версию Apache Commons IO на 2.0 и запустить сборку проекта. 
Получим:
`[ERROR] Failed to execute goal com.soebes.maven.plugins:uptodate-maven-plugin:0.2.0:dependency (default) on project example: There is a more up-to-date version ( 2.6 ) of the dependency commons-io:commons-io:2.0 available. -> [Help 1]` 

Здесь у нас ошибка сборки, вызванная плагином. В сообщении к ошибке указано, что мы используем 2.0 версию при наличии 2.6. 

Вообще, Мавен очень полезный инструмент. Возможно, поначалу он покажется трудным в использовании, но практикуйся, создавай свои проекты под управлением Мавена, и через некоторое время ты будешь очень доволен результатом. 

В этой статье нарочно упущено много деталей о Мавене — мы сконцентрировались на самом необходимом. Но совершенству нет предела: почитать больше о Maven можно [на официальном сайте продукта](https://maven.apache.org/).