---
layout: post
title: "Mocking repos with Dynamic Proxies"
date: "2023-01-06"
categories: 
  - "coding"
  - "java"
  - "spring"
  - "testing" 
---

Recently I stumbled across an interesting GitHub repository.
It shows a way to mock [Spring Data](https://www.baeldung.com/the-persistence-layer-with-spring-data-jpa) repositories for testing.
The clue is, that the "mocks" are actual in-memory implementations based on Dynamic Proxies.
No [Docker](https://www.docker.com/), no [H2](https://www.h2database.com), no [Mockito](https://site.mockito.org/).

> [https://github.com/mmnaseri/spring-data-mock](https://github.com/mmnaseri/spring-data-mock)

## Spring Data

Spring Data works by implementing repository interface during runtime. 
Here's an example. 
Let's say you have a `User` object which should be saved.
You could write the following repository interface:

```java
public interface UserRepository extends JpaRepository<User, Long> {
    User findByName(String name);
}
```

During runtime, Spring Data would provide an implementation based on this interface.
Every method of the interface follows a certain convention, so Spring Data knows how to implement it.

## Dynamic Proxies

Technically, this is solved by using [Dynamic Proxies](https://www.baeldung.com/java-dynamic-proxies).
Dynamic Proxies let us create a proxy for an interface during runtime.
The proxy will receive every method invocation on the interface and can handle it.
Spring uses it a lot, not only for repositories, but for all kinds of cross-cutting concerns such as transactions or caches.

In [Kotlin](https://kotlinlang.org/), this could look like this:

```kotlin
val proxy = Proxy.newProxyInstance(
    this::class.java.classLoader,
    arrayOf<Class<*>>(UserRepository::class.java),
    MyProxyClass()
) as UserRepository

val user = proxy.findByName("Thomas")

class MyProxyClass : InvocationHandler {
    override operator fun invoke(proxy: Any?, method: Method, args: Array<Any?>?): Any? {
        if (method.name == "findByName") {
            // ...
        } else {
            // ...
        }
    }
}
```

## How we usually test

Let's consider the following example:

```kotlin
class UserServiceTest {

    @Test
    fun `should create invoice for user`() {

        doReturn(user).whenever(userRepo).findByName("Thomas")
        doReturn(order).whenever(orderRepo).find("O-202201-0002")
        
        val invoice = userService.createInvoice("Thomas", "O-202201-0002")
        
        assertThat(invoice).isEqualTo(/*...*/)
    }
}
```

This could be a typical test case for a `UserService`.
We need to mock the behaviour of two repositories used by the `UserService`:

- `doReturn(user).whenever(userRepo).findByName("Thomas")`
- `doReturn(order).whenever(orderRepo).find("O-202201-0002")` 

Another way would be using Docker containers or an in-memory database (like [H2](https://www.h2database.com)).
But every solution has its drawbacks:

- Mocking is time-consuming and requires deep white-box-knowledge of the code under test.
- Docker or in-memory databases are slow and require additional setup. 

However, we could also go into another direction and provide some in-memory implementations by our own.

```kotlin
class TestInMemoryUserRepository: UserRepository {
    private val users = mutableListOf<User>()
    override fun findByName(name: String): User? {
        return users.find { it.name == name }
    }
}
```

Instead of mocking or using Docker, we have a super simple "in-memory" implementation which will just act as the normal repository.

But as always, there's also a drawback to this solution:
we probably need to implement this kind of class over and over again.

Imagine an example like this:

```kotlin
interface UserRepository {
    fun find(id: String): User?
    fun save(user: User)
}

interface OrderRepository {
    fun find(orderNumber: String): Order?
    fun save(order: Order)
    fun delete(orderNumber: String)
}

interface AddressRepository {
    fun find(id: String): Address?
    fun save(address: Address)
    fun findAll(userId: String): List<Address>
}

interface InvoiceRepository {
    // ...
}
```

## Using Dynamic Proxies for testing

However, we can use Dynamic Proxies to make this a bit more pleasuring.
If you are already working with Spring Data, [@mmnaseri](https://twitter.com/mmnaseri)'s [GitHub library](https://github.com/mmnaseri/spring-data-mock) gives you a quick start:
Using his implementation, mocking a repository is really simple:

```java
UserRepository repository = RepositoryFactoryBuilder.builder().mock(UserRepository.class);
repository.save(new User());
```

But what if you don't use Spring Data?
Take a look at the interfaces from the example above - they don't use Spring Data!
In fact, nearly all the software I developed during the last years, does not use Spring Data.
Instead, we use [DynamoDB](https://aws.amazon.com/dynamodb/) (on AWS) with its SDK directly.

In such a case, we can write a Dynamic Proxy on our own.
The most complicated part is the `InvocationHandler`.
You can find an example below. 

Note that the most important thing is, that your interfaces stick to a naming-convention.
If your method is sometimes called `find(...)`, sometimes `findById(...)` and then `get(...)` or `load(...)` again, you will have a hard time to write a generic Dynamic Proxy.
But if you stick to a naming-convention, you can re-use the Dynamic Proxy for different interfaces.

```kotlin
class MyGenericRepositoryProxy : InvocationHandler {
    private val store = mutableListOf<Any>()
    override operator fun invoke(proxy: Any?, method: Method, args: Array<Any?>?): Any? {
        when (method.name) {
            "findByName" -> {
                val nameToFind = args?.first()!!
                return store
                    .find { obj ->
                        obj::class.declaredMemberProperties.any {
                            it.name == "name" && it.getter.call(obj).toString() == nameToFind
                        }
                    }
            }
            "findAll" -> {
                return store
            }
            "save" -> {
                val objToSave = args?.first()!!
                store.add(objToSave)
                return null
            }
            else -> {
                // ...
            }
        }
    }
}
```

We can wrap the creation of the Dynamic Proxy in a nice factory method:

```kotlin
object RepoMockFactory {
    fun <T> mock(javaClass: Class<T>): T {
        return Proxy.newProxyInstance(
            this::class.java.classLoader,
            arrayOf<Class<*>>(javaClass),
            MyGenericRepositoryProxy()
        ) as T
    }
}
```

## Using our mocks

After we created our Dynamic Proxy, we can use it in our tests.
No Docker containers, no H2-database and no `doReturn(xy).whenever(xy).xy(...)`.
We can just use our mocks like real implementations.
This makes the test clean and easy to read.

```kotlin
class UserServiceTest {

    @Test
    fun `should create invoice for user`() {

        val mockedUserRepo = RepoMockFactory.mock(UserRepository::class.java)
        val mockedOrderRepo = RepoMockFactory.mock(OrderRepository::class.java)
        
        mockedUserRepo.save(User("Thomas"))
        mockedOrderRepo.save(Order("O-202201-0002"))
        
        val userService = UserService(mockedUserRepo, mockedOrderRepo)
        val invoice = userService.createInvoice("Thomas", "O-202201-0002")
        
        assertThat(invoice).isEqualTo(/*...*/)
    }
}
```

## More

- [Dynamic Proxies in Java](https://www.baeldung.com/java-dynamic-proxies)
- [Java Dynamic proxy mechanism and how Spring is using it](https://medium.com/@spac.valentin/java-dynamic-proxy-mechanism-and-how-spring-is-using-it-93756fc707d5) 
- [Spring Data Mock](https://github.com/mmnaseri/spring-data-mock) on GitHub 
- [Dynamische Proxys mit dem JDK umsetzen](https://www.innoq.com/de/articles/2020/03/java-dynamic-proxy/) (German)

**Best regards,** Thomas.
