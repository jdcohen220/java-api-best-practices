# Java API Best Practices

## Introduction

As engineers we write code every day, and it is inconceivable that this code would ever exist in a vacuum, isolated from all other software ever written. Never has the 'standing on the shoulders of giants' metaphor been more apt than it is today in software engineering, with GitHub, Stack Overflow, Maven Central, and all other directories of code, support, and software libraries available at our fingertips.

Software is built from APIs - we make use of the JDK, and numerous dependencies brought in from tools such as Maven or Gradle, on a daily basis. But this ignores my central thesis: that we are all API designers, and we are all therefore responsible for crafting something for others to use. The term 'crafting' is used deliberately here. Too often software engineering gets wrapped up in the formality of engineering, but API design is as much, if not moreso, an art form that requires creativity and a gut feeling to be developed over many years.

API design has had numerous books dedicated to it. This refcard will necessarily cover far less than this, but it will hopefully give readers a chance to consider ways in which their software development can be improved. Thanks to Microsoft for funding the creation of this refcard.

## API Characteristics

There are many criteria through which an API can be characterized, six of which are introduced below:

* **Understandable:** How often as an engineer have you download a library as a Maven dependency, and then wondered which class gets you started with using the API? An API should not be considered successful if a developer cannot intuitively understand how to use it.
* **Well-documented:** Because we expect others to use our APIs, we should put in the effort to document it. Our focus here is on good-quality and detailed JavaDoc content.
* **Consistent:** A good API should not surprise its users, and one way we can fail at this is by not being consistent.
* **Fit for purpose:** In developing an API we must ensure that we target it at the right level.
* **Restrained:** Writing API is very cheap, but we should not mistake the cost of writing API as the full cost over its lifetime.
* **Evolvable:** For every API decision we make, we are potentially backing ourselves into another corner. As we make API decisions, we must take the time to view them in the wider context of the future releases of the SDK.

These six criteria have proven to be useful when considering if an API is high quality, but there are certain to be many other criteria teams can apply to their process. The main aspiration a team should have after reading this refcard is to define their own criteria through which they can judge and review their API.

## API as a Contract

API has to be thought of as a contract - when we make available API to other developers, we are promising them certain functionality. It is often hard for engineers to leave their code alone, as they imagine new and better approaches (even when not working). Thinking that we should revise our API, to attempt to make it better, is to only be done after much consideration, as by doing this we risk introducing breaking changes and bugs to the developers down stream from us.

Having said this, there is a time when API experimentation is the right thing to do - and that it during the journey from initial idea conception until we reach the 1.0.0 release of our API. Even if we don't need to version our APIs, as they are only to be used by ourselves, we should be cognisant of where this boundary lies, that separates 'experimentation time' from 'business time'.

> TODO Cover topics like justifying everything, "why do you need this?", the cost of API (maintenance, etc)

## Consistency

> TODO

## API Documentation

There are two kinds of document that are critical to developers, JavaDoc and more in-depth articles. Both of these are equally important to developers, but it is important to understand that they serve different purposes. In this refcard we will cover JavaDoc, as it is more relevant to our interests as API designers.

JavaDoc is intended to give a developer an overview of the API - it can be considered the 'what' in terms of the contract offered by the API. Engineers responsible for writing API should consider it part of their job to ensure that a JavaDoc is complete, with class-level and method-level overviews, specifying the expected inputs, outputs, exceptional circumstances, and any other detail (without going into detail regarding implementation).

The value of JavaDoc extends beyond offering it to other developers - it can also help us. This is because JavaDoc gives us a filtered view of our SDK by only showing API that is intended for public use. If we establish a routine of regularly generating JavaDoc we can review our API for issues such as missing JavaDoc, leaking implementation classes or methods, and other things that aren't what we expect.

As most Java projects are based on Maven or Gradle, generating JavaDocs for a project is typically as simple as running `mvn javadoc:javadoc` or `gradle javadoc`, respectively.

### JavaDoc Tags

JavaDoc ships with a [number of tags](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/javadoc.html#CHDJFCCC) such as `@link`, `@param`, and `@return`, which provide more context to the JavaDoc tooling, and which therefore enables a richer experience when HTML output is generated. It is extremely useful when writing JavaDoc content to keep these in the back of your mind, to ensure that they are all used. To understand when to use each of these tags, refer to the ['Tag Comments` section](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/javadoc.html#CHDJFCCC).

## Minimize API

It is the natural instinct of an API designer to want to write as much API as they can - to offer more convenience rather than less convenience - but this leads to two concerns:

1. It can lead to API overload: developers are required to scan through and understand more API than is necessary to complete their job.
2. The more API we expose, the greater the maintenance burden we place on our future selves.

All API designers should start by understanding the critical use cases required for their API, and design their API to support these use cases. They should fight the urge to add more convenience (thinking to themselves that by adding a new method will save developers from writing a few more lines of code).

A related aspect to this is to ensure that implementation classes do not 'leak' out into public API. Appropriate measures should be taken to ensure implementation classes are hidden. There are two main approaches to doing this:

1. Put implementation classes into packages under an `impl` package. All classes under this package can then be excluded from the JavaDoc output, and documented to not be part of the API for developers to use. If this API were being developed under JDK 9 or later, a module could be defined that excludes this impl package from being exported (and therfore, it will not be available to developers at all).
2. Make implementation classes ‘package-private’ (i.e. have no modifier on the class). This means that the classes are not part of the public API, and also are not able to be used by developers.

As part of excluding implementation classes from the public API, it is important that when reviewing the public API (using JavaDocs or otherwise) that developers ensure that no public API exports implementation classes through their method signatures. If this is found to be the case, we are leaking implementation details and the API should be changed to prevent this.

## Intentional Inheritance

> TODO

## Minimize Exposing External Dependencies

> TODO

## Don't Return null

Sir Tony Hoare called the invention of the null reference (something he created) his '[billion-dollar mistake](https://en.wikipedia.org/wiki/Tony_Hoare)'. In Java we have become so accustomed to handling error conditions by returning `null` that it is second nature to null check everything, but in many cases there are better options. Refer to the table below for some common examples:

| Return Type               | Non-null Return Value                                                             |
|---------------------------|-----------------------------------------------------------------------------------|
| String                    | `""` (An empty string)                                                            |
| List / Set Map / Iterator | Use the `Collections` class, e.g. `Collections.emptyList()`                       |
| Stream                    | `Stream.empty()`                                                                  |
| Array                     | Return an empty, zero-length array                                                |
| All other types           | Consider using `Optional` (but read the `Optional` section later in this refcard) |

In guaranteeing to return non-null values to callers of our API, our users can opt to not include the noisiness of the null check in their code base. It is important however, that should this approach be taken, that we ensure that it is applied consistently across an entire API. It is very easy to erode trust in an API if it fails to consistently apply patterns (and in failing to do so, causes the user to encounter unexpected null pointer exceptions).

## Understand when to use Optional

Java 8 introduced `Optional` as a way of lessening the possibility of null pointer exceptions, because when a method returns `Optional`, it guarantees that it will be non-null. It is then up to the consumer of the API to determine if the returned `Optional` contains an element, or is empty. In other words, an `Optional<T>` can be thought of as a container for at most one element.

The `Optional` return type is best used in select cases when:

1. A result might not be able to be returned, and
2. The API consumer has to perform some different action in this case

Optional provides a number of convenience methods, some of which are highlighted below, in a hypothetical situation where there exists a `public Optional<Car> getFastest(List<Car> cars)` method:

```java
// getFastest returns Optional<Car>, but if the cars list is empty, it
// returns Optional.empty(). In this case, we can choose to map this to an
// invalid value.
Car fastestCar = getFastest(cars).orElse(Car.INVALID);

// If the orElse case is expensive to calculate, we can also use a Supplier
// to only generate the alternate value if the Optional is empty
Car fastestCar = getFastest(cars).orElseGet(() -> searchTheWeb());

// We could alternatively throw an exception
Car fastestCar = getFastest(cars).orElseThrow(MissingCarsException::new);

// We can also provide a lambda expression to operate on the value, if it
// is not empty
getFastest(cars).ifPresent(this::raceCar)
```

The examples above all demonstrate good times for an API to return `Optional`. On the other hand, if most users of an API that returns `Optional` write code as shown below, it is possible to argue that this is an object-oriented version of a null-reference check, and is probably no better (or any more intuitive) than simply declaring that this particular API will return `null` rather than `Optional`.

```java
// Whilst it is ok to call get() directly on an Optional, you risk a
// NoSuchElementException if it is empty. You can wrap it with an
// isPresent() call as shown below, but if your API is commonly used like
// this, it suggests that Optional might not be the right return type
Optional<Car> result = getFastest(cars);
if (result.isPresent()) {
  result.get().startCarRace();
}
```

There are two final rules when it comes to returning an `Optional` in API:

1. Never return `Optional<Collection<T>>` from a method, as this can be more succinctly represented by simply returning `Collection<T>` with an empty collection (as mentioned earlier in this refcard).
2. Never, ever return `null` from a method that has a return type of `Optional`!

## Static Factories

Static factories offer three benefits over constructors:

1. Ability to be named (i.e. constructors must be the class name)
2. They do not require a new instance to be created
3. Ability to return subclasses

> TODO Code samples

## Beware of Boxing

Boxing / Unboxing is when Java converts primitives to and from reference types, for example, converting between `int` and `Integer` types. This is often done automatically through the process known as autoboxing. There are three concerns when using reference types:

1. Increased possibility of null pointer exceptions (compared with no possibility with primitives).
2. Correctness (e.g. the equality operator `==` works differently for `int` and `Integer`).
3. Performance considerations.

As noted in the [Java Programming Language](https://docs.oracle.com/javase/1.5.0/docs/guide/language/autoboxing.html
) documentation, the following is stated:

>It is not appropriate to use autoboxing and unboxing for scientific computing, or other performance-sensitive numerical code. An Integer is not a substitute for an int; autoboxing and unboxing blur the distinction between primitive types and reference types, but they do not eliminate it.

For these reasons, when we write API it is important that we consider the necessity of any API that returns or accepts reference types, as opposed to primitive types. Whenever possible, reference types should be replaced with primitive types.

## Functional Interfaces

In Java 8 the `@FunctionalInterface` annotation was introduced, allowing API designers to designate that a particular class is intended for use in lambda expressions. It is not necessary that a class have this annotation, but by having this associated with a class, it enables the compiler to enforce that the class has exactly one abstract method, which is the requirement for supporting lambda expressions.

From a developers point of view this is beneficial as it ensures that a class intended for use in lambda expressions does not accidentally lose that ability with the introduction of additional abstract methods - because the compiler will not allow this situation to arise.

Having said this, developers should have some reticence to creating their own functional interfaces, as recent releases of the JDK ship with 43 functional interfaces baked in. These can be broken down into six categories, each outlined below:

| Interface           | Signature             |
|---------------------|-----------------------|
| `UnaryOperator<T>`  | `T apply(T t)`        |
| `BinaryOperator<T>` | `T apply(T t1, T t2)` |
| `Predicate<T>`      | `boolean test(T t)`   |
| `Function<T,R>`     | `R apply(T t)`        |
| `Supplier<T>`       | `T get()`             |
| `Consumer<T>`       | `void accept(T t)`    |

Developers can choose to use these functional interfaces in lieu of creating their own, but there are still valid reasons for creating a custom functional interface. Examples include wanting a more descriptive name for the interface (for example, `Comparator` is a very expressive name for the function it serves), and wanting to introduce additional functionality (default methods in interfaces, or non-abstract methods in classes).


## Conclusion