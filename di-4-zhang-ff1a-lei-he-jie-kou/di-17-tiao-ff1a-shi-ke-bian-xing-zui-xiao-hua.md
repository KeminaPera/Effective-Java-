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

这个类表示了一个复数（复数包含了实数部分和虚数部分）。除了标准的对象方法，它还为实数部分和虚数部分分别提供了访问方法，同时还提供了四个基本基本运算：加法，减法，乘法和除法。注意看这些基本运算是如何被创建的，以及为什么是返回一个新的Complex实例而不是去修改原有的实例。这种模式被称为函数式方法（functional approach），因为方法返回了操作数作用于函数上的结果，而不是去修改这个操作数。

The functional approach may appear unnatural if you’re not familiar with it, but it enables immutability, which has many advantages. Immutable objects are simple. An immutable object can be in exactly one state, the state in which it was created. If you make sure that all constructors establish class invariants, then it is guaranteed that these invariants will remain true for all time, with no further effort on your part or on the part of the programmer who uses the class. Mutable objects, on the other hand, can have arbitrarily complex state spaces. If the documentation does not provide a precise description of the state transitions performed by mutator methods, it can be difficult or impossible to use a mutable class reliably.

**Immutable objects are inherently thread-safe; they require no synchronization.** They cannot be corrupted by multiple threads accessing them concurrently. This is far and away the easiest approach to achieve thread safety. Since no thread can ever observe any effect of another thread on an immutable object, **immutable objects can be shared freely**. Immutable classes should therefore encourage clients to reuse existing instances wherever possible. One easy way to do this is to provide public static final constants for commonly used values. For example, the Complex class might provide these constants:

```
public static final Complex ZERO = new Complex(0, 0);
public static final Complex ONE = new Complex(1, 0);
public static final Complex I = new Complex(0, 1);
```

This approach can be taken one step further. An immutable class

can provide static factories \(Item 1\) that cache frequently

requested instances to avoid creating new instances when existing

ones would do. All the boxed primitive classes and BigInteger do

this. Using such static factories causes clients to share instances

instead of creating new ones, reducing memory footprint and

garbage collection costs. Opting for static factories in place of

public constructors when designing a new class gives you the

flexibility to add caching later, without modifying clients.

