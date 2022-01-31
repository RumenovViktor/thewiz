---
layout: page
title:  "How in/out and ref keywords work in C#"
date:   2022-01-31 21:29:11 +0300
categories: blog
permalink: /:categories/How-in-out-and-ref-keywords-work-in-C-sharp/
---

In this article, we're going to discuss the **in** and **out** keywords in C#. Probably
you've seen code using them and you've wondered how they work.

Let's start with the easier one - the out keyword:

### 1. Out keyword in C#

A good example of usage of out keyword is **int.TryParse** method. As you probably know, 
it can accept 2 parameters in it's signature - a string value which can might or might not be 
parsed to integer. Either way, it will return a boolean result from the execution, indicating if
the parsing was successful and the out value from the second parameter.

Let's see an example: 

```c#
var result = int.TryParse("Invalid", out int intValue);
Console.WriteLine($"Valid: {result}, IntValue: {intValue}"); // Valid: false, IntValue: 0"
```
This example shows that when an invalid value is passed, the result from the execution is going to be 
false, and default value of the integer will be assigned to the passed **out** parameter

Now let's see an example of a successful execution:

``` c#
var result = int.TryParse("5", out int intValue);
Console.WriteLine($"Valid: {result}, IntValue: {intValue}"); // Valid: true, IntValue: 5"
```

It's clear what happened here - the result from the execution is successul (true) and the parsed integer
is passed to the out parameter.

So what happened here? What happened behind the scenes is that - when we apply the out keyword, we're 
basically saying that we want to create a reference of the integer type and assign whichever value was
parsed from the string (default if it was not successful).

Let's take our own custom example:

```c#
static void Main(string[] args)
{
    ExampleOut(out int intValue);
    Console.WriteLine($"IntValue: {intValue}"); // IntValue: 5
}

private static void ExampleOut(out int intValue)
{
    intValue = 5;
}
```

Here we've defined a new method that's only parsing the passed value,
everything is fine, the reference is created and the value is transferred to the calling method. 
But what happens if we remove the initialization from ExampleOut?

```c#
static void Main(string[] args)
{
    ExampleOut(out int intValue);
    Console.WriteLine($"IntValue: {intValue}"); // IntValue: 5
}

private static void ExampleOut(out int intValue)
{
    // Compilation error...
}
```

The code doesn't compile...this is happening because there are additional
validations when we use **out** keyword, a value must always be provided.

The out keyword is built on top of **ref** keyword with some additional
validation tweaks. If we try to do it with ref though:

```c#
static void Main(string[] args)
{
    int intValue = 56;
    ExampleOut(ref intValue);
    Console.WriteLine($"IntValue: {intValue}");
}

private static void ExampleOut(ref int intValue)
{
    // Builds without any issue
}
```

### 2. In keyword in C#

The **In** keyword is very much like **ref** as we saw for the **out**
keyword, but with the difference that by marking a variable with it,
it makes it immutable.

```c#
var intValue = 56;
ExampleOut(intValue);

static void ExampleOut(in int intValue)
{
    intValue = 3; // This will not compile
}
```

It totally makes sense if you want to restrict the modifications of the value
of the passed parameter, but it comes with some drawbacks which we won't
be discussing as part of this article.

### Conclusion

We took a loot at how ref, in and out keywords in C# work. We saw that
they are creating a reference of the passed parameter and they are just
built on top of the ref keyword with some additional restrictions.

We saw that the difference between ref, in and out keywords is:
- out - creates a reference and forces initialization of the variable
- in - creates a reference and forces immutability of the variable
- ref - create a reference of the variable
