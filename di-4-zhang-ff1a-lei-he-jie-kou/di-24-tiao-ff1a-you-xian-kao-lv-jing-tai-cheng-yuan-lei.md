### 第24条：优先考虑静态成员类

A nested class is a class defined within another class. A nested class should exist only to serve its enclosing class. If a nested class would be useful in some other context, then it should be a top-level class. There are four kinds of nested classes: static member classes, nonstatic member classes, anonymous classes, and local classes. All but the first kind are known as inner classes. This item tells you when to use which kind of nested class and why.

嵌套类是定义在另一个类中的类。一个嵌套类的存在应该是为了服务它的外围类。如果一个嵌套类还可以用于别的上下文，那么它就应该是个顶层类（top-level class）。有4种嵌套类：静态成员类，非静态成员类，匿名类以及局部类。除了第一种，其余的都被称为内部类。本条目将告诉你何时用哪种嵌套类以及这么做的原因。

A static member class is the simplest kind of nested class. It is best thought of as an ordinary class that happens to be declared inside another class and has access to all of the enclosing class’s members, even those declared private. A static member class is a static member of its enclosing class and obeys the same accessibility rules as other static members. If it is declared private, it is accessible only within the enclosing class, and so forth.

静态成员类是最简单的嵌套类。我们可以把它看成是一个普通的类，只是这个类恰好在别的类的内部声明，而且可以访问外围类的所有成员，即使是那些私有成员。静态成员类是它的外围类的静态成员，与其它的静态成员一样有着相同的可访问性。如果它被声明成私有的，那么它就只能在它的外围类里被访问，等等。

One common use of a static member class is as a public helper class, useful only in conjunction with its outer class. For example, consider an enum describing the operations supported by a calculator \(Item 34\). The Operation enum should be a public static member class of the Calculator class. Clients of Calculator could then refer to operations using names like Calculator.Operation.PLUS and Calculator.Operation.MINUS.

静态成员类通常的一个用法是作为一个公有的辅助类，只有与它的外围类一起用时才有意义。例如，考虑一个枚举，它描述了计算器所支持的所有操作（条目34）。操作枚举应该是作为Calculator类的一个公有静态成员类。然后Calculator的客户端可以通过使用名字如Calculator.Operation.PLUS和Calculator.Operations.MINUS来引用操作。

Syntactically, the only difference between static and nonstatic member classes is that static member classes have the modifier static in their declarations. Despite the syntactic similarity, these two kinds of nested classes are very different. Each instance of a nonstatic member class is implicitly associated with an enclosing instance of its containing class. Within instance methods of a nonstatic member class, you can invoke methods on the enclosing instance or obtain a reference to the enclosing instance using the qualified this construct \[JLS, 15.8.4\]. If an instance of a nested class can exist in isolation from an instance of its enclosing class, then the nested class must be a static member class: it is impossible to create an instance of a nonstatic member class without an enclosing instance.

从语法上来说，静态成员类和非静态成员类之间的区别是，静态成员类在声明上有static标识符。这两种嵌套类虽然语法上很相似，这但彼此之间却有很大的不同。非静态成员类的每个实例都是隐式地和它的外围类的实例绑定在一起。在非静态成员类的实例方法内部，你可以调用外围实例的方法，或者通过标识了this的构造来获取外围实例的引用。

The association between a nonstatic member class instance and its

enclosing instance is established when the member class instance

is created and cannot be modified thereafter. Normally, the

association is established automatically by invoking a nonstaticmember class constructor from within an instance method of the

enclosing class. It is possible, though rare, to establish the

association manually using the expression enclosingInstance.new

MemberClass\(args\). As you would expect, the association takes up

space in the nonstatic member class instance and adds time to its

construction.

One common use of a nonstatic member class is to define

an Adapter \[Gamma95\] that allows an instance of the outer class

to be viewed as an instance of some unrelated class. For example,

implementations of the Map interface typically use nonstatic

member classes to implement their collection views, which are

returned by Map’s keySet, entrySet, and values methods. Similarly,

implementations of the collection interfaces, such as Set and List,

typically use nonstatic member classes to implement their

iterators:

