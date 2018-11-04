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

While the enum type \(BasicOperation\) is not extensible, the interface type \(Operation\) is, and it is the interface type that is used to represent operations in APIs. You can define another enum type that implements this interface and use instances of this new type in place of the base type. For example, suppose you want to define an extension to the operation type shown earlier, consisting of the exponentiation and remainder operations. All you have to do is write an enum type that implements the Operation interface:

虽然枚举类型（BasicOperation）无法扩展，但它的接口类型（Operation）可以，并且这个接口类型用于在API里表示操作。你可以定义另一个实现这个接口的枚举类型并

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



