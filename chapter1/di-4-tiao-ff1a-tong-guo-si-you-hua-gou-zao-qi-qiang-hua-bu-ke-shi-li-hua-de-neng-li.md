### 第4条：通过私有化构造器强化不可实例化的能力

Occasionally you’ll want to write a class that is just a grouping of static methods and static fields. Such classes have acquired a bad reputation because some people abuse them to avoid thinking in terms of objects, but they do have valid uses. They can be used to group related methods on primitive values or arrays, in the manner of _java.lang.Math_ or _java.util.Arrays_. They can also be used to group static methods, including factories \(Item 1\), for objects that implement some interface, in the manner of _java.util.Collections_. \(As of Java 8, you can also put such methods in the interface, assuming it’s yours to modify.\) Lastly, such classes can be used to group methods on a final class, since you can’t put them in a subclass.

有时你可能会想写一个只包含一组静态方法和静态属性的类。这些类的名声不是很好，因为有些人会滥用它们从而避免思考如何面向对象来编程。但即便如此，这些类也有它们自己的用处。我们可以利用这种类，以 _java.lang.Math_或_java.util.Arrays_的方式，把基本类型的值或者数组类型上的相关方法组织起来。这些类也可以通过_java.util.Collections_的方式，把实现了某些接口的对象上的静态方法，包括工厂（条目1），组织起来。（对于Java 8，你也可以将这些方法放到接口中，假定它只有你来修改。）最后，这些类也能在final类上用来组织方法，毕竟你不能将这些方法放入子类当中。

Such utility classes were not designed to be instantiated: an instance would be nonsensical. In the absence of explicit constructors, however, the compiler provides a public, parameterless default constructor. To a user, this constructor is indistinguishable from any other. It is not uncommon to see unintentionally instantiable classes in published APIs.

这种工具类被设计成了不可实例化，实例对它没有任何意义。虽然没有显示的构造器，然而，编译器提供了一个公有的无参默认构造器。对于用户，这个构造器于其它构造器没什么区别。在一些已发布的API中，我们常常可以看到一些被无意识实例化的类。

**Attempting to enforce noninstantiability by making a class abstract does not work.**The class can be subclassed and the subclass instantiated. Furthermore, it misleads the user into thinking the class was designed for inheritance \(Item 19\). There is, however, a simple idiom to ensure noninstantiability. A default constructor is generated only if a class contains no explicit constructors, soa class can be made noninstantiable by including a private constructor:





