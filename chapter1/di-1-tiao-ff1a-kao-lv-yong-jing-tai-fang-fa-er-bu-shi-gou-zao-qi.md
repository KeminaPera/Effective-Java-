## 第1条：考虑用静态方法而不是构造器

The traditional way for a class to allow a client to obtain an instance is to provide a public constructor. There is another technique that should be a part of every programmer’s toolkit. A class can provide a public static factory method, which is simply a static method that returns an instance of the class. Here’s a simple example from Boolean\(the boxed primitive class for boolean\). This method translates a boolean primitive value into a Boolean object reference:

对于一个类，若想让一个客户端获得它的实例，一种传统的方式就是该类提供一个公有的构造器。对于此，每个程序员的工具箱里头应该还有另一种技术：一个类可以提供一个公有的静态工厂方法，而这个方法返回类该类的一个实例。这里举一个Boolean类（基本类型boolean的包装类）的例子。这个方法将一个boolean基本类型值转换成类一个Boolean对象引用：

```
public static Boolean valueOf(boolean b) { 
    return b ? Boolean.TRUE : Boolean.FALSE;
}
```

Note that a static factory method is not the same as the Factory Method pattern from Design Patterns\[Gamma95\]. The static factory method described in this item has no direct equivalent in Design Patterns.

A class can provide its clients with static factory methods instead of, or in addition to, public constructors. Providing a static factory method instead of a public constructor has both advantages and disadvantages.

要注意的是，静态工厂方法不同于设计模式\[Gamma95\]中的工厂方法。本条目中描述的静态工厂方法不直接等同于设计模式中介绍的工厂方法。

一个类可以为它的客户端提供静态工厂方法，而不仅仅是公有构造器。提供静态工厂方法而不是公有构造器有些优点和缺点。

**One advantage of static factory methods is that, unlike constructors, they have names**.If the parameters to a constructor do not, in and of themselves, describe the object being returned, a static factory with a well-chosen name is easier to use and the resulting client code easier to read. For example, the constructor BigInteger\(int, int, Random\), which returns a BigInteger that is probably prime, would have been better expressed as a static factory method named BigInteger.probablePrime. \(This method was added in Java4.\)

A class can have only a single constructor with a given signature. Programmers have been known to get around this restriction by providing two constructors whose parameter lists differ only in the order of their parameter types. This is a really bad idea. The user of such an API will never be able to remember which constructor is which and will end up calling the wrong one by mistake. People reading code that uses these constructors will not know what the code does without referring to the class documentation.

Because they have names, static factory methods don’t share the restriction discussed in the previous paragraph. In cases where a class seems to require multiple constructors with the same signature, replace the constructors with static factory methods and carefully chosen names to highlight their differences.

  


