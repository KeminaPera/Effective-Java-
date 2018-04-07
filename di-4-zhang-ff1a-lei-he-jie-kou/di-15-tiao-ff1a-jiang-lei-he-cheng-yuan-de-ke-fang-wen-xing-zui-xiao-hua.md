### 第15条：最小化类和成员的可访问性

The single most important factor that distinguishes a well-designed component from a poorly designed one is the degree to which the component hides its internal data and other implementation details from other components. A well-designed component hides all its implementation details, cleanly separating its API from its implementation. Components then communicate only through their APIs and are oblivious to each others’ inner workings. This concept, known as information hiding or encapsulation, is a fundamental tenet of software design \[Parnas72\].

要区分一个设计良好的组件和一个设计糟糕的组件，一个最重要的因素就是，这个组件将其内部的数据和其它实现细节向其它组件隐藏到了什么程度。一个设计良好的组件隐藏了它所有的实现细节，把它的API与它的实现清晰地隔离开来，然后组件之间通过它们各自的API进行交互，彼此之间不知道别的组件内部是如何工作的。这个概念被称为信息隐藏（information hiding）或者封装（encapsulation），是软件设计的一个基本宗旨\[Parnas72\]。

Information hiding is important for many reasons, most of which stem from the fact that it decouples the components that comprise a system, allowing them to be developed, tested, optimized, used, understood, and modified in isolation. This speeds up system development because components can be developed in parallel. It eases the burden of maintenance because components can be understood more quickly and debugged or replaced with little fear of harming other components. While information hiding does not, in and of itself, cause good performance, it enables effective performance tuning: once a system is complete and profiling has determined which components are causing performance problems \(Item 67\), those components can be optimized without affecting the correctness of others. Information hiding increases software reuse because components that aren’t tightly coupled often prove useful in other contexts besides the ones for which they were developed. Finally, information hiding decreases the risk in building large systems because individual components may prove successful even if the system does not.

信息隐藏之所以很重要有多方面的原因，但大多数原因都是基于这么一个事实，信息隐藏可以对组成系统的组件进行解耦，从而允许这些组件独立开发，测试，优化，使用，理解和修改。这么做加快了系统开发，因为组件可以并行开发。它还减轻了维护的负担，因为程序员可以快速理解这些组件和调试它们，而且在替换的时候不用担心会破坏其它组件。虽然信息隐藏对内对外都不会带来更好的性能，但它能让我们有效地去调节性能：在系统开发完成后，如果我们通过剖析确定了哪些组件会引起性能问题（条目67），那么这些组件可以单独优化而且不会影响其它组件的正确性。信息隐藏提高了软件的复用性，因为组件间的耦合度低这一点使得它们不仅在开发环境，而且在别的环境也能变得有用。最后，信息隐藏降低了开发大型系统的风险，因为即使系统不可用了，但这些独立的组件却有可能仍可用。

Java has many facilities to aid in information hiding. The access control mechanism \[JLS, 6.6\] specifies the accessibility of classes, interfaces, and members. The accessibility of an entity is determined by the location of its declaration and by which, if any, of the access modifiers \(private, protected, and public\) is present on the declaration. Proper use of these modifiers is essential to information hiding.

Java有很多机制来帮助我们进行信息隐藏。访问控制机制\[JLS, 6.6\]指定了类，接口和成员的可访问性。一个实体的可访问性由它的声明位置以及声明中出现的访问修饰符（private，protected，和public）来确定（如果有的话）。正确地使用这些修饰符对于信息隐藏是很重要的。

The rule of thumb is simple: **make each class or member as inaccessible as possible.**In other words, use the lowest possible access level consistent with the proper functioning of the software that you are writing.

经验法则很简单：**尽可能地让每个类或者成员不可被外界访问。**换句话说，在与我们编写的软件的对应功能保持一致的情况下，我们应该使用最低的访问级别。

For top-level \(non-nested\) classes and interfaces, there are only two possible access levels: package-private and public. If you declare a top-level class or interface with the public modifier, it will be public; otherwise, it will be package-private. If a top-level class or interface can be made package-private, it should be. By making it package-private, you make it part of the implementation rather than the exported API, and you can modify it, replace it, or eliminate it in a subsequent release without fear of harming existing clients. If you make it public, you are obligated to support it forever to maintain compatibility.

对于顶层（非嵌套的）类和接口，只有两种可能的访问级别：包级私有（package-private）和公有（public）。如果你用public修饰符声明了一个顶层类或者接口，那么它将是公有的；否则，它将是包级私有的。如果一个顶层类或接口可以做成包级私有的，那么就应该是包私有的。通过把它做成包级私有，我们就把它做成了实现的一部分，而不是导出的API，而且我们即使在接下来的发行版本中修改它，替换它或者删除它，也不用担心会影响到现存的客户端。如果我们把它做成了公有，那么为了维护兼容性，我们将不得不长期去支持它。

If a package-private top-level class or interface is used by only one class, consider making the top-level class a private static nested class of the sole class that uses it \(Item 24\). This reduces its accessibility from all the classes in its package to the one class that uses it. But it is far more important to reduce the accessibility of a gratuitously public class than of a package-private top-level class: the public class is part of the package’s API, while the package-private top-level class is already part of its implementation.

如果一个包级私有的顶成类或接口只被一个类使用，那么可以考虑将这个顶层类或接口做成那个唯一使用它的类的私有静态内部类（条目24）。这么做可以将它的可访问性从它所处包下的所有类减少到那个使用它的类。然而，降低不必要的公有类的可访问性比降低包级私有顶层类的可访问性要重要得多：公有类是包的API的一部分，而包级私有顶层类已经是这个包的实现的一部分。

For members \(fields, methods, nested classes, and nested interfaces\), there are four possible access levels, listed here in order of increasing accessibility:

* private—The member is accessible only from the top-level class where it is declared.
* package-private—The member is accessible from any class in the package where it is declared. Technically known as default access, this is the access level you get if no access modifier is specified \(except for interface members, which are public by default\).
* protected—The member is accessible from subclasses of the class where it is declared \(subject to a few restrictions \[JLS, 6.6.2\]\) and from any class in the package where it is declared.
* public—The member is accessible from anywhere.

对于成员（域，方法，嵌套类，或者嵌套接口），都有四种可能的访问级别，下面按照可访问性递增的顺序罗列了出来：

* private—成员只能被声明它的顶级类访问。
* package-private—成员可以被声明它的包下面的所有类访问。从技术上讲，它被称为缺省访问权限（default access），当没有指定访问修饰符时，我们就会得到这种访问级别（接口成员除外，它们默认是公有的）。
* protected—成员可以被声明它的类的子类访问（但有一些限制\[JLS, 6.6.2\]），同时，声明它的包下面的所有类也可以访问它。
* public—成员可以在任何地方被访问。

After carefully designing your class’s public API, your reflex should be to make all other members private. Only if another class in the same package really needs to access a member should you remove the private modifier, making the member package-private. If you find yourself doing this often, you should reexamine the design of your system to see if another decomposition might yield classes that are better decoupled from one another. That said, both private and package-private members are part of a class’s implementation and do not normally impact its exported API. These fields can, however, “leak” into the exported API if the class implements Serializable\(Items 86and87\).

For members of public classes, a huge increase in accessibility occurs when the access level goes from package-private to protected. A protected member is part of the class’s exported API and must be supported forever. Also, a protected member of an exported class represents a public commitment to an implementation detail \(Item 19\). The need for protected members should be relatively rare.

There is a key rule that restricts your ability to reduce the accessibility of methods. If a method overrides a superclass method, it cannot have a more restrictive access level in the subclass than in the superclass \[JLS, 8.4.8.3\]. This is necessary to ensure that an instance of the subclass is usable anywhere that an instance of the superclass is usable \(the Liskov substitution principle, seeItem 15\). If you violate this rule, the compiler will generate an error message when you try to compile the subclass. A special case of this rule is that if a class implements an interface, all of the class methods that are in the interface must be declared public in the class.

To facilitate testing your code, you may be tempted to make a class, interface, or member more accessible than otherwise necessary. This is fine up to a point. It is acceptable to make a private member of a public class package-private in order to test it, but it is not acceptable to raise the accessibility any higher. In other words, it is not acceptable to make a class, interface, or member a part of a pack-age’s exported API to facilitate testing. Luckily, it isn’t necessary either because tests can be made to run as part of the package being tested, thus gaining access to its package-private elements.

**Instance fields of public classes should rarely be public**\(Item 16\). If an instance field is nonfinal or is a reference to a mutable object, then by making it public, you give up the ability to limit the values that can be stored in the field. This means you give up the ability to enforce invariants involving the field. Also, you give up the ability to take any action when the field is modified, so **classes with public mutable fields are not generally thread-safe. **Even if a field is final and refers to an immutable object, by making it public you give up the flexibility to switch to a new internal data representation in which the field does not exist.

The same advice applies to static fields, with one exception. You can expose constants via public static final fields, assuming the constants form an integral part of the abstraction provided by the class. By convention, such fields have names consisting of capital letters, with words separated by underscores \(Item 68\). It is critical that these fields contain either primitive values or references to immutable objects \(Item 17\). a field containing a reference to a mutable object has all the disadvantages of a nonfinal field. While the reference cannot be modified, the referenced object can be modified—with disastrous results.

Note that a nonzero-length array is always mutable, so it is wrong for a class to have a public static final array field, or an accessor that returns such a field.If a class has such a field or accessor, clients will be able to modify the contents of the array. This is a frequent source of security holes:

```
// Potential security hole!
public static final Thing[] VALUES = { ... };
```

Beware of the fact that some IDEs generate accessors that return references to private array fields, resulting in exactly this problem. There are two ways to fix the problem. You can make the public array private and add a public immutable list:

```
private static final Thing[] PRIVATE_VALUES = { ... }; 
public static final List<Thing> VALUES = Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES)) ;
```

Alternatively, you can make the array private and add a public method that returns a copy of a private array:

```
private static final Thing[] PRIVATE_VALUES = { ... }; 
public static final Thing[] values() {
    return PRIVATE_VALUES.clone(); 
}
```

To choose between these alternatives, think about what the client is likely to do with the result. Which return type will be more convenient? Which will give better performance?

As of Java 9, there are two additional, implicit access levels introduced as part of the module system. A module is a grouping of packages, like a package is a grouping of classes. A module may explicitly export some of its packages via export declarations in its module declaration\(which is by convention contained in a source file named module-info.java\). Public and protected members of unexported packages in a module are inaccessible outside the module; within the module, accessibility is unaffected by export declarations. Using the module system allows you to share classes among packages within a module without making them visible to the entire world. Public and protected members of public classes in unexported packages give rise to the two implicit access levels, which are intramodular analogues of the normal public and protected levels. The need for this kind of sharing is relatively rare and can often be eliminated by rearranging the classes within your packages.

Unlike the four main access levels, the two module-based levels are largely advisory. If you place a module’s JAR file on your application’s class path instead of its module path, the packages in the module revert to their non-modular behavior: all of the public and protected members of the packages’ public classes have their normal accessibility, regardless of whether the packages are exported by the module \[Reinhold, 1.2\]. The one place where the newly introduced access levels are strictly enforced is the JDK itself: the unexported packages in the Java libraries are truly inaccessible outside of their modules.

Not only is the access protection afforded by modules of limited utility to the typical Java programmer, and largely advisory in nature; in order to take advantage of it, you must group your packages into modules, make all of their dependencies explicit in module declarations, rearrange your source tree, and take special actions to accommodate any access to non-modularized packages from within your modules \[Reinhold, 3\]. It is too early to say whether modules will achieve widespread use outside of the JDK itself. In the meantime, it seems best to avoid them unless you have a compelling need.

To summarize, you should reduce accessibility of program elements as much as possible \(within reason\). After carefully designing a minimal public API, you should prevent any stray classes, interfaces, or members from becoming part of the API. With the exception of public static final fields, which serve as constants, public classes should have no public fields. Ensure that objects referenced by public static final fields are immutable.

