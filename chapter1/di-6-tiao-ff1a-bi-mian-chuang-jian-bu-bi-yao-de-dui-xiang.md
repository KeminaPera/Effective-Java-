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

多数情况下，当不可变类同时提供了构造器和静态工厂方法时（条目1），我们优先使用_静态工厂_方法来避免创建不必要的对象。例如，比起在Java 9中已经过时的_Boolean\(String\)_，我们优先使用工厂方法_Boolean.valueOf\(String\)_。构造器每次被调用时都要创建一个新的对象，而工厂方法从不要求这么做，而且实际上也不会这么做。除了复用不可变的对象，我们也可以复用那些已知不会被修改的可变对象。

Some object creations are much more expensive than others. If you’re going to need such an “expensive object” repeatedly, it may be advisable to cache it for reuse. Unfortunately, it’s not always obvious when you’re creating such an object. Suppose you want to write a method to determine whether a string is a valid Roman numeral. Here’s the easiest way to do this using a regular expression:

一些对象的创建的代价要比其它的对象要大。如果我们将不断地需要这些高代价对象，一个建议就是将其缓存起来并复用。不幸的是，什么时候需要创建这种对象并不总是那么明显。假设我们要编写一个方法来确定一个字符串是否是有效的罗马数字，最简单的方式就是通过使用正则表达式，如下所示：

```
// Performance can be greatly improved!
static boolean isRomanNumeral(String s) {
    return s.matches("^(?=.)M*(C[MD]|D?C{0,3})" + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$"); 
}
```

The problem with this implementation is that it relies on the _String.matches_ method. While String.matches is the easiest way to check if a string matches a regular expression, it’s not suitable for repeated use in performance-critical situations.The problem is that it internally creates a _Pattern_ instance for the regular expression and uses it only once, after which it becomes eligible for garbage collection. Creating a _Pattern_ instance is expensive because it requires compiling the regular expression into a finite state machine.

这种实现方式的问题在于它依赖于_String.matches_方法。虽然String.matches是校验一个字符串是否符合一个正则表达式最简单的方式，但在性能要求高的情景下，若想重复使用这种方式，就显得不是那么合适了。这里的问题是，它在内部创建了一个Pattern实例来用于正则表达式然后这个实例只使用一次，使用完之后它就被垃圾回收了。创建一个Pattern实例是昂贵的，因为它需要将正则表达式编译成一个有限状态机。

To improve the performance, explicitly compile the regular expression into a _Pattern_ instance \(which is immutable\) as part of class initialization, cache it, and reuse the same instance for every invocation of the _isRomanNumeral_ method:

为了改善性能，我们可以将所需的正则表达式显式地编译进一个不可变的Pattern对象里，并让其作为类初始化的一部分，将其缓存起来。这样以后每次调用isRomanNumeral方法时，就可以复用相同的Pattern对象了：

```
// Reusing expensive object for improved performance
public class RomanNumerals {
    private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})"
                + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
    static boolean isRomanNumeral(String s) { 
        return ROMAN.matcher(s).matches();
    } 
}
```

The improved version of _isRomanNumeral_ provides significant performance gains if invoked frequently. On my machine, the original version takes 1.1 μs on an 8-character input string, while the improved version takes 0.17 μs, which is 6.5 times faster. Not only is the performance improved, but arguably, so is clarity. Making a static final field for the otherwise invisible Pattern instance allows us to give it a name, which is far more readable than the regular expression itself.

在面对频繁调用时，改进版的_isRomanNumeral_得到了显著的性能提升。在我的电脑上，对于8个字符的字符串输入，原始的版本花费了1.1毫秒，而改进的版本则只花费了0.17毫秒，比原先的快了6.5倍。改进后的版本不仅性能提升了，而且可以说更清晰了，因为我们可以给静态不可改的私有Pattern实例取一个名字，而这比正则表达式本身更容易阅读。

If the class containing the improved version of the _isRomanNumeral_ method is initialized but the method is never invoked, the field _ROMAN_ will be initialized needlessly. It would be possible to eliminate the initialization by lazily initializing the field \(Item 83\) the first time the _isRomanNumeral_ method is invoked, but this is not recommended. As is often the case with lazy initialization, it would complicate the implementation with no measurable performance improvement \(Item 67\).

如果包含了改进版的_isRomanNumeral_方法的类被初始化了，但这个方法缺从没被调用过，那么_ROMAN_属性的初始化就没什么意义了。虽然我们可以在_isRomanNumeral_方法被调用时才去初始化这个属性（条目83），从而减少了无意义的初始化，但这么做是不推荐的。延迟初始化（lazy initialization）会导致实现复杂化，而性能却没有可观的改进（条目67）。

When an object is immutable, it is obvious it can be reused safely, but there are other situations where it is far less obvious, even  
 counterintuitive. Consider the case of adapters \[Gamma95\], also known as views. An adapter is an object that delegates to a backing object, providing an alternative interface. Because an adapter has no state beyond that of its backing object, there’s no need to create more than one instance of a given adapter to a given object.

当一个对象是不可变时，很明显它能被安全地复用。但其它的一些情况就不是那么明显了，有时甚至还会有点违反直觉。考虑适配器（adapter\[Gamma95\]）的情形，也叫做视图。适配器是这么一个对象，它代理了后端对象（backing object），然后提供了一个转换的接口。由于适配器除了后端对象之外，没有其它的状态信息，所以对于指定的对象，创建一个指定的适配器就可以了。

For example, the keySet method of the Map interface returns a Set view of the Map object, consisting of all the keys in the map. Naively, it would seem that every call to keySet would have to create a new Set instance, but every call to keySet on a given Map object may return the same Set instance. Although the returned Set instance is typically mutable, all of the returned objects are functionally identical: when one of the returned objects changes, so do all the others, because they’re all backed by the same Map instance. While it is largely harmless to create multiple instances of the keySet view object, it is unnecessary and has no benefits.

例如，Map接口的keySet方法返回了Map对象的Set视图，这个视图里包含了map里的所有键值。乍一看，好像每次调用keySet方法时都会创建一个新的Set实例，但其实每次调用指定的Map对象的keySet方法时，都返回了相同的Set实例。虽然所有返回的Set实例通常都是可变的，但所有返回的对象都是功能一致的：当其中一个返回的对象发生变化时，所有的其它对象也会发生变化，因为它们都由同一个Map实例支撑。虽然创建keySet视图对象的多个实例并没有什么害处，却也是没必要的，也没啥好处。

Another way to create unnecessary objects is _autoboxing_, which allows the programmer to mix primitive and boxed primitive types, boxing and unboxing automatically as needed. Autoboxing blurs but does not erase the distinction between primitive and boxed primitive types.There are subtle semantic distinctions and not-so-subtle performance differences \(Item 61\). Consider the following method, which calculates the sum of all the positive _int_ values. To do this, the program has to use long arithmetic because an int is not big enough to hold the sum of all the positive int values:

另一种会创建不必要对象的方式是自动装箱（_autoboxing_）， 这种方式允许程序员混用基本类型和装箱基本类型，然后按需自动装箱和解箱。自动装箱模糊了基本类型和装箱基本类型之间的区别，但并没有消除这种区别。它们之间在语义上有着微妙的区别，同时在性能上有着较为明显饿差别（条目61）。我们一起来看看下面这个方法。这个方法计算了所有_int_正值的和。为了计算这个和，程序员必须采用_long_，因为一个_int_不够大，无法容纳所有_int_正值的总和：

```
// Hideously slow! Can you spot the object creation?
private static long sum() {
    Long sum = 0L;
    for (long i = 0; i <= Integer.MAX_VALUE; i++)
        sum += i;
    return sum; 
}
```

This program gets the right answer, but it is much slower than it should be, due to a one-character typographical error. The variable sum is declared as a Long instead of a long, which means that the program constructs about $$2^{31}$$ unnecessary Long instances \(roughly one for each time the long i is added to the Long sum\). Changing the declaration of sum from Long to long reduces the runtime from 6.3 seconds to 0.59 seconds on my machine. The lesson is clear:prefer primitives to boxed primitives, and watch out for unintentional autoboxing.

上述这段程序能获得正确的值，但它比实际情况要慢很多，因为程序里面打错了一个字符。变量sum被声明成了Long类型，而不是long，这意味着程序构造了$$2^{31}$$个不必要的Long实例（大约每次往Long类型的sum中增加long时构造一个Long实例）。将sum的类型从Long换成long时，在我的机器上运行时间从6.3秒减成了0.59秒。结论很明显：优先使用基本类型而不是装箱基本类型，同时小心无意识的自动装箱。

This item should not be misconstrued to imply that object creation is expensive and should be avoided. On the contrary, the creation and reclamation of small objects whose constructors do little explicit work is cheap, especially on modern JVM implementations. Creating additional objects to enhance the clarity, simplicity, or power of a program is generally a good thing.

但千万不要将本条目误解为“创建对象的代价很昂贵，所以我们应该避免创建对象”。相反，那些构造器做很少显示工作的小对象的创建和回收是很廉价的，特别是在现代的JVM实现上。通过创建额外的对象来加强代码的清晰、简单或者功能，这通常是件好事。

Conversely, avoiding object creation by maintaining your own object pool is a bad idea unless the objects in the pool are extremely heavyweight. The classic example of an object that does justify an object pool is a database connection. The cost of establishing the connection is sufficiently high that it makes sense to reuse these objects. Generally speaking, however, maintaining your own object pools clutters your code, increases memory footprint, and harms performance. Modern JVM implementations have highly optimized garbage collectors that easily outperform such object pools on lightweight objects.

  


