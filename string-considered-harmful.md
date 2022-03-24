---
marp: true
theme: uncover
class: invert
paginate: true

---

# String Considered Harmful

</br>

### Boosterconf 2022
### Morten Nygaard Åsnes


</br>

[@mortenaa](twitter.com/mortenaa)
morten.nygaard.aasnes@dfind.no
<!--
Hello everybody, I'm Morten, and I work as a consultant for Dfind Consulting here in Bergen. I mostly work with Java and Kotlin, and so the few code examples I'm gonna show wil be in Kotlin.
I´m going to talk about how we use types in our code. Not large classes or complex types we design,
but rather the types of the smaller objects or values that we pass around in our code. 
-->

---

# Types

* Set of possible values and operations
* Primitive types
* User defined types
* Static vs Dynamic
* Exploratory programming

<!--
So what is a type anyway? The formal definition is that a type defines a set of possible values and a set of operations (for an object). So the type of a variable or property is just the set of possible values that variable can take. For primitive types like strings or integers this set of possible values can be very large.

When it comes to how typing is handled in programming languages, there are many variations, but one common divide, is between dynamicly typed and statically typed languages. In statically typed languages, the types of all variables are known at compile time. Wether the types are expicitly stated in the source code, or can be infered by the compiler. 

In dynamically typed languages, on the other hand the types are only known completely at run time. Dynamic vs static languages is an old debate. But I do think there is a trend towards adding more types, and checking types at compile time.

The main advantage of static languages is that you can catch errors at compile time.

For dynamic languages, there is perhaps an advantage in not having everything so explicit, in that it makes it more pleasent to just try out things without necesarily thinking through how everything fit's together. And it allowsa kind of exploratory programming, that is usefull in data science for instance.
-->

---

# Primitive Obsession

* Optimization
* Convenience
* Verbosity
* Validation
* Communicate intent

<!--
So one common code smell related to types, is the over use of of primitive types for small and simple objects.
This is a quite common thing to do so it´s even got a name. Primitive obsession.

There are a lot of examples of this that I think you can find in most code bases. User names, passwords, phone numbers, addresses, and so on are often represented by strings.

Maybe it´s that old practice of optimizing code to not waste memory or cpu, that used to be importent when those resources where limited. But in most cases that is not something we need to wory about anymore. Another reason might be that it´s just easy and convinient to grab a primitive type insteads of adding a custom type.

The advantages to making a custom type instead are many. It improves type safety. It also improves readability.

With primitive types code for doing validation will be spread all around the code base. And it´s easy to forget it somewhere. With a custom type you do validation in the constructor or a factory method, and that´s that. Whenever you pass it to a function, you can be sure it´s valid.

Also another point is that a custom type will communicate much clearer what that object represents in the domain, than when you pass around strings and ints. In that case the only thing you have to go by is the parameter names.
-->

---

# The Compiler is your friend

* Excplicit vs Implicit
* Compile time errors are better than runtime errors
* Feedback loop

<!--
But if you are using a language where the compiler can catch type errors at compile time, that is something you should embrace. 

Every type error the compiler can catch at compile time is a unit test you don't have to write.

And with most modern ide's the type checking will also be done by the ide itself, enabling you to catch errors even while you're typing. This shortens the feedback loop. So to take advantage of this we should structure our code such that as many errors as possible can be caught by the compiler (or the ide)

some errors like mixing up the order of parameters will then be caught at compile time, instead of causing problems at run time. 

And if you need to refactor at some point, it's a lot easier when you´re dealing with a concrete type
-->

---

# The problem with String

* A string can contain anything
* Validation must be enforced outside the type
* Buypasses the type system

<!--
So the worst offender, is the overuse of the string type. Coming back to the definition of a type as the set of possible values, a string can basically hold any value you can think of.

Which is why it's such a usefull and convenient type to use! But if you use string for everything, you might as well us a dynamic and weakly typed language.
because you'll get no help from the compiler. 

Even something as simple as a persons name can be problematic.
How long could a name be? How short? Can it contain numbers, punctuation. Is it just a first name? Last name and then first name? Then what about middle name? If this is something you need to enforce the structure of, then at least wraping it in a custom types saves you from duplicating validation code all over the place.
-->

---
# A String example

* National identity number
* 11 digits
* 2 control digits
<!--
another example where it seems natural to use string is the norwegian
fødselsnummer or national identity number (which is kind of like a sosial security number)
it contains 11 digits, and some of those encode things like date of birth and gender. and the last two digits are a checksum.

So a string is easy to use for this. And thats what all code i've seen dealing with national identiti numbers use. If you need to verify that it is valid
it's easy to check that it's a string containing exactly eleven digits. And it's easy to extract the last two digits if you need to verify the checksum.

But the problem remains that not all strings are going to be a valid identity number, even if it does contain only 11 digits. So you need to validate it, and that validation must be done everywhere it´s used.

So I think this probablyt should be it´s own type. If you have the code for validation you´re almost there anyway. And if you fear adding boilerplate and verbosity, it doesn´t need to be so bad
-->
---

# Constructors

```kotlin

"27113626688".toNationalIdentiyNumber

42.asPersonAge

10.dp
```
<!--
Take these examples from Kotlin, where you can use extension functions to make it really nice and concise. Making a national identity number from a string. Or a persons age from an int. The last example is from the android framework Jetpack Compose, where it constructs a density independent pixel from an int.

Similar constructs exists in some other languages, and if you can´t use those, just calling a constructor or a factory method isn´t really so bad?
-->
---
# Make illegal states unrepresentable

* Only valid instances can be constructed
* Can be proved at compile time
* Replaces some unit test
<!--
When you´ve decided to make a new type, you should try to make it so that it can only represent values that are valid according to the domain or business rules. If we go back to the definition of a type, the set of possible values should match exactly the set of legal values in the domain.

The principle is to "Make illegal states unrepresentable". If we follow this principle it´s not possible to have non-valid instances at runtime. if you make a mistake it will be caught at compile time. This can even eliminate the need for some unit tests.

A common example where this can be used, is when you have a class that consist of a few related objects, But the combined state of the whole thing is only valid for some combinations of values. Making it possible to construct instances of the class that are nonsensical.
-->

---

```kotlin
enum JobState { 
    Submitted,
    Running,
    Finished,
    Failed
}

data class Job (
    val id: UUID,
    val state: JobState,
    val progress: Int?,
    val result: Result?,
    val error: Error?
)
```
<!--
Take this representation of some kind of job or process. The process can be in one of four different states, and depending on which state, it will have some properties set, and some unused, that is in the case of Kotlin a null value. The problem is that the object can now be in a state that does not make logically sense. A job that is not started could be constructed to have progress, a running job could have an error set even if it´s not in the failed state, and so on.
All this makes the class harder to use, and increases the probability of making errors.  
-->

---

```kotlin
sealed class Job(val id: UUID)

data class Submitted(val id: UUID): Job(id)
data class Started(val id: UUID, val progress: int): Job(id)
data class Finished(val id: UUID, val result: Result): Job(id)
data class Failed(val id: UUID, val error: Error): Job(id)
```
<!--
The solution then is to use the type system to ensure illegal states cannot be constructed.

This is a good case for using sealed classes in Kotlin, 
and since Java 17, you can do this in Java as well. 

Now we can make sure that only a Job in a failed state has an error, and a Started job has progress, and so on. And this will be enforced by the compiler.

-->

---

# Conclusion

* Make types
* Be carefull with Strings
* Prefer compile time errors to runtime errors

</br>

[@mortenaa](twitter.com/mortenaa)
morten.nygaard.aasnes@dfind.no
<!--
So to conclude, make more types. Don´t overuse primitive types, even for simple cases. Be carefull with Strings. And try to make it easier for the compiler to catch errors at compile time.

Thank you!
-->
