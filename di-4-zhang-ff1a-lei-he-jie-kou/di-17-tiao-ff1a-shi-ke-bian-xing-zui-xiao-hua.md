### 第17条：使可变性最小化

An immutable class is simply a class whose instances cannot be modified. All of the information contained in each instance is fixed for the lifetime of the object, so no changes can ever be observed. The Java platform libraries contain many immutable classes, including String, the boxed primitive classes, and BigInteger and BigDecimal. There are many good reasons for this: Immutable classes are easier to design, implement, and use than mutable classes. They are less prone to error and are more secure. To make a class immutable, follow these five rules:

1. **Don’t provide methods that modify the object’s state**\(known as mutators\).
2. **Ensure that the class can’t be extended.**This prevents careless or malicious subclasses from compromising the immutable behavior of the class by behaving as if the object’s state has changed. Preventing subclassing is generally accomplished by making the class final, but there is an alternative that we’ll discuss later.

3. **Make all fields final. **This clearly expresses your intent in a manner that is enforced by the system. Also, it is necessary to ensure correct behavior if a reference to a newly created instance is passed from one thread to another without synchronization, as spelled out in the memory model\[JLS, 17.5; Goetz06, 16\].

4. **Make all fields private. **This prevents clients from obtaining access to mutable objects referred to by fields and modifying these objects directly. While it is technically permissible for immutable classes to have public final fields containing primitive values or references to immutable objects, it is not recommended because it precludes changing the internal representation in a later release \(Items 15and16\).

5. **Ensure exclusive access to any mutable components. **If your class has any fields that refer to mutable objects, ensure that clients of the class cannot obtain references to these objects. Never initialize such a field to a client-provided object reference or return the field from an accessor. Make defensive copies\(Item 50\) in constructors, accessors, and readObject methods \(Item 88\).

简单来说，不可变类是指它的实例不能被更改的类。每个实例包含的信息在对象的整个生命周期都是固定的，因此在这个过程中也观察不到任何变化。Java类库包含了很多不可变类，包括String，封箱基本类，BigInteger以及BidDecimal。不可变类有几点好处：比起可变类，不可变类更容易设计，实现和使用，而且更不容易出错，更安全。若要让一个类不可变，我们可以遵循以下5条规则：

1. **不要提供能修改对象状态的方法**（即setter方法）。
2. **确保这个类不能被扩展。**这防止了粗心或者恶意的子类通过改变对象的状态，从而破坏类的不可变行为。一般情况下，可以通过将类设为final，从而防止被继承，但也有另一种方式，我们后面会讨论到。
3. **将所有域设为final。**通过这种系统强制的方式，可以清晰表达我们的意图。而且，如果一个指向新创建的实例的引用在缺乏同步机制的情况下从一个线程传入另一个线程，就必须确保正确的行为，正如内存模型（memory model）里描述的那样\[JLS, 17.5; Goetz06, 16\]。
4. **将所有域设为私有。**这防止客户端获得访问被域引用的可变对象的权限，从而防止了这些可变对象被直接修改。虽然从技术上可以让可变类的公有final域包含基础类型值或指向不可变对象的引用，但这么做也是不推荐的，因为这妨碍了在未来的发布版本中修改类的内部展示。
5. **确保对任何可变组件的互斥访问。**如果类里包含了指向可变对象的域，则要确保类的客户端不能包含指向这些对象的引用。永远不要用客户端提供的对象引用来初始化这些域，也不要从访问方法中返回该对象引用。在构造器，访问方法和readObject方法（条目88）中，请使用保护性拷贝（defensive copies，条目50）。

Many of the example classes in previous items are immutable. One such class is PhoneNumber in Item 11, which has accessors for each attribute but no corresponding mutators. Here is a slightly more complex example:

在前面的条目里，很多示例类都是不可变的。如条目11的PhoneNumber类，这个类为每个属性都写了一个访问方法，但是没有相应的设置方法。下面是个稍微复杂点的例子：

```
// Immutable complex number class 
public final class Complex {
    private final double re; 
    private final double im;
    public Complex(double re, double im) { 
        this.re = re;
        this.im = im;
    }
    public double realPart() { 
        return re; 
    } 
    public double imaginaryPart() { 
        return im; 
    }
    public Complex plus(Complex c) {
        return new Complex(re + c.re, im + c.im);
    }
    public Complex minus(Complex c) { 
        return new Complex(re - c.re, im - c.im);
    }
    public Complex times(Complex c) {
        return new Complex(re * c.re - im * c.im, re * c.im + im * c.re); 
    }
    public Complex dividedBy(Complex c) {
        double tmp = c.re * c.re + c.im * c.im;
        return new Complex((re * c.re + im * c.im) / tmp, (im * c.re - re * c.im) / tmp);

    }
    @Override 
    public boolean equals(Object o) { 
        if (o == this) return true;
        if (!(o instanceof Complex)) return false;
        Complex c = (Complex) o;
        // See page 47 to find out why we use compare instead of == 
        return Double.compare(c.re, re) == 0 && Double.compare(c.im, im) == 0; 
    }
    @Override 
    public int hashCode() {
        return 31 * Double.hashCode(re) + Double.hashCode(im);
    }
    @Override 
    public String toString() { 
        return "(" + re + " + " + im + "i)";
    } 
}
```

This class represents a complex number\(a number with both real and imaginary parts\). In addition to the standard Object methods, it provides accessors for the real and imaginary parts and provides the four basic arithmetic operations: addition, subtraction, multiplication, and division. Notice how the arithmetic operations create and return a new Complex instance rather than modifying this instance. This pattern is known as the functional approach because methods return the result of applying a function to their operand, without modifying it. Contrast it to the procedural or imperative approach in which methods apply a procedure to their operand, causing its state to change. Note that the method names are prepositions \(such as plus\) rather than verbs \(such as add\). This emphasizes the fact that methods don’t change the values of the objects. The BigInteger and BigDecimal classes did not obey this naming convention, and it led to many usage errors.

这个类表示了一个复数（复数包含了实数部分和虚数部分）。除了标准的对象方法，它还为实数部分和虚数部分分别提供了访问方法，同时还提供了四个基本基本运算：加法，减法，乘法和除法。注意看这些基本运算是如何被创建的，以及为什么是返回一个新的Complex实例而不是去修改原有的实例。这种模式被称为函数式方法（functional approach），因为方法返回了函数作用于操作数的结果，而不是去修改这个操作数。与之相对应的是过程方法（procedural approach）或命令方法（imperative approach），这两种方法都将一个过程作用于操作数上，使得操作数的状态改变。注意，方法名字用的是介词（如plus），而不是动词（如add）。这强调了方法不改变对象的值这么一个事实。BigInteger和BigDecimal类没有遵守这个命名规范，导致了很多用法上的错误。

The functional approach may appear unnatural if you’re not familiar with it, but it enables immutability, which has many advantages. Immutable objects are simple. An immutable object can be in exactly one state, the state in which it was created. If you make sure that all constructors establish class invariants, then it is guaranteed that these invariants will remain true for all time, with no further effort on your part or on the part of the programmer who uses the class. Mutable objects, on the other hand, can have arbitrarily complex state spaces. If the documentation does not provide a precise description of the state transitions performed by mutator methods, it can be difficult or impossible to use a mutable class reliably.

如果你不熟悉函数式方法，可能会觉得它有点不自然，但它可以让传进来的参数具有不变性，这能带来很多好处。不变的对象能让很多情况简单化。不变的对象能在其被创建之后就保持一致的状态。如果你能确保所有的构造器都建立了这个类的约束关系，那么就可以确保这些约束条件在任意时候都有效，你和用这个类的程序员都不用再做额外的工作。另一方面，可变对象可能会有任意复杂的状态空间。如果文档没有明确说明修改方法会使对象的状态转移，那么将很难甚至不可能去使用一个可变对象。

**Immutable objects are inherently thread-safe; they require no synchronization.** They cannot be corrupted by multiple threads accessing them concurrently. This is far and away the easiest approach to achieve thread safety. Since no thread can ever observe any effect of another thread on an immutable object, **immutable objects can be shared freely**. Immutable classes should therefore encourage clients to reuse existing instances wherever possible. One easy way to do this is to provide public static final constants for commonly used values. For example, the Complex class might provide these constants:

**不可变对象天生就是线程安全的，它们不要求同步。**当多个线程同时去访问它们时，它们也不会被破坏。这是线程安全最简单的实现方式。由于每个线程都无需考虑别的线程对不可变对象的影响，**不可变对象可以自由地被共享**。因此，不可变对象应该鼓励客户端只有有可能的话就复用现有实例。一种简便的方式可以帮助我们达到复用的目的，那就是为一些常用的值提供公有静态final的常量值。例如，Complex类可以提供这些常量：

```
public static final Complex ZERO = new Complex(0, 0);
public static final Complex ONE = new Complex(1, 0);
public static final Complex I = new Complex(0, 1);
```

This approach can be taken one step further. An immutable class can provide static factories \(Item 1\) that cache frequently requested instances to avoid creating new instances when existing ones would do. All the boxed primitive classes and BigInteger do this. Using such static factories causes clients to share instances instead of creating new ones, reducing memory footprint and garbage collection costs. Opting for static factories in place of public constructors when designing a new class gives you the flexibility to add caching later, without modifying clients.

这方式可以被进一步扩展。不可变类可以提供静态工厂（条目1）将频繁被请求的实例缓存起来，从而避免重复创建现有实例。所有的装箱基础类和BigInteger都采用了这种方式。使用这种静态工厂让客户端共享了实例，而不用创建新的实例，减少了内存占用和垃圾回收的成本。在设计一个新的类时，用静态工厂来替代公有构造方法能让方便我们以后添加缓存，同时还不用修改客户端。

A consequence of the fact that immutable objects can be shared freely is that you never have to make defensive copies of them \(Item 50\). In fact, you never have to make any copies at all because the copies would be forever equivalent to the originals. Therefore, you need not and should not provide a clone method or copy constructor \(Item 13\) on an immutable class. This was not well understood in the early days of the Java platform, so the String class does have a copy constructor, but it should rarely, if ever, be used \(Item 6\).

“不可变对象可以自由地被共享”这一点还有个好处，那就是我们永远都不用进行保护性拷贝（条目50）。实际上，我们永远都不用做任何拷贝，因为这些拷贝始终等于原对象。因而，你不用也无需为一个不可变类提供克隆方法或者用于复制的构造器（条目13）。在早期的Java平台中，这一点不是很好理解，所以String类提供了一个用于复制的构造器，但它应该尽量少地被用到。

**Not only can you share immutable objects, but they can share their internals.** For example, the BigInteger class uses a sign-magnitude representation internally. The sign is represented by an int, and the magnitude is represented by an int array. The negate method produces a new BigInteger of like magnitude and opposite sign. It does not need to copy the array even though it is mutable; the newly created BigInteger points to the same internal array as the original.

**不仅可以共享不可变对象，它们的内部信息也可以被共享。**例如，BigInteger类在内部使用符号-数值的方式来表示。符号通过一个int数值来表示，数值用一个int数组来表示。它的negate方法生成了一个数值相同，符号相反的新的BigInteger。这个方法不用拷贝数组，即使数组是可变的，新创建的BigInteger原始实例的同一个内部数组。

**Immutable objects make great building blocks for other objects**, whether mutable or immutable. It’s much easier to maintain the invariants of a complex object if you know that its component objects will not change underneath it. A special case of this principle is that immutable objects make great map keys and set elements: you don’t have to worry about their values changing once they’re in the map or set, which would destroy the map or set’s invariants.

**不可变对象为其它对象（无论是可变还是不可变）提供了大量的构件（building block）。**如果你知道一个复杂对象的内部组件不会改变，那么维护它的约束条件也简单很多。

Immutable objects provide failure atomicity for free \(Item 76\). Their state never changes, so there is no possibility of a temporary inconsistency.

The major disadvantage of immutable classes is that they require a separate object for each distinct value. Creating these objects can be costly, especially if they are large. For example, suppose that you have a million-bit BigInteger and you want to change its low-order bit:

```
BigInteger moby = ...;
moby = moby.flipBit(0);
```

The flipBit method creates a new BigInteger instance, also a million bits long, that differs from the original in only one bit. The operation requires time and space proportional to the size of the BigInteger. Contrast this to java.util.BitSet. Like BigInteger, BitSet represents an arbitrarily long sequence of bits, but unlike BigInteger, BitSet is mutable. The BitSet class provides a method that allows you to change the state of a single bit of a million-bit instance in constant time:

```
BitSet moby = ...;
moby.flip(0);
```

The performance problem is magnified if you perform a multistep operation that generates a new object at every step, eventually discarding all objects except the final result. There are two approaches to coping with this problem. The first is to guess which multistep operations will be commonly required and to providethem as primitives. If a multistep operation is provided as a primitive, the immutable class does not have to create a separate object at each step. Internally, the immutable class can be arbitrarily clever. For example, BigInteger has a package-private mutable “companion class” that it uses to speed up multistep operations such as modular exponentiation. It is much harder to use the mutable companion class than to use BigInteger, for all of the reasons outlined earlier. Luckily, you don’t have to use it: the implementors of BigInteger did the hard work for you.

The package-private mutable companion class approach works fine if you can accurately predict which complex operations clients will want to perform on your immutable class. If not, then your best bet is to provide a public mutable companion class. The main example of this approach in the Java platform libraries is the String class, whose mutable companion is StringBuilder \(and its obsolete predecessor, StringBuffer\).

