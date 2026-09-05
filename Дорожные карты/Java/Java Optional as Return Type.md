## 1. Introduction[](https://www.baeldung.com/java-optional-return#introduction)

The _[Optional](https://www.baeldung.com/java-optional)_ type was introduced in Java 8.  It provides a clear and explicit way to convey the message that there may not be a value, without using _null_.

When getting an _Optional_ return type, we’re likely to check if the value is missing, leading to fewer _NullPointerException_s in the applications. However, the _Optional_ type isn’t suitable in all places.

Although we can use it wherever we see fit, in this tutorial, we’ll focus on some best practices of using _Optional_ as a return type.

## 2. _Optional_ as Return Type[](https://www.baeldung.com/java-optional-return#optional-as-return-type)

An _Optional_ type can be a return type for most methods except some scenarios discussed later in the tutorial.

Most of the time, returning an _Optional_ is just fine:

```java
public static Optional<User> findUserByName(String name) {
    User user = usersByName.get(name);
    Optional<User> opt = Optional.ofNullable(user);
    return opt;
}
```

This is handy since we can use the _Optional_ API in the calling method:

```java
public static void changeUserName(String oldFirstName, String newFirstName) {
    findUserByFirstName(oldFirstName).ifPresent(user -> user.setFirstName(newFirstName));
}
```

It’s also appropriate for a static method or utility method to return an _Optional_ value.  However, there are many situations where we should not return _an Optional_ type.

## 3. When to Not Return _Optional_[](https://www.baeldung.com/java-optional-return#when-to-not-return-optional)

Because _Optional_ is a wrapper and [value-based](https://docs.oracle.com/javase%2F9%2Fdocs%2Fapi%2F%2F/java/lang/doc-files/ValueBased.html) class, there are some operations that can’t be done against _Optional_ object. Many times, it’s just simply better to return the actual type rather than an _Optional_ type.

Generally speaking, for getters in POJOs, it’s more suitable to return the actual type, not an _Optional_ type. Particularly, it’s important for Entity Beans, Data Models, and DTOs to have traditional getters.

We’ll examine some of the important use cases below.

### 3.1. Serialization[](https://www.baeldung.com/java-optional-return#1-serialization)

Let’s imagine we have a simple entity:

```java
public class Sock implements Serializable {
    Integer size;
    Optional<Sock> pair;

    // ... getters and setters
}
```

**This actually won’t work at all.** If we were to try and serialize this, we’d get an _NotSerializableException_:

```java
new ObjectOutputStream(new ByteArrayOutputStream()).writeObject(new Sock());
```

And really, while [serializing _Optional_ may work with other libraries](https://www.baeldung.com/jackson-optional), **it certainly adds what may be unnecessary complexity.**

Let’s take a look at another application of this same serialization mismatch, this time with JSON.

### 3.2. JSON[](https://www.baeldung.com/java-optional-return#2-json)

Modern applications convert Java objects to JSON all the time. If a getter returns an _Optional_ type, we’ll most likely see some unexpected data structure in the final JSON.

Let’s say we have a bean with an optional property:

```java
private String firstName;

public Optional<String> getFirstName() {
    return Optional.ofNullable(firstName);
}

public void setFirstName(String firstName) {
    this.firstName = firstName;
}
```

So, if we use Jackson to serialize an instance of _Optional_, we’ll get:

```plaintext
{"firstName":{"present":true}}
```

But, what we’d really want is:

```plaintext
{"firstName":"Baeldung"}
```

So, _Optional_ is a pain for serialization use cases. Next, let’s look at the cousin to serialization: **writing data to a database.**

### 3.3. JPA[](https://www.baeldung.com/java-optional-return#3-jpa)

In JPA, the getter, setter, and field should have name as well as type agreement. For example, a _firstName_ field of type _String_ should be paired with a getter called _getFirstName_ that also returns a _String._

Following this convention makes several things simpler, including the use of reflection by libraries like Hibernate, to give us great Object-Relational mapping support.

Let’s take a look at our same use case of **an optional first name in a POJO.**

This time, though, it’ll be a JPA entity:

```java
@Entity
public class UserOptionalField implements Serializable {
    @Id
    private long userId;

    private Optional<String> firstName;

    // ... getters and setters
}
```

And let’s go ahead and try to persist it:

```java
UserOptionalField user = new UserOptionalField();
user.setUserId(1l);
user.setFirstName(Optional.of("Baeldung"));
entityManager.persist(user);
```

Sadly, we run into an error:

```plaintext
Caused by: jakarta.persistence.PersistenceException: [PersistenceUnit: com.baeldung.optionalReturnType] Unable to build Hibernate SessionFactory
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.persistenceException(EntityManagerFactoryBuilderImpl.java:1015)
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.build(EntityManagerFactoryBuilderImpl.java:941)
	at org.hibernate.jpa.HibernatePersistenceProvider.createEntityManagerFactory(HibernatePersistenceProvider.java:56)
	at jakarta.persistence.Persistence.createEntityManagerFactory(Persistence.java:79)
	at jakarta.persistence.Persistence.createEntityManagerFactory(Persistence.java:54)
	at com.baeldung.optionalReturnType.PersistOptionalTypeExample.<clinit>(PersistOptionalTypeExample.java:11)
Caused by: org.hibernate.MappingException: Could not determine type for: java.util.Optional, at table: UserOptionalField, for columns: [org.hibernate.mapping.Column(firstName)]
```

We could try **deviating from this standard.** For example, we could keep the property as a _String_, but change the getter:

```java
@Column(nullable = true) 
private String firstName; 

public Optional<String> getFirstName() { 
    return Optional.ofNullable(firstName); 
}
```

It appears that we could have both ways: have an _Optional_ return type for the getter and a persistable field _firstName_.

However, now that we are inconsistent with our getter, setter, and field, it’ll be more difficult to leverage JPA defaults and IDE source code tools.

Until JPA has elegant support of _Optional_ type, we should stick to the traditional code. It’s simpler and better:

```java
private String firstName;

// ... traditional getter and setter
```

Let’s finally take a look at how this affects the front end – check to see if the problem we run into sounds familiar.

### 3.4. Expression Languages[](https://www.baeldung.com/java-optional-return#4-expression-languages)

Preparing a DTO for the front-end presents similar difficulties.

For example, let’s imagine that we are using JSP templating to read our _UserOptional_ DTO’s _firstName_ from the request:

```java
<c:out value="${requestScope.user.firstName}" />
```

Since it’s an _Optional_, we’ll not see “_Baeldung_“. Instead, we’ll see the _String_ representation of the _Optional_ type:

```plaintext
Optional[Baeldung]
```

And this isn’t a problem just with JSP. Any templating language, be it Velocity, Freemarker, or something else, will need to add support for this. Until then, let’s continue to keep our DTOs simple.

## 4. Conclusion[](https://www.baeldung.com/java-optional-return#conclusion)

In this tutorial, we’ve learned how we can return an _Optional_ object, and how to deal with this kind of return value.

On the other hand, we’ve also learned that there are many scenarios that we would be better off to not use _Optional_ return type for a getter. While we can use _Optional_ type as a hint that there might be no non-null value, we should be careful not to overuse the _Optional_ return type, particularly in a getter of an entity bean or a DTO.