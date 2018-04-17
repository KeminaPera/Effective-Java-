### 第21条：DESIGN INTERFACES FOR POSTERITY

Prior to Java 8, it was impossible to add methods to interfaces without breaking existing implementations. If you added a new method to an interface, existing implementations would, in general, lack the method, resulting in a compile-time error. In Java 8, the default method construct was added \[JLS 9.4\], with the intent of allowing the addition of methods to existing interfaces. But adding new methods to existing interfaces is fraught with risk.

在Java 8之前，若要接口里添加方法，就必须破坏现有的接口实现类。如果你往一个接口里添加了一个新的方法，一般情况下，现有的接口实现类会缺少这个方法，从而导致编译时错误。在Java 8里，添加了默认方法（default method）构造\[JLS 9.4\]，目的是为了（不破坏现有接口实现类的情况下）可以将方法加入现有接口。但是往现有接口里添加新的方法是充满风险的。

The declaration for a default method includes a default implementation that is used by all classes that implement the interface but do not implement the default method. While the addition of default methods to Java makes it possible to add methods to an existing interface, there is no guarantee that these methods will work in all preexisting implementations. Default methods are “injected” into existing implementations without the knowledge or consent of their implementors. Before Java 8, these implementations were written with the tacit understanding that their interfaces would never acquire any new methods.

一个默认方法的声明包含了一个默认的方法实现，对于所有实现了这个接口的类而言，若没有实现这个默认方法，则可以使用接口提供的这个默认实现。虽然Java加入默认方法使得我们可以往现有接口里添加方法，但并不保证这些方法会在现有的接口实现类里工作。在现有接口实现类不知道或者未经其同意的情况下，默认方法被“注入”了这些接口实现类里。在Java 8之前，这些接口实现类都是基于默认接口不会添加任何新方法的情况下编写的。

Many new default methods were added to the core collection interfaces in Java 8, primarily to facilitate the use of lambdas\(Chapter 6\). The Java libraries’ default methods are high-quality general-purpose implementations, and in most cases, they work fine. But **it is not always possible to write a default method that maintains all invariants of every conceivable implementation.**

在Java 8里，很多新的默认方法都被加入核心的集合接口里，这主要是为了促进lambda表达式的使用（见第6章）。Java类库里的默认方法是高质量的通用实现，在大多数情况下，这些方法都能良好运行。但并不总是能编写出可以维持每个想得到的接口实现类的约束性的默认方法。

For example, consider the removeIf method, which was added to the Collection interface in Java 8. This method removes all elements for which a given boolean function \(or predicate\) returns true. The default implementation is specified to traverse the collection using its iterator, invoking the predicate on each element, and using the iterator’s remove method to remove the elements for which the predicate returns true. Presumably the declaration looks something like this:

例如，考虑removeIf方法的情况，这个方法在Java 8里被添加进集合接口。这个方法给定函数（predicate）返回true时，会将相应的元素移除。默认实现是用它的迭代器去遍历集合，在每个元素上调用predicate函数，并使用迭代器的remove方法来移除调用predicate函数返回true的元素。这个声明大概是这样子的：

```
// Default method added to the Collection interface in Java 8
default boolean removeIf(Predicate<? super E> filter) {
    Objects.requireNonNull(filter);
    boolean result = false;
    for (Iterator<E> it = iterator(); it.hasNext(); ) {
        if (filter.test(it.next())) {
            it.remove();
            result = true;
        }
    } 
    return result;
}
```

This is the best general-purpose implementation one could possibly write for the removeIf method, but sadly, it fails on some real-world Collection implementations. For example, consider org.apache.commons.collections4.collection.SynchronizedCollection. This class, from the Apache Commons library, is similar to the one returned by the static factory Collections.-synchronizedCollection in java.util. The Apache version additionally provides the ability to use a client-supplied object for locking, in place of the collection. In other words, it is a wrapper class \(Item 18\), all of whose methods synchronize on a locking object before delegating to the wrapped collection.

这是能给removeIf方法写的最好的通用实现了，但遗憾的是，它在现实中的一些集合框架里是无法工作的。例如，考虑org.apache.commons.collections4.collection.SynchronizedCollection。这个来自Apache公共库的类，与java.util.SynchronizedCollection这个静态工厂返回的类相似。Apache版本额外提供了可以使用客户端提供的对象来进行锁的功能，以此替代集合。换句话说，它是个包装者类（条目18），它的所有方法在代理给被包装的集合之前都会在一个锁定的对象上进行同步。

The Apache SynchronizedCollection class is still being actively maintained, but as of this writing, it does not override the removeIf method. If this class is used in conjunction with Java 8, it will therefore inherit the default implementation of removeIf, which does not, indeed cannot, maintain the class’s fundamental promise: to automatically synchronize around each method invocation. The default implementation knows nothing about synchronization and has no access to the field that contains the locking object. If a client calls the removeIf method on a SynchronizedCollection instance in the presence of concurrent modification of the collection by another thread, a ConcurrentModificationException or other unspecified behavior may result.

Apache的SynchronizedCollection类仍然在积极维护，但在编写本文时，它仍未覆盖removeIf方法。如果这个类与Java 8一起用，那么它将继承removeIf的默认实现

In order to prevent this from happening in similar Java platform

libraries implementations, such as the package-private class

returned by Collections.synchronizedCollection, the JDK

maintainers had to override the default removeIf implementation

and other methods like it to perform the necessary synchronization

before invoking the default implementation. Preexisting collection

implementations that were not part of the Java platform did not

have the opportunity to make analogous changes in lockstep with

the interface change, and some have yet to do so.

In the presence of default methods, existing

implementations of an interface may compile without

error or warning but fail at runtime. While not terribly

common, this problem is not an isolated incident either. A handfulof the methods added to the collections interfaces in Java 8 are

known to be susceptible, and a handful of existing

implementations are known to be affected.

