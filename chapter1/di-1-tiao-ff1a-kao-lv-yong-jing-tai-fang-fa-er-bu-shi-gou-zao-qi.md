## 第1条：考虑用静态方法而不是构造器

The traditional way for a class to allow a client to obtain an instance is to provide a public constructor. There is another technique that should be a part of every programmer’s toolkit. A class can provide a public static factory method, which is simply a static method that returns an instance of the class. Here’s a simple example from _Boolean_\(the boxed primitive class for _boolean_\). This method translates a _boolean_ primitive value into a _Boolean_ object reference:

对于一个类，若想让一个客户端获得它的实例，一种传统的方式就是该类提供一个公有的构造器。对于此，每个程序员的工具箱里头应该还有另一种技术：一个类可以提供一个公有的静态工厂方法，而这个方法返回类该类的一个实例。这里举一个_Boolean_类（基本类型_boolean_的包装类）的例子。这个方法将一个_boolean_基本类型值转换成类一个_Boolean_对象引用：

```
public static Boolean valueOf(boolean b) { 
    return b ? Boolean.TRUE : Boolean.FALSE;
}
```

Note that a static factory method is not the same as the _Factory Method_ pattern from _Design Patterns_\[Gamma95\]. The static factory method described in this item has no direct equivalent in _Design Patterns_.

A class can provide its clients with static factory methods instead of, or in addition to, public constructors. Providing a static factory method instead of a public constructor has both advantages and disadvantages.

要注意的是，静态工厂方法不同于设计模式\[Gamma95\]中的工厂方法模式（_Factory Method_ pattern）。本条目中描述的静态工厂方法不直接等同于设计模式中介绍的工厂方法。

一个类可以为它的客户端提供静态工厂方法，而不仅仅是公有构造器。提供静态工厂方法而不是公有构造器以下几点优势和不足之处。

**One advantage of static factory methods is that, unlike constructors, they have names**. If the parameters to a constructor do not, in and of themselves, describe the object being returned, a static factory with a well-chosen name is easier to use and the resulting client code easier to read. For example, the constructor _BigInteger\(int, int, Random\)_, which returns a _BigInteger_ that is probably prime, would have been better expressed as a static factory method named _BigInteger.probablePrime_. \(This method was added in Java4.\)

A class can have only a single constructor with a given signature. Programmers have been known to get around this restriction by providing two constructors whose parameter lists differ only in the order of their parameter types. This is a really bad idea. The user of such an API will never be able to remember which constructor is which and will end up calling the wrong one by mistake. People reading code that uses these constructors will not know what the code does without referring to the class documentation.

Because they have names, static factory methods don’t share the restriction discussed in the previous paragraph. In cases where a class seems to require multiple constructors with the same signature, replace the constructors with static factory methods and carefully chosen names to highlight their differences.

**比起构造器，静态工厂方法的一大优势是，它们有名字。**如果一个构造器的参数并不描述返回的对象，那么具有适当名字的静态工厂方法则更容易使用，而且产生的客户端代码也更容易阅读。例如，构造器_BigInteger\(int, int, Random\)_返回的_BigInteger_可能为素数，但假如采用一个静态工厂方法并将其命名为_BigInteger.probablePrime_，则能表达得更清楚。（Java4中已经添加了该方法。）

一个类只能有一个带有特定签名的构造器。程序员们都知道如何避开这个限制，那就是通过提供两个构造器，而它们的参数列表只在参数类型的顺序上有所不同。这真不是个好主意。用户在面对这样的API将很难记住哪个构造器是哪个，并最终误用了错误的构造器。人们在阅读用了这些构造器的代码时，若没有参考相关类的文档，也将很难知道这些代码干了什么。

由于静态工厂方法拥有名字，所以它们不受上述限制的影响。当一个类貌似需要多个相同签名的构造器时，可以用多个静态工厂方法来替代这些构造器，并对静态工厂方法们慎重选好名字以突出它们的不同。

**A second advantage of static factory methods is that, unlike constructors, they are not required to create a new object each time they’re invoked. **This allows immutable classes \(Item 17\) to use preconstructed instances, or to cache instances as they’re constructed, and dispense them repeatedly to avoid creating unnecessary duplicate objects.

The _Boolean.valueOf\(boolean\)_ method illustrates this technique: it never creates an object. This technique is similar to the _Flyweight_ pattern \[Gamma95\]. It can greatly improve performance if equivalent objects are requested often, especially if they are expensive to create.

The ability of static factory methods to return the same object from repeated invocations allows classes to maintain strict control over what instances exist at any time. Classes that do this are said to be _instance-controlled_.There are several reasons to write instance-controlled classes. Instance control allows a class to guarantee that it is a singleton \(Item 3\) or noninstantiable \(Item 4\). Also, it allows an immutable value class \(Item 17\) to make the guarantee that no two equal instances exist:_a.equals\(b\)_ if and only if _a == b._ This is the basis of the _Flyweight_ pattern \[Gamma95\]. Enum types \(Item 34\) provide this guarantee.

**静态工厂方法的第二大优势是，不像构造器，静态工厂方法不必在每次被调用时都产生一个新的对象。**这就使得那些不可变类（条目15）能使用提前构造好的实例，或者在它们初始化时就缓存好实例，从而可以不断重复地使用这些实例，避免创建不必要的重复对象。

_Boolean.valueOf\(boolean\)_方法就诠释了这种技术：它从不创建对象。这种技术类似于享元模式（_Flyweight_ pattern）\[Gamma95\]。在相同对象经常被请求的情况下，特别是创建这些对象的开销还很大时，此技术能大幅提升性能。

静态工厂方法的这种能为重复调用返回相同对象的能力允许类能严格控制在某个时刻应该存在哪些实例。这种类被叫做实体控制类（_instance-controlled_）。编写实体控制类有几个原因。实体控制能让一个类确保它是单例（条目3）的或者不可初始化的（条目4）。而且，实体控制也能让不可变类确保不会存在两个相同的实例：_a.equals\(b\)_当且仅当_a==b。_这也是享元模式的基础。枚举类型（条目34）提供了这种保证。

**A third advantage of static factory methods is that, unlike constructors, they can return an object of any subtype of their return type. **This gives you great flexibility in choosing the class of the returned object.

One application of this flexibility is that an API can return objects without making their classes public. Hiding implementation classes in this fashion leads to a very compact API. This technique lends itself to interface-based frameworks\(Item 20\), where interfaces provide natural return types for static factory methods. Prior to Java 8, interfaces couldn’t have static methods. By convention, static factory methods for an interface named _Type_ were put in a noninstantiable companion class\(Item 4\) named _Types_. For example, the Java Collections Framework has forty-five utility implementations of its interfaces, providing unmodifiable collections, synchronized collections, and the like. Nearly all of these implementations are exported via static factory methods in one noninstantiable class \(_java.util.Collections_\). The classes of the returned objects are all nonpublic.

The Collections Framework API is much smaller than it would have been had it exported forty-five separate public classes, one for each convenience implementation. It is not just the bulk of the API that is reduced but the conceptual weight:the number and difficulty of the concepts that programmers must master in order to use the API. The programmer knows that the returned object has precisely the API specified by its interface, so there is no need to read additional class documentation for the implementation class. Furthermore, using such a static factory method requires the client to refer to the returned object by interface rather than implementation class, which is generally good practice \(Item 64\). As of Java 8, the restriction that interfaces cannot contain static methods was eliminated, so there is typically little reason to provide a noninstantiable companion class for an interface. Many public static members that would have been at home in such a class should instead be put in the interface itself. Note, however, that it may still be necessary to put the bulk of the implementation code behind these static methods in a separate package-private class. This is because Java 8 requires all static members of an interface to be public. Java 9 allows private static methods, but static fields and static member classes are still required to be public.

**静态工厂方法的第三大优势是，不像构造器，静态工厂方法能任意原返回类型的子类型的对象。**这让我们在选择类的返回对象时具有很大的灵活性。

这种灵活性的一种应用就是，一个API可以返回对象，同时又不用使得类是公有的。在这种方式下隐藏类的实现能让API变得更简洁紧凑。

