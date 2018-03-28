### 第6条：避免创建不必要的对象

It is often appropriate to reuse a single object instead of creating a new functionally equivalent object each time it is needed. Reuse can be both faster and more stylish. An object can always be reused if it is immutable \(Item 17\).

As an extreme example of what not to do, consider this statement:

很多时候，我们最好去复用一个对象而不是每次在需要时都去创建一个新的功能相同的对象。复用的方式不仅更快速，而且更时尚。如果一个对象是不可变的，那么它总是能被复用（条目17）。

举一个极端的反面例子，考虑下面这个语句：

```
String s = new String("bikini"); // DON'T DO THIS!
```

The statement creates a new String instance each time it is executed, and none of those object creations is necessary. The argument to the String constructor \("bikini"\) is itself a String instance, functionally identical to all of the objects created by the constructor. If this usage occurs in a loop or in a frequently invoked method, millions of String instances can be created needlessly.

这条语句每次被执行时都会产生一个新的String实例，但这些对象的创建都是不必要的。传递给String构造器的参数（"bikini"）本身就是一个String实例，而且功能上与构造器创建的所有实例一样。如果这个用法在循环里使用或被频繁调用，那么将会产生无数个不必要的String对象。

The improved version is simply the following:

改进的版本如下所示：

```
String s = "bikini";
```

This version uses a single String instance, rather than creating a new one each time it is executed. Furthermore, it is guaranteed that the object will be reused by any other code running in the same virtual machine that happens to contain the same string literal \[JLS, 3.10.5\].

这个版本只用了一个String实例，而不是每次执行时都创建一个新的。而且，它保证了只要在同一个虚拟机上，假如别的代码也刚好用了相同的字符串字面常量，该对象就会被复用。

You can often avoid creating unnecessary objects by using _static factory_ methods \(Item 1\) in preference to constructors on immutable classes that provide both. For example, the factory method _Boolean.valueOf\(String\)_ is preferable to the constructor _Boolean\(String\)_, which was deprecated in Java 9. The constructor must create a new object each time it’s called, while the factory method is never required to do so and won’t in practice. In addition to reusing immutable objects, you can also reuse mutable objects if you know they won’t be modified.

多数情况下，当不可变类同时提供了构造器和静态工厂方法时（条目1），我们优先使用_静态工厂_方法来避免创建不必要的对象。例如，比起在Java 9中已经过时的_Boolean\(String\)_，我们优先使用工厂方法Boolean.valueOf\(String\)。构造器每次被调用时都要创建一个新的对象，而工厂方法从不要求这么做，而且实际上也不会这么做。除了复用不可变的对象，我们也可以复用那些已知不会被修改的可变对象。

Some object creations are much more expensive than others. If you’re going to need such an “expensive object” repeatedly, it may be advisable to cache it for reuse. Unfortunately, it’s not always obvious when you’re creating such an object. Suppose you want to write a method to determine whether a string is a valid Roman numeral. Here’s the easiest way to do this using a regular expression:

