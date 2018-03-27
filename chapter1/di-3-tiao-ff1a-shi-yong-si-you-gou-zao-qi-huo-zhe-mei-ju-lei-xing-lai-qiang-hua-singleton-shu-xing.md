### 第3条：使用私有构造器或者枚举类型来强化Singleton属性

A singleton is simply a class that is instantiated exactly once \[Gamma95\]. Singletons typically represent either a stateless object such as a function \(Item 24\) or a system component that is intrinsically unique. **Making a class a singleton can make it difficult to test its clients **because it’s impossible to substitute a mock implementation for a singleton unless it implements an interface that serves as its type.

其实Singleton是指仅被实例化一次的类。Singleton通常被用来表示一个无状态的对象，比如函数（条目24），或者一个独一无二的系统组建。使类成为Singleton会使得它的客户端难以调试，因为无法用模拟实现去替代Singleton，除非这个类实现一个充当其类型的接口。

There are two common ways to implement singletons. Both are based on keeping the constructor private and exporting a public static member to provide access to the sole instance. In one approach, the member is a final field:

有两种通用的方式来实现Singleton。但这两种方式都是将构造器私有化，然后提供一个公有的静态成员，并通过这个静态成员来访问这个唯一的实例。在第一种方式中，这个静态成员是final属性：

```
// Singleton with public final field
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public void leaveTheBuilding() { ... } 
}
```

The private constructor is called only once, to initialize the public static final field _Elvis.INSTANCE_. The lack of a public or protected constructor guarantees a “monoelvistic” universe: exactly one Elvis instance will exist once the Elvis class is initialized—no more, no less. Nothing that a client does can change this, with one caveat: a privileged client can invoke the private constructor reflectively \(Item 65\) with the aid of the _AccessibleObject.setAccessible_ method. If you need to defend against this attack, modify the constructor to make it throw an exception if it’s asked to create a second instance.

此处的私有构造器仅被调用一次，用来初始化公有静态final属性_Elvis.INSTANCE_。公有构造器或受保护构造器的缺失保证了_Elvis_对象在全局中的唯一性，一旦_Elvis_被初始化，就只有一个_Elvis_实例存在，不多也不少。除了拥有特权的客户端能通过调用_AccessibleObject.setAccessible_方法进行反射调用私有构造器，否则客户端无论做什么都无法改变这一点。如果你需要防御这个攻击，可以修改构造器，使其在被请求创建第二个实例时抛出一个异常。

In the second approach to implementing singletons, the public member is a static factory method:

在第二种实现Singleton的方式中，公有成员是一个静态工厂方法：

```
// Singleton with static factory
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public static Elvis getInstance() { 
        return INSTANCE; 
    }
    public void leaveTheBuilding() { ... } 
}
```

All calls to _Elvis.getInstance_ return the same object reference, and no other _Elvis_ instance will ever be created \(with the same caveat mentioned earlier\).  
所有对于_Elvis.getInstance_的调用都将返回一个相同对象引用，而且别的_Elvis_对象将永远不被创建（除了刚提到的拥有特权的客户端的情况）。

The main advantage of the public field approach is that the API makes it clear that the class is a singleton: the public static field is final, so it will always contain the same object reference. The second advantage is that it’s simpler.

采用第一种方式的好处是，透过API，我们能清晰地意识到这个类是个Singleton：公有静态属性是final不可变的，所以它将总是保持着相同的对象引用。而第二种方式的好处是它更简单些。

One advantage of the static factory approach is that it gives you the flexibility to change your mind about whether the class is a singleton without changing its API. The factory method returns the sole instance, but it could be modified to return, say, a separate instance for each thread that invokes it. A second advantage is that you can write a generic singleton factory if your application requires it \(Item 30\). A final advantage of using a static factory is that a method reference can be used as a supplier, for example _Elvis::instance_ is a _Supplier&lt;Elvis&gt;_. Unless one of these advantages is relevant, the public field approach is preferable.

采用静态工厂的方式的另一个好处是，在不改变API的前提下，若想改变该类是否为Singleton的想法，它给了我们灵活性。工厂方法返回了唯一的实例，但我们也可以对其进行修改，比如改成对每个调用它的线程返回一个唯一实例。还一个好处是，如果应用有需求，我们可以写一个通用的Singleton工厂（条目30）。最后还有一个好处，就是静态工厂方法引用能作为一个Supplier，例如，_Elvis:instance_就是一个_Supplier&lt;Elvis&gt;_。除非上述这些优点的任一一个是很重要的，否则公有属性方式（即第一种方式）是更可取的。

To make a singleton class that uses either of these approaches serializable\(Chapter 12\), it is not sufficient merely to add implements Serializable to its declaration. To maintain the singleton guarantee, declare all instance fields transient and provide a readResolve method \(Item 89\). Otherwise, each time a serialized instance is deserialized, a new instance will be created, leading, in the case of our example, to spurious Elvis sightings. To prevent this from happening, add this readResolve method to the Elvis class:

对于采用上述任一一种方式创建的Singleton类，若要使其可序列化（第12章），仅仅在类的声明里让其实现Serializable接口是不够的。为了保证Singleton，应当将所有的实例属性声明为瞬时的（transient），并提供一个readResolve方法（条目89）。否则，每次反序列化一个已被序列化的实例时，将产生一个新的实例，在刚刚的例子中，就会产生一个假冒的Evlis实例。为了防止这个现象发生，我们可以在Elvis类里添加readResolve方法：

```
// readResolve method to preserve singleton property
private Object readResolve() {
// Return the one true Elvis and let the garbage collector // take care of the Elvis impersonator.
    return INSTANCE; 
}
```

A third way to implement a singleton is to declare a single-element enum:

第三种实现Singleton的方式是声明一个包含单个元素的枚举：

```
// Enum singleton - the preferred approach
public enum Elvis { 
    INSTANCE;
    public void leaveTheBuilding() { ... } 
}
```

This approach is similar to the public field approach, but it is more concise, provides the serialization machinery for free, and provides an ironclad guarantee against multiple instantiation, even in the face of sophisticated serialization or reflection attacks. This approach may feel a bit unnatural, but a single-element enum type is often the best way to implement a singleton. Note that you can’t use this approach if your singleton must extend a superclass other than Enum\(though you can declare an enum to implement interfaces\).

这种 方式类似于公有属性方式，但它更简洁，而且不仅免费提供了序列化机制，而且对多次初始化的行为提供了强有效的保证，即使是面对复杂的序列化或者反射攻击。这种方式可能会让人觉得有点不自然，但单元素枚举通常是实现Singleton的最佳方式。要注意的是，如果我们的Singleton必须扩展自一个超类而不是枚举时，这种方式就不能使用了（虽然你也可以申请一个枚举，这个枚举实现自一个或多个接口）。

