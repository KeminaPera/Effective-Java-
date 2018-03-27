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
  




