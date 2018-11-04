In almost all respects, enum types are superior to the typesafe enum pattern described in the first edition of this book \[Bloch01\]. On the face of it, one exception concerns extensibility, which was possible under the original pattern but is not supported by the language construct. In other words, using the pattern, it was possible to have one enumerated type extend another; using the language feature, it is not. This is no accident. For the most part, extensibility of enums turns out to be a bad idea. It is confusing that elements of an extension type are instances of the base type and not vice versa. There is no good way to enumerate over all of the elements of a base type and its extensions. Finally, extensibility would complicate many aspects of the design and implementation.

从各个方面来看，枚举类型优于本书第一版所描述的类型安全枚举模式\[Bloch01\]。表面上看，有个涉及到扩展性的地方，类型安全枚举模式包含了，而语言级别的构造则不支持。也就是说，采用类型安全枚举模式，可以让一个枚举类型继承另一个；而采用语言特性，则做不到这点。这并不意外。因为大多数情况下，让枚举具有可扩展性不是一个好的实践。首先，这么做会产生一些混乱，好像扩展类型的元素是基本类型的实例，同时基本类型的实例好像不是扩展类型的元素。到目前为止，没有一个好的办法来枚举一个基本类型及其扩展类型的所有元素。最后，在很多方面上，这么做使得设计和实现变得复杂化。

That said, there is at least one compelling use case for extensible enumerated types, which is operation codes, also known as opcodes. An opcode is an enumerated type whose elements represent operations on some machine, such as the Operation type in Item 34, which represents the functions on a simple calculator. Sometimes it is desirable to let the users of an API provide their own operations, effectively extending the set of operations provided by the API.

也就是说，对于可扩展枚举类型，至少有一个具有说服力的场景，它就是操作码（opcode）。一个操作码就是一个枚举类型，它的元素代表了某些机器上的操作，例如条目34里提到的操作类型，这些操作类型分别表示一个简单计算器的各个功能。有时候，用户会很希望他们正在用的API能提供一系列的操作，然后通过有效地去扩展这些操作来获得他们自己的操作。

Luckily, there is a nice way to achieve this effect using enum types. The basic idea is to take advantage of the fact that enum types can implement arbitrary interfaces by defining an interface for the opcode type and an enum that is the standard implementation of the interface. For example, here is an extensible version of the Operation type from Item 34:

幸运的是，有个可以采用枚举类型来达到这种目的的好办法——由于枚举类型可以实现任意个接口，所以，我们可以为操作类型定义一个接口，这样的话，一个枚举值就是这个接口的标准实现。以条目34的操作类型（Operation）为例，下面是该类型的可扩展实现版本：

**// Emulated extensible enum using an interface**

```java
public interface Operation {
    double apply(double x, double y);
}
public enum BasicOperation implements Operation {
    PLUS("+") {
        public double apply(double x, double y) { return x + y; }
    },
    MINUS("-") {
        public double apply(double x, double y) { return x - y; }
    },
    TIMES("*") {
        public double apply(double x, double y) { return x * y; }
    },
    DIVIDE("/") {
        public double apply(double x, double y) { return x / y; }
    };
    private final String symbol;
    BasicOperation(String symbol) {
        this.symbol = symbol;
    } 
    @Override 
    public String toString() {
        return symbol;
    }
}
```

While the enum type \(BasicOperation\) is not extensible, the interface type \(Operation\) is, and it is the interface type that is used to represent operations in APIs. You can define another enum type that implements this interface and use instances of this new type in place of the base type. For example, suppose you want to define an extension to the operation type shown earlier, consisting of the exponentiation and remainder operations. All you have to do is write an enum type that implements the _Operation_ interface:

虽然枚举类型（_BasicOperation_）无法扩展，但它的接口类型（_Operation_）可以，并且这个接口类型用于在API里表示操作。你可以定义另一个实现这个接口的枚举类型并使用这个新的类型的实例来替换原本的基础类型。例如，假设你想定义一个前面所示的操作类型的扩展，这个扩展由幂运算和余运算组成。你所需做的就是写一个实现了Operation接口的枚举类型：

**// Emulated extension enum**

```java
public enum ExtendedOperation implements Operation { 
    EXP("^") {
        public double apply(double x, double y) { 
            return Math.pow(x, y);
        } 
    },
    REMAINDER("%") {
        public double apply(double x, double y) {
            return x % y; 
        }
    };
    private final String symbol;
    ExtendedOperation(String symbol) { 
        this.symbol = symbol;
    }
    @Override 
    public String toString() { 
        return symbol;
    } 
}
```

You can now use your new operations anywhere you could use the basic operations, provided that APIs are written to take the interface type \(_Operation_\), not the implementation \(_BasicOperation_\). Note that you don’t have to declare the abstract _apply_ method in the enum as you do in a nonextensible enum with instance-specific method implementations \(page 162\). This is because the abstract method \(_apply_\) is a member of the interface \(_Operation_\).

现在你可以在任意可以使用基本运算的地方使用这两个新的运算，因为它们都实现了_Operation_接口类型，而不是实现_BasicOperation。_注意，你无需在枚举里声明_apply_方法，就像你不必在具有特定实例方法实现的不可扩展枚举里进行声明一样。这是因为，抽象方法（_apply_）是接口的一个成员（_Operation_）。

Not only is it possible to pass a single instance of an “extension enum” anywhere a “base enum” is expected, but it is possible to pass in an entire extension enum type and use its elements in addition to or instead of those of the base type. For example, here is a version of the test program on page 163 that exercises all of the extended operations defined previously:

不仅可以在任意要求传入“基本枚举”的场景传入“扩展枚举”的实例，还可以传入整个扩展枚举类型，并使用扩展枚举类型的元素来增加或者替换那些基本类型。例如，下面这个测试程序测试来前面定义的所有扩展后的运算：

```java
public static void main(String[] args) { 
    double x = Double.parseDouble(args[0]); 
    double y = Double.parseDouble(args[1]); 
    test(ExtendedOperation.class, x, y);
}
private static <T extends Enum<T> & Operation> void test( Class<T> opEnumType, double x, double y) {
    for (Operation op : opEnumType.getEnumConstants()) 
        System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
}
```

Note that the class literal for the extended operation type \(_ExtendedOperation.class_\) is passed from _main_ to _test_ to describe the set of extended operations. The class literal serves as a bounded type token\(Item 33\). The admittedly complex declaration for the _opEnumType_ parameter \(_&lt;T extends Enum&lt;T&gt;& Operation&gt; Class&lt;T&gt;_\) ensures that the _Class_ object represents both an enum and a subtype of _Operation_, which is exactly what is required to iterate over the elements and perform the operation associated with each one.

仔细看下，扩展操作类型的类字面值（_ExtendedOperation.class_）从_main_方法里传入_test_方法里，它描述了扩展操作集合。这个类字面值充当了有界类型的标记（条目33）。_opEnumType_参数的声明比较复杂（_&lt;T extends Enum&lt;T&gt;& Operation&gt; Class&lt;T&gt;_），但这确保了_Class_对象同时表示的是枚举和_Operation_的子类型，才能准确遍历所有的枚举元素并完成元素说代表的操作。

A second alternative is to pass a _Collection&lt;? extends Operation&gt;_, which is a bounded wildcard type\(Item 31\), instead of passing a class object:

第二种做法是传入_Collection&lt;? extends Operation&gt;，_它是一个有界通配符类型（条目31），而不是传入一个类对象：

```java
public static void main(String[] args) {
    double x = Double.parseDouble(args[0]);
    double y = Double.parseDouble(args[1]); 
    test(Arrays.asList(ExtendedOperation.values()), x, y);
}
private static void test(Collection<? extends Operation> opSet, double x, double y) { 
    for (Operation op : opSet)
        System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
}
```

The resulting code is a bit less complex, and the _test_ method is a bit more flexible: it allows the caller to combine operations from multiple implementation types. On the other hand, you forgo the ability to use _EnumSet_\(Item 36\) and _EnumMap_\(Item 37\) on the specified operations.

这种方式下的代码会稍微复杂些，但同时_test_方法也会灵活些：它允许调用方将不同操作类型实现结合起来。不过，另一方面，你也放弃了在特定操作上使用_EnumSet_（条目36）和_EnumMap_（条目37）的能力。

Both programs shown previously will produce this output when run with command line arguments 4 and 2:

在命令行里运行前面所示的两段代码，并传入参数4和2，输出的结果将会是这样子的：

```
4.000000 ^ 2.000000 = 16.000000 
4.000000 % 2.000000 = 0.000000
```

A minor disadvantage of the use of interfaces to emulate extensible enums is that implementations cannot be inherited from one enum type to another. If the implementation code does not rely on any state, it can be placed in the interface, using default implementations \(Item 20\). In the case of our _Operation_ example, the logic to store and retrieve the symbol associated with an operation must be duplicated in _BasicOperation_ and _ExtendedOperation_. In this case it doesn’t matter because very little code is duplicated. If there were a larger amount of shared functionality, you could encapsulate it in a helper class or a static helper method to eliminate the code duplication.

利用接口来模仿可扩展枚举有个小缺点，那就是这些接口实现彼此之前不能继承。如果接口实现代码不依赖于任何状态，那么可以用默认的实现来

The pattern described in this item is used in the Java libraries. For example, the _java.nio.file.LinkOption_ enum type implements  
 the _CopyOption_ and _OpenOption_ interfaces.

本条目里描述的模式被用于Java库。例如，_java.nio.file.LinkOption_枚举类型实现了_CopyOption_和_OpenOption_接口。

In summary, **while you cannot write an extensible enum type, you can emulate it by writing an interface to accompany a basic enum type that implements the interface. **This allows clients to write their own enums \(or other types\) that implement the interface. Instances of these types can then be used wherever instances of the basic enum type can be used, assuming APIs are written in terms of the interface.

总的来说，**虽然你无法编写一个可扩展枚举类型，但你可以通过编写一个接口以及一个实现了这个接口的基本枚举类型来模仿这一点。**这种做法允许客户端编写他们自己的实现了这个接口的枚举（或者其它类型）。这些实现了这个接口的类型的实例可以用于能使用基本枚举类型实例的任意场景。

