# Java API Best Practices

## Introduction

As engineers we write code every day, and it is inconceivable that this code would ever exist in a vacuum, isolated from all other software ever written. Never has the 'standing on the shoulders of giants' metaphor been more apt than it is today in software engineering, with GitHub, Stack Overflow, Maven Central, and all other directories of code, support, and software libraries available at our fingertips.

Software is built from Application Programming Interfaces (APIs) - we make use of the JDK, and numerous dependencies brought in from tools such as Maven or Gradle, on a daily basis. If you were to ask a room full of software engineers if they were API developers, their response is usually that no, they are not. This is incorrect! Anyone who has ever crafted a public class or public method should consider themselves an API developer. The term 'crafting' is used deliberately here. Too often software engineering gets wrapped up in the formality of engineering, but API design is as much, if not moreso, an art form that requires creativity and a gut feeling to be developed over many years.

API design has had numerous books dedicated to it. This refcard, out of necessity, will cover far less than this, but it will hopefully give readers a chance to consider ways in which their software development skills can be improved through considered API design.

## API Characteristics

There are many criteria through which an API can be characterized, six of which are introduced below, and which form the threads that we will cover in more depth throughout this refcard.

* **Understandable:** How often as an engineer have you downloaded a library as a Maven dependency, and then wondered which class gets you started with using the API? An API should not be considered successful if a developer cannot intuitively understand how to use it.
* **Well-documented:** Because we expect others to use our APIs, we should put in the effort to document it. Our focus in this refcard is on high-quality, detailed JavaDoc content.
* **Consistent:** A good API should not surprise its users, and one way we can fail at this is by not being consistent.
* **Fit for purpose:** In developing an API we must ensure that we target it at the right level for the intended user.
* **Restrained:** Creating new API can happen almost too quickly - a few taps on the keyboard - but we should remember that for every commitment, we are committing to a lifetime of support.
* **Evolvable:** For every API decision we make, we are potentially backing ourselves into another corner. As we make API decisions, we must take the time to view them in the wider context of the future releases of the SDK.

These six criteria have proven to be useful when considering if an API is high quality, but there are certain to be many other criteria teams can apply to their process. The main aspiration a team should have after reading this refcard is to define their own criteria through which they can judge and review their API.

## API as a Contract

API has to be thought of as a contract - when we make API available to other developers, we are promising them certain functionality. It is often hard for engineers to leave their code alone, as they imagine new and better approaches (even when not working). Thinking that we should revise our API, to attempt to make it better, is to only be done after much consideration, as by doing this we risk introducing breaking changes and bugs to the developers down stream from us.

Having said this, there is a time when API experimentation is the right thing to do - and that is during the journey from initial idea conception until we reach the 1.0.0 release of our API. Even if we don't need to version our APIs, as they are only to be used by ourselves, we should be cognisant of where this boundary lies, that separates 'experimentation time' from 'business time'.

## The Importance of Justification

The easiest API to maintain is no API at all, and it is therefore extremely important to require justification for every method and class that forms part of our API. During the process of designing our APIs, we should therefore frequently find ourselves asking the following question: 'is this really required?'. We should assure ourselves that the API is paying for itself, returning vital functionality in return for its continued existence.

## API Documentation

There are two kinds of document that are critical to developers when working with an SDK: JavaDoc, and more in-depth articles, such as those Microsoft publishes for [Java on Azure](https://docs.microsoft.com/java/azure/?WT.mc_id=link-refcard-jogiles). Both of these are equally important to developers, but it is important to understand that they serve different purposes. In this refcard we will cover JavaDoc, as it is more relevant to our interests as API developers.

JavaDoc is intended to give a developer an overview of the API. Engineers responsible for writing API should consider it part of their job to ensure that a JavaDoc is complete, with class-level and method-level overviews, specifying the expected inputs, outputs, exceptional circumstances, and any other detail (without going into detail regarding implementation).

In an ideal world, the effort to create high quality JavaDoc would go a step further, to also include code snippets that users can copy/paste into their own software to kick start their own development. These code snippets need not be long screeds of code - it is best if they are constrained to no more than five to ten lines of code. These code snippets can be added to the JavaDoc of the relevant class or method over time, as users start to ask questions on the API.

The value of JavaDoc extends beyond offering it to other developers - it can also help us. This is because JavaDoc gives us a filtered view of our SDK by only showing API that is intended for public use. If we establish a routine of regularly generating JavaDoc we can review our API for issues such as missing JavaDoc, leaking implementation classes or methods, and other things that aren't what we expect.

As most Java projects are based on Maven or Gradle, generating JavaDocs for a project is typically as simple as running `mvn javadoc:javadoc` or `gradle javadoc`, respectively. Getting into the habit of generating this documentation (especially with settings configured to fail on any error or warning) on a regular basis ensures that we are always able to spot API issues early, and remind ourselves of areas of our API that need more JavaDoc content to be written.

### JavaDoc Tags

JavaDoc ships with a [number of tags](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/javadoc.html#CHDJFCCC) such as `@link`, `@param`, and `@return`, which provide more context to the JavaDoc tooling, and which therefore enables a richer experience when HTML output is generated. It is extremely useful when writing JavaDoc content to keep these in the back of your mind, to ensure that they are all used when relevant. To understand when to use each of these tags, refer to the ['Tag Comments` section](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/javadoc.html#CHDJFCCC) of the Java Platform, Standard Edition Tools Reference documentation.

## Consistency

Very rarely these days does software get developed by a single person, and even if it did, the human condition is so fickle that what they deem is great one day may be considered dead wrong the next. Fortunately, as we design API, we have a clear record of the decisions we have made in the form of our public API, and it can be quite easy to spot when something is deviating from this forming convention.

The short term benefit to having consistent API is that we reduce the risk of frustrating our users, and the long term benefit is that a consistent API ensures that when an end user arrives at a new section of your API, that they are more readily able to intuit how it should be used.

Some of the more important considerations around consistency include:

* **Return types:** Ideally all API that must return a collection should be consistent, using only a few classes rather than all possible ones. A good subset of collections to return might be `List`, `Set`, and `Iterator` (and in this case, never `Collection`, `Iterable`, and `Stream`). Similarly, returning `null` should be avoided in a consistent fashion (this is covered further below).
* **Method naming patterns:** Developers rely on their IDE to auto-complete their input, so consider the importance of API naming, to ensure related concepts appear adjacent to each other in the users auto-complete popup lists.
* **Argument order:** APIs that overload methods to accept different numbers or types of arguments should always ensure that the ordering is consistent and logical.
* **Consistent instantiation process:** Using a variation of contructor, factory, singleton, and dependency injection patterns in an API can lead to confusion for the developer. Creating an instantiation plan for an API up-front can help to mitigate this.

## Minimize API

It is the natural instinct of an API developer to want to write as much API as they can - to offer more convenience rather than less - but this leads to two concerns:

1. It can lead to API overload: developers are required to scan through and understand more API than is necessary to complete their job.
2. The more API we expose, the greater the maintenance burden we place on our future selves.

All API developers should start by understanding the critical use cases required for their API, and design their API to support these use cases. They should fight the urge to add more convenience (thinking to themselves that by adding a new method will save developers from writing a few more lines of code).

A related aspect to this is to ensure that implementation classes do not 'leak' out into public API. Appropriate measures should be taken to ensure implementation classes are hidden. There are two main approaches to doing this:

1. Put implementation classes into packages under an `impl` package. All classes under this package can then be excluded from the JavaDoc output, and documented to not be part of the API for developers to use. If this API were being developed under JDK 9 or later, a module could be defined that excludes this impl package from being exported (and therfore, it will not be available to developers at all).
2. Make implementation classes ‘package-private’ (i.e. have no modifier on the class). This means that the classes are not part of the public API, and also are not able to be used by developers.

As part of excluding implementation classes from the public API, it is important that when reviewing the public API (using JavaDocs or otherwise) that developers ensure that no public API exports implementation classes through their method signatures. If this is found to be the case, we are leaking implementation details and the API should be changed to prevent this.

## Minimize Exposing External Dependencies

As mentioned already in the JavaDoc section above, leaking implementation details through our API is a bad thing. Even worse than this is leaking external dependency APIs (that is, libraries that our implementation depends on) out through our API, if that is not intended. Doing this means we've unintentionally increased the surface area of our API to include all the API that the leaked dependency class(es) expose, and we've also made our API beholden to the whims of an API that is outside of our control.

If we discover that we are exposing external dependencies in our API, we should consider if this is a desirable outcome or if we should move to counteract this. The options that exist include removing the offending API and writing a wrapper class around the leaked class.

## Intentional Inheritance

As an API developer, we must strike a balance between offering developers the functionality and flexibility that they need to perform their jobs, and the ability for ourselves to evolve our API over time. One way to ensure we, as API developers, retain some level of control is to make use of the `final` keyword. By making our classes or methods `final`, we are signalling to developers that, at this point in time, they cannot consider extending or overriding these particular classes and methods.

It has to be said that this is not particularly popular for developers, as no one likes being told that they can't do something. Despite this, it is better to err on the side of caution when developing APIs, and if there is not sufficient certainty that the API is right for extension, using the `final` keyword helps to keep some power back for the API developer to improve the situation in the future. The `final` keyword, after all, can always be removed in a subsequent release, but it is not a wise idea to make something `final` after it has already been released.

## Backwards Compatibility

This refcard, up until now, has skirted around the issue of how exactly to evolve an API. The basic advice is that adding new API is generally fine, but removing or changing existing API is not. The reason for this is essentially because adding API is (generally) backwards compatible, whereas removing or changing existing API is backwards incompatible. In other words, when we remove or change existing API, we run the risk of breaking our users when they upgrade to our next release - if they relied on this API that no longer exists in the form their code expects.

Sometimes we must make backwards incompatible changes, for example if we made a mistake in our API design or if we overlooked some aspect of the requirements that requires a different approach. The challenge is to do this in a way that is clearly communicated to our users, whenever possible. Making use of the `@Deprecated` annotation (and related `@deprecated` JavaDoc tag) is a good first step, but this only works when we have a clearly articulated policy regarding when we permit breaking changes in a release. A common approach to this is to subscribe to the [semantic versioning](https://semver.org/) policy of only making incompatible API changes in major releases (that is, in the versioning scheme MAJOR.MINOR.PATCH, where the MAJOR value is incremented). In this approach, anything that you plan to change or remove is marked as deprecated, but not removed or modified until the next major release. If this policy is to be used, it is important to communicate this outwardly, so that users of your API can be certain of this plan.

On the other side of the coin is the case where there is accidental API breaks, introduced by developers who are unaware of the implications of a change they have made. This happens more often than ideal, and is more often than not very hard to notice. Tools exist to watch API changes, and to notify you of backwards incompatibilities that have been introduced. [Revapi](https://revapi.org/) is one such tool that continues to prove its worth to projects inside of Microsoft.

## Don't Return null

Sir Tony Hoare called the invention of the null reference (something he created) his '[billion-dollar mistake](https://en.wikipedia.org/wiki/Tony_Hoare)'. In Java we have become so accustomed to handling some error conditions by returning `null` that it is second nature to null check everything, but in many cases there are better options, rather than returning `null`. Refer to the table below for some common examples:

| Return Type               | Non-null Return Value                                                   |
|---------------------------|-------------------------------------------------------------------------|
| String                    | `""` (An empty string)                                                  |
| List / Set Map / Iterator | Use the `Collections` class, e.g. `Collections.emptyList()`             |
| Stream                    | `Stream.empty()`                                                        |
| Array                     | Return an empty, zero-length array                                      |
| All other types           | Consider using `Optional` (but read the `Optional` section below first) |

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

In Java 8 the `@FunctionalInterface` annotation was introduced, allowing API developers to designate that a particular class is intended for use in lambda expressions. It is not necessary that a class have this annotation, but by having this associated with a class, it enables the compiler to enforce that the class has exactly one abstract method, which is the requirement for supporting lambda expressions.

From a developers point of view this is beneficial as it ensures that a class intended for use in lambda expressions does not accidentally lose that ability with the introduction of additional abstract methods - because the compiler will not allow this situation to arise.

Having said this, developers should have some reticence to creating their own functional interfaces, as recent releases of the JDK ship with 43 functional interfaces baked in, as part of the [java.util.function](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html) package. These can be broken down into six categories, each outlined below:

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

This document has covered an array of considerations that all engineers should have in the back of their mind whenever they are writing code that has any public API. At a high level, readers should put this refcard down with an appreciation for the importance of considered API design. If it wasn't already present, readers should start to sense that there is an art form to API design, and that improving our skills in this area comes about through practice and feedback - from our mentors and from our users. As with many art forms, API design succeeds not by seeing how much can be put in, but by seeing how much can be taken out. The challenge therefore is minimalism, it is consistency, it is intentionalism, and above all, it is consideration - for API, but more importantly, for the end user of the API. We must constantly practice developer empathy to ensure we keep our end users needs sharply in perspective.

Regardless of whether we are writing API for our own consumption, for others in our organization, or more broadly as part of an open source project or commercial library, taking into consideration the values outlined in this refcard will hopefully help to direct readers to a higher quality and more professional outcome. This should not be looked at simply as 'more work', but as a challenge to ourselves, to fixate on the artistic endeavour of making an enjoyable, functional, productive API for our users.