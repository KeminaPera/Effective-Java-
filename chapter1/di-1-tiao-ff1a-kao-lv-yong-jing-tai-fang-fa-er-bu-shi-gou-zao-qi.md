第1条：考虑用静态方法而不是构造器

The traditional way for a class to allow a client to obtain an instance is to provide a public constructor. There is another technique that should be a part of every programmer’s toolkit. A class can provide a public static factory method, which is simply a static method that returns an instance of the class. Here’s a simple example from _Boolean_\(the boxed primitive class for _boolean_\). This method translates a _boolean_ primitive value into a _Boolean_ object reference:

对于一个类，若想让一个客户端获得它的实例，一种传统的方式就是该类提供一个公有的构造器。对于此，每个程序员的工具箱里头应该还有另一种技术：一个类可以提供一个公有的静态工厂方法，而这个方法返回该类的一个实例。这里举一个_Boolean_类（基本类型_boolean_的包装类）的例子。这个方法将一个_boolean_基本类型值转换成类一个_Boolean_对象引用：

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

**比起构造器，静态工厂方法的一大优势是，它们有名字。**如果一个构造器的参数并不描述返回的对象，那么具有适当名字的静态工厂方法则更容易使用，而且产生的客户端代码也更容易阅读。例如，构造器_BigInteger\(int, int, Random\)_返回的_BigInteger_可能为素数，但假如采用一个静态工厂方法并将其命名为_BigInteger.probablePrime_，则能表达得更清楚。（Java 4中已经添加了该方法。）

一个类只能有一个带有特定签名的构造器。程序员们都知道如何避开这个限制，那就是通过提供两个构造器，而它们的参数列表只在参数类型的顺序上有所不同。这不是个好主意。用户在面对这样的API将很难记住哪个构造器是哪个，并最终误用了错误的构造器。人们在阅读用了这些构造器的代码时，若没有参考相关类的文档，也将很难知道这些代码干了什么。

由于静态工厂方法拥有名字，所以它们不受上述限制的影响。当一个类貌似需要多个相同签名的构造器时，可以用多个静态工厂方法来替代这些构造器，并对静态工厂方法们慎重选好名字以突出它们的不同。

**A second advantage of static factory methods is that, unlike constructors, they are not required to create a new object each time they’re invoked. **This allows immutable classes \(Item 17\) to use preconstructed instances, or to cache instances as they’re constructed, and dispense them repeatedly to avoid creating unnecessary duplicate objects.

The _Boolean.valueOf\(boolean\)_ method illustrates this technique: it never creates an object. This technique is similar to the _Flyweight_ pattern \[Gamma95\]. It can greatly improve performance if equivalent objects are requested often, especially if they are expensive to create.

The ability of static factory methods to return the same object from repeated invocations allows classes to maintain strict control over what instances exist at any time. Classes that do this are said to be _instance-controlled_.There are several reasons to write instance-controlled classes. Instance control allows a class to guarantee that it is a singleton \(Item 3\) or noninstantiable \(Item 4\). Also, it allows an immutable value class \(Item 17\) to make the guarantee that no two equal instances exist:_a.equals\(b\)_ if and only if _a == b._ This is the basis of the _Flyweight_ pattern \[Gamma95\]. Enum types \(Item 34\) provide this guarantee.

**静态工厂方法的第二大优势是，不像构造器，静态工厂方法不必在每次被调用时都产生一个新的对象。**这就使得那些不可变类（条目15）能使用提前构造好的实例，或者在它们初始化时就缓存好实例，从而可以不断重复地使用这些实例，避免创建不必要的重复对象。

_Boolean.valueOf\(boolean\)_方法就诠释了这种技术：它从不创建对象。这种技术类似于享元模式（_Flyweight_ pattern）\[Gamma95\]。在相同对象经常被请求的情况下，特别是创建这些对象的开销还很大时，此技术能大幅提升性能。

静态工厂方法的这种能为重复调用返回相同对象的能力允许类能严格控制在某个时刻应该存在哪些实例。这种类被叫做实体控制类（_instance-controlled_）。编写实体控制类有几个原因。实体控制能让一个类确保它是单例（条目3）的或者不可初始化的（条目4）。而且，实体控制也能让不可变类确保不会存在两个相同的实例：_a.equals\(b\)_当且仅当_a==b。_这也是享元模式的基础。枚举类型（条目34）提供了这种保证。

**A third advantage of static factory methods is that, unlike constructors, they can return an object of any subtype of their return type. **This gives you great flexibility in choosing the class of the returned object.

One application of this flexibility is that an API can return objects without making their classes public. Hiding implementation classes in this fashion leads to a very compact API. This technique lends itself to interface-based frameworks\(Item 20\), where interfaces provide natural return types for static factory methods.

Prior to Java 8, interfaces couldn’t have static methods. By convention, static factory methods for an interface named _Type_ were put in a noninstantiable companion class\(Item 4\) named _Types_. For example, the Java Collections Framework has forty-five utility implementations of its interfaces, providing unmodifiable collections, synchronized collections, and the like. Nearly all of these implementations are exported via static factory methods in one noninstantiable class \(_java.util.Collections_\). The classes of the returned objects are all nonpublic.

The Collections Framework API is much smaller than it would have been had it exported forty-five separate public classes, one for each convenience implementation. It is not just the bulk of the API that is reduced but the conceptual weight:the number and difficulty of the concepts that programmers must master in order to use the API. The programmer knows that the returned object has precisely the API specified by its interface, so there is no need to read additional class documentation for the implementation class. Furthermore, using such a static factory method requires the client to refer to the returned object by interface rather than implementation class, which is generally good practice \(Item 64\).

As of Java 8, the restriction that interfaces cannot contain static methods was eliminated, so there is typically little reason to provide a noninstantiable companion class for an interface. Many public static members that would have been at home in such a class should instead be put in the interface itself. Note, however, that it may still be necessary to put the bulk of the implementation code behind these static methods in a separate package-private class. This is because Java 8 requires all static members of an interface to be public. Java 9 allows private static methods, but static fields and static member classes are still required to be public.

**静态工厂方法的第三大优势是，不像构造器，静态工厂方法能任意原返回类型的子类型的对象。**这让我们在选择类的返回对象时具有很大的灵活性。

这种灵活性的一种应用就是，一个API可以返回对象，同时又不用使得类是公有的。在这种方式下隐藏类的实现能让API变得更简洁紧凑。这种技术适用于基于接口的框架（_interface-based frameworks_，条目20），在这种框架中，接口为静态工厂方法提供了自然的返回类型。

在Java 8之前，接口不能拥有静态方法。按照Java 8之前的传统惯例，接口Type的静态工厂方法被放在了不可实例化的伙伴类（companion class，条目4）Types类里。例如，Java集合框架的接口有45个便利实现，提供了不可修改集合，同步集合等。几乎所有的这些实现都是通过在一个不可实例化的类（_java.util.Collections_）中导出。所有返回对象的类都是非公有的。

这种集合框架API的实现方式比单独为导出45个公有类的方式要小得多，每个类对应一个便利实现。这不仅API数量的减少，而且也是概念意义上的减少——程序员为了用这个API而必须掌握的概念的数量和难度。程序员知道返回的对象是由它的接口精确指定的，从而没必要再去阅读这个对象实现类的参考文档了。而且，使用这种静态工厂方法时，要求客户端通过接口来引用返回对象而不是通过实现类，这是一种好的编程实践（条目64）。

对于Java 8，去除了接口不能包含静态方法的限制，所以大多数情况下没什么理由为一个接口提供一个不可实例化的伴随类。许多原本应该放在伙伴类里面的静态成员现在应该放在接口里。然而要注意的是，仍然有必要将这些实现代码放在一个单独的包私有类的静态方法里面。这是因为Java 8要求接口里的所有静态成员都必须是公有的。Java 9允许接口包含私有静态方法，但静态属性和静态类成员仍需是公有的。

**A fourth advantage of static factories is that the class of the returned object can vary from call to call as a function of the input parameters. **Any subtype of the declared return type is permissible. The class of the returned object can also vary from release to release.  
 The _EnumSet_ class \(Item 36\) has no public constructors, only static factories. In the OpenJDK implementation, they return an instance of one of two subclasses, depending on the size of the underlying enum type: if it has sixty-four or fewer elements, as most enum types do, the static factories return a _RegularEnumSet_ instance, which is backed by a single _long_; if the enum type has sixty-five or more elements, the factories return _aJumboEnumSet_ instance, backed by a _long_ array.

The existence of these two implementation classes is invisible to clients. If _RegularEnumSet_ ceased to offer performance advantages for small enum types, it could be eliminated from a future release with no ill effects. Similarly, a future release could add a third or fourth implementation of _EnumSet_ if it proved beneficial for performance. Clients neither know nor care about the class of the object they get back from the factory; they care only that it is some subclass of _EnumSet_.

**静态工厂的第四大优势是，可以根据调用时传入的不同参数而返回不同类的对象。**声明返回类型的任意子类型都是被允许的。返回对象的类也可以因不同发布版本而不同。

_EnumSet_类（条目36）没有公有的构造器，而只有静态工厂。在OpenJDK的实现里，这些静态工厂返回两个子类中的一个实例，这取决于底层枚举类型的大小：若跟大多数枚举类型一样，包含64个或者更少的元素，那么静态工厂返回一个由_long_支持的_RegularEnumSet_实例；若包含了65个或者更多的元素，那么静态工厂返回一个由long数组支持的JumboEnumSet实例。

这两个实现类的存在对于客户端是不可见的。假如RegularEnumSet对于小型枚举类型不再具有性能优势时，那么在未来版本中大可将RegularEnumSet去除并且不会产生任何坏的影响。类似地，在未来版本中，可以添加第三个或第四个Enumset的实现，如果这些实现被证明能提供好的性能。客户不用知道或者关心他们从工厂里拿回来的对象的类，他们只关心拿回来的是EnumSet的子类就可以了。

**A fifth advantage of static factories is that the class of the returned object need not exist when the class containing the method is written. **Such flexible static factory methods form the basis of service provider frameworks, like the Java Database Connectivity API \(JDBC\). A service provider framework is a system in which providers implement a service, and the system makes the implementations available to clients, decoupling the clients from the implementations.

There are three essential components in a service provider framework: a service interface, which represents an implementation; a provider registration API, which providers use to register implementations; and a service access API, which clients use to obtain instances of the service. The service access API may allow clients to specify criteria for choosing an implementation. In the absence of such criteria, the API returns an instance of a default implementation, or allows the client to cycle through all available implementations. The service access API is the flexible static factory that forms the basis of the service provider framework.

An optional fourth component of a service provider framework is a service provider interface, which describes a factory object that produce instances of the service interface. In the absence of a service provider interface, implementations must be instantiated reflectively \(Item 65\). In the case of JDBC, _Connection_ plays the part of the service interface, _DriverManager.registerDriver_ is the provider registration API, _DriverManager.getConnection_ is the service access API, and _Driver_ is the service provider interface.

There are many variants of the service provider framework pattern. For example, the service access API can return a richer service interface to clients than the one furnished by providers. This is the _Bridge_ pattern \[Gamma95\]. Dependency injection frameworks \(Item 5\) can be viewed as powerful service providers. Since Java 6, the platform includes a general-purpose service provider framework, _java.util.ServiceLoader_, so you needn’t, and generally shouldn’t, write your own \(Item 59\). JDBC doesn’t use _ServiceLoader_, as the former predates the latter.

**静态工厂的第五大优势是，在编写包含该方法的类时，返回对象的类不需要存在。**灵活的静态工厂方法是服务提供者框架的基础，比如Java数据库连接API（JDBC）。服务提供者框架是指提供了服务实现的系统，而这个系统对客户端可用，从而使得客户端跟实现解耦。

一个服务提供者框架有三个基本组件：用于展示实现的服务接口；用于给提供者注册实现的提供者注册API；用于给客户端获取服务示例的服务访问API。服务访问API允许客户端指定选择实现的标准。若没有指定标准，API则返回一个默认的实现实例，或允许客户端循环遍历所有可用的实现。服务访问API就是这些灵活的静态工厂，而这些静态工厂形成了服务提供者框架的基础。

除了上述三个组件外，一个可选的组件是服务提供者接口。服务提供者接口描述了生产服务接口实例的工厂对象。若没有服务提供者接口，则实现必须通过反射进行初始化（条目65）。在JDBC的例子中，_Connection_类扮演了服务接口的角色，_DriverManager.registerDriver_扮演了服务者注册API的角色，_DriverManager.getConnection_扮演了服务访问API的角色，_Driver_扮演了服务提供者接口的角色。

服务提供者框架模式也有很多变种。例如，服务访问API可以向客户端返回一个比服务提供者提供的更丰富的服务接口。这是桥接模式（_Bridge_ pattern）\[Gamma95\]。依赖注入框架（条目5）可以看成是强大的服务提供者。从Java 6开始，jdk包含了一个通用的服务提供框架，_java.util.ServiceLoader，_因此你无需也不应该再自己写一个框架了（条目59）。JDBC没有使用_ServiceLoader_，因为它在Java6之前就存在了。

**The main limitation of providing only static factory methods is that classes without public or protected constructors cannot be subclassed. **For example, it is impossible to subclass any of the convenience implementation classes in the Collections Framework. Arguably this can be a blessing in disguise because it encourages programmers to use composition instead of inheritance \(Item 18\), and is required for immutable types \(Item 17\).

**只提供静态工厂方法的主要限制是，没有公有或者保护构造方法的类不能有子类。**例如，Java的集合框架里面的任一便利实现都无法被子类化。但另一方面，这也鼓励了程序员使用组合而不是继承（条目18），而且这也是不可变类型所需要的。从这两个角度看，也算是因祸得福了。

**A second shortcoming of static factory methods is that they are hard for programmers to find. **They do not stand out in API documentation in the way that constructors do, so it can be difficult to figure out how to instantiate a class that provides static factory methods instead of constructors. The Javadoc tool may someday draw attention to static factory methods. In the meantime, you can reduce this problem by drawing attention to static factories in class or interface documentation and by adhering to common naming conventions. Here are some common names for static factory methods. This list is far from exhaustive:

**静态工厂方法的第二个不足之处是程序员难以找到他们。**它们并不像构造器那样能在API文档中突显出来，因而会有点难以知道如何初始化一个只提供静态工厂方法而不是构造器的类。也许Javadoc文档工具在未来某一天会注意到这个问题。为了减少这个问题的出现，我们可以多关注类文档或接口文档里的静态工厂，同时遵守通用的命名规则。这里列举一些通用的静态工厂方法名字。当然，这个列表并非是详尽的：

•_ from_—A type-conversion method that takes a single parameter and returns a corresponding instance of this type, for example:

•_ from_—这种方式通过给方法传入单个参数，然后返回该类型的相应实例，例如：

`Date d = Date.from(instant);`

**• **_of_—An aggregation method that takes multiple parameters and returns an instance of this type that incorporates them, for  
 example:

**• **_of_—这种方式通过给方法传入多个参数，然后返回一个包含了这些参数的该类型实例，例如：

`Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);`

• _valueOf_—A more verbose alternative to _from_ and _of_, for example:

• _valueOf_—_from_和_of_更为详细的替代方式，例如：

`BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);`

• _instance or getInstance_—Returns an instance that is described by its parameters \(if any\) but cannot be said to have the same value, for example:

• _instance or getInstance_—返回一个由参数（如果有的话）描述的实例，但不能说具有相同的值，例如：

`StackWalker luke = StackWalker.getInstance(options);`

• _create or newInstance_—Like instance or getInstance, except that the method guarantees that each call returns a new instance, for example:

• _create or newInstance_—类似于instance或getInstance, 只不过保证每次调用都返回一个新的实例，例如：

`Object newArray = Array.newInstance(classObject, arrayLen);`

• getType—Like getInstance, but used if the factory method is in a different class. Type is the type of object returned by the factory method, for example:

