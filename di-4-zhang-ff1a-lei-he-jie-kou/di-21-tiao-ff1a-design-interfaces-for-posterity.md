### 第21条：DESIGN INTERFACES FOR POSTERITY

Prior to Java 8, it was impossible to add methods to interfaces without breaking existing implementations. If you added a new method to an interface, existing implementations would, in general, lack the method, resulting in a compile-time error. In Java 8, the default method construct was added \[JLS 9.4\], with the intent of allowing the addition of methods to existing interfaces. But adding new methods to existing interfaces is fraught with risk.

在Java 8之前，若要接口里添加方法，就必须破坏现有的接口实现类。如果你往一个接口里添加了一个新的方法，一般情况下，现有的接口实现类会缺少这个方法，从而导致编译时错误。在Java 8里，添加了默认方法（default method）构造\[JLS 9.4\]，目的是为了（不破坏现有接口实现类的情况下）可以将方法加入现有接口。但是往现有接口里添加新的方法是充满风险的。

The declaration for a default method includes a default implementation that is used by all classes that implement the interface but do not implement the default method. While the addition of default methods to Java makes it possible to add methods to an existing interface, there is no guarantee that these methods will work in all preexisting implementations. Default methods are “injected” into existing implementations without the knowledge or consent of their implementors. Before Java 8, these implementations were written with the tacit understanding that their interfaces would never acquire any new methods.

一个默认方法的声明包含了一个默认的方法实现，对于所有实现了这个接口的类而言，若没有实现这个默认方法，则可以使用接口提供的这个默认实现。虽然Java加入默认方法使得我们可以往现有接口里添加方法，但并不保证这些方法会在现有的接口实现类里工作。在现有接口实现类不知道或者未经其同意的情况下，默认方法被“注入”了这些接口实现类里。在Java 8之前，这些接口实现类都是基于默认接口不会添加任何新方法的情况下编写的。

Many new default methods were added to the core collection interfaces in Java 8, primarily to facilitate the use of lambdas\(Chapter 6\). The Java libraries’ default methods are high-quality general-purpose implementations, and in most cases, they work fine. But **it is not always possible to write a default method that maintains all invariants of every conceivable implementation.**

在Java 8里，很多新的默认方法都被加入核心的集合接口里，这主要是为了促进lambda表达式的使用（见第6章）。Java类库里的默认方法是高质量的通用实现，在大多数情况下，这些方法都能良好运行。但并不总是能编写出可以维持每个想得到的接口实现类的约束性的默认方法。

For example, consider the removeIf method, which was added to the Collection interface in Java 8. This method removes all elements for which a given boolean function \(or predicate\) returns true. The default implementation is specified to traverse the collection using its iterator, invoking the predicate on each element, and using the iterator’s remove method to remove the elements for which the predicate returns true. Presumably the declaration looks something like this:

