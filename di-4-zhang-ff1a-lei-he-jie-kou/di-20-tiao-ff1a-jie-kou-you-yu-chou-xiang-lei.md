### 第20条：接口优于抽象类

Java has two mechanisms to define a type that permits multiple implementations: interfaces and abstract classes. Since the introduction of default methods for interfaces in Java 8 \[JLS 9.4.3\], both mechanisms allow you to provide implementations for some instance methods. A major difference is that to implement the type defined by an abstract class, a class must be a subclass of the abstract class. Because Java permits only single inheritance, this restriction on abstract classes severely constrains their use as type definitions. Any class that defines all the required methods and obeys the general contract is permitted to implement an interface, regardless of where the class resides in the class hierarchy.

Java有两种机制可以定义一个允许多实现的类型：接口和抽象类。由于在Java 8\[JLS 9.4.3\]中为接口引入了默认方法，所以现在两种机制都能让你为一些实例方法提供实现。一个主要的不同点是，若要实现由一个抽象类定义的类型，那么这个类必须是抽象类的一个子类。因为Java只允许单继承，所以这严格约束了抽象类作为类型定义的使用。任一定义了所有所需方法同时遵守了通用约定的类都被允许实现一个接口，而不管这个类处于类层次（class hierarchy）的哪个位置。

**Existing classes can easily be retrofitted to implement a new interface.** All you have to do is to add the required methods, if they don’t yet exist, and to add an implements clause to the class declaration. For example, many existing classes were retrofitted to implement the Comparable, Iterable, and Autocloseable interfaces when they were added to the platform. Existing classes cannot, in general, be retrofitted to extend a new abstract class. If you want to have two classes extend the same abstract class, you have to place it high up in the type hierarchy where it is an ancestor of both classes. Unfortunately, this can cause great collateral damage to the type hierarchy, forcing all descendants of the new abstract class to subclass it, whether or not it is appropriate.

**现有类可以很容易地被改造以实现一个新的接口。**你必须做的事情就是添加所需的方法，假如这些方法还没有编写的话，同时在类的声明处添加实现说明。例如，但JDK里添加了Comparable接口，Iterable接口，以及Autocloseable接口时，很多现有类都被改造并实现了它们。通常情况下，现有类无法被改造以扩展一个新的抽象类。如果你想让两个类去扩展一个相同的抽象类，就必须将这个抽象类放在类型层次（type hierarchy）的顶层，因为它是两个子类的父类。不幸的是，这样会对类型层次带来较大的附带影响，因为这样强迫了所有的后代类都要扩展这个父类，无论是否合适。

**Interfaces are ideal for defining mixins.** Loosely speaking, a mixinis is a type that a class can implement in addition to its “primary type,” to declare that it provides some optional behavior. For example, Comparable is a mixin interface that allows a class to declare that its instances are ordered with respect to other mutually comparable objects. Such an interface is called a mixin because it allows the optional functionality to be “mixed in” to the type’s primary functionality. Abstract classes can’t be used to define mixins for the same reason that they can’t be retrofitted onto existing classes: a class cannot have more than one parent, and there is no reasonable place in the class hierarchy to insert a mixin.

**接口是定义混合类型（mixins）的理想选择。**不严格地将，混合类型是指这样的一个类型：一个类除了实现它的“主类型（primary type）”，还可以实现混合类型，来声明它提供了一些额外的行为。例如，Comparable接口就是一个混合类型，它允许一个类声明其实例可以与其它可相互比较的对象进行排序。这样的一个接口我们就叫它为混合类型，因为它允许往原有类型的主要功能加入额外的功能。因为相同的原因，抽象类无法被用来定义混合类型：抽象类无法被改造到现有的类当中。一个类不能至多只能有有一个父类，而且在类层级当中也没有合理的位置来插入一个混合类型。

**Interfaces allow for the construction of nonhierarchical type frameworks. **Type hierarchies are great for organizing some things, but other things don’t fall neatly into a rigid hierarchy. For example, suppose we have an interface representing a singer and another representing a songwriter:

**接口构造非层次结构的框架。**类层次非常适合于组织一些事物，但其它有些事物并不能整齐地被组织进一个严格的层次中。例如，假设我们有一个接口，这个接口代表了一个歌手（singer），同时还有另一个代表作曲人（songwriter）的接口：

```
public interface Singer { 
    AudioClip sing(Song s);
}
public interface Songwriter {
    Song compose(int chartPosition); 
}
```

In real life, some singers are also songwriters. Because we used interfaces rather than abstract classes to define these types, it is perfectly permissible for a single class to implement both Singer and Songwriter. In fact, we can define a third interface that extends both Singer and Songwriter and adds new methods that are appropriate to the combination:

在实际生活中，一些歌手同时还是作曲人。因为定义这两个类型时我们用的是接口而不是抽象类，所以完全允许一个类同时实现Singer接口和Songwriter接口。实际上，我们可以定义一个同时扩展了Singer接口和Songwriter接口的接口，并添加适合于这种组合的方法：

```
public interface SingerSongwriter extends Singer, Songwriter { 
    AudioClip strum();
    void actSensitive();
}
```

You don’t always need this level of flexibility, but when you do, interfaces are a lifesaver. The alternative is a bloated class hierarchy containing a separate class for every supported combination of attributes. If there are n attributes in the type system, there are $$2^n$$ possible combinations that you might have to support. This is what’s known as a combinatorial explosion. Bloated class hierarchies can lead to bloated classes with many methods that differ only in the type of their arguments because there are no types in the class hierarchy to capture common behaviors.

你可能并不总是需要这样的灵活性，

**Interfaces enable safe, powerful functionality enhancements** via the wrapper class idiom \(Item 18\). If you use abstract classes to define types, you leave the programmer who wants to add functionality with no alternative but inheritance. The resulting classes are less powerful and more fragile than wrapper classes.

When there is an obvious implementation of an interface method in terms of other interface methods, consider providing implementation assistance to programmers in the form of a default method. For an example of this technique, see the removeIf method on page 104. If you provide default methods, be sure to document them for inheritance using the @implSpec Javadoc tag \(Item 19\). There are limits on how much implementation assistance you can provide with default methods. Although many interfaces specify the behavior of Object methods such as equals and hashCode, you are not permitted to provide default methods for them. Also, interfaces are not permitted to contain instance fields or nonpublic static members \(with the exception of private static methods\). Finally, you can’t add default methods to an interface that you don’t control.

You can, however, combine the advantages of interfaces and abstract classes by providing an abstract skeletal implementation class to go with an interface. The interface defines the type, perhaps providing some default methods, while the skeletal implementation class implements the remaining non-primitive interface methods atop the primitive interface methods. Extending a skeletal implementation takes most of the work out of implementing an interface. This is theTemplate Method pattern \[Gamma95\].

By convention, skeletal implementation classes are called AbstractInterface, where Interface is the name of the interface they implement. For example, the Collections Framework provides a skeletal implementation to go along with each main collection interface:AbstractCollection,AbstractSet,AbstractList, andAbstractMap. Arguably it would have made sense to call them Skeletal Collection, SkeletalSet, SkeletalList, and Skeletal Map, but the Abstract convention is now firmly established. When properly designed, skeletal implementations \(whether a separate abstract class, or consisting solely of default methods on an interface\) can make it very easy for programmers to provide their own implementations of an interface. For example, here’s a static factory method containing a complete, fully functional List implementation a top AbstractList:

```
// Concrete implementation built atop skeletal implementation
static List<Integer> intArrayAsList(int[] a) {
    Objects.requireNonNull(a);
    // The diamond operator is only legal here in Java 9 and later 
    // If you're using an earlier release, specify <Integer>
    return new AbstractList<>() {
        @Override 
        public Integer get(int i) { 
            return a[i]; // Autoboxing (Item 6)
        }
        @Override 
        public Integer set(int i, Integer val) { 
            int oldVal = a[i];
            a[i] = val; // Auto-unboxing
            return oldVal; // Autoboxing
        }
        @Override 
        public int size() {
            return a.length; }
        };
}
```

When you consider all that a List implementation does for you, this example is an impressive demonstration of the power of skeletal implementations. Incidentally, this example is an Adapter\[Gamma95\] that allows an int array to be viewed as a list of Integer instances. Because of all the translation back and forth between int values and Integer instances \(boxing and unboxing\), its performance is not terribly good. Note that the implementation takes the form of an anonymous class\(Item 24\). The beauty of skeletal implementation classes is that they provide all of the implementation assistance of abstract classes without imposing the severe constraints that abstract classes impose when they serve as type definitions. For most implementors of an interface with a skeletal implementation class, extending this class is the obvious choice, but it is strictly optional. If a class cannot be made to extend the skeletal implementation, the class can always implement the interface directly. The class still benefits from any default methods present on the interface itself. Furthermore, the skeletal implementation can still aid the implementor’s task. The class implementing the interface can forward invocations of interface methods to a contained instance of a private inner class that extends the skeletal implementation. This technique, known as simulated multiple inheritance, is closely related to the wrapper class idiom discussed inItem 18. It provides many of the benefits of multiple inheritance, while avoiding the pitfalls.

Writing a skeletal implementation is a relatively simple, if somewhat tedious, process. First, study the interface and decide which methods are the primitives in terms of which the others can be implemented. These primitives will be the abstract methods in your skeletal implementation. Next, provide default methods in the interface for all of the methods that can be implemented directly atop the primitives, but recall that you may not provide default methods for Object methods such as equals and hashCode. If the primitives and default methods cover the interface, you’re done, and have no need for a skeletal implementation class. Otherwise, write a class declared to implement the interface, with implementations of all of the remaining interface methods. The class may contain any nonpublic fields ands methods appropriate to the task.

As a simple example, consider the Map.Entry interface. The obvious primitives are getKey, getValue, and \(optionally\)setValue. The interface specifies the behavior of equals and hashCode, and there is an obvious implementation of toString in terms of the primitives. Since you are not allowed to provide default implementations for the Object methods, all implementations are placed in the skeletal implementation class:

```
// Skeletal implementation class
public abstract class AbstractMapEntry<K,V> implements Map.Entry<K,V> {
    // Entries in a modifiable map must override this method 
    @Override 
    public V setValue(V value) {
        throw new UnsupportedOperationException(); 
    }
    // Implements the general contract of Map.Entry.equals 
    @Override 
    public boolean equals(Object o) {
        if (o == this) return true;
        if (!(o instanceof Map.Entry)) return false;
        Map.Entry<?,?> e = (Map.Entry) o;
        return Objects.equals(e.getKey(), getKey()) && Objects.equals(e.getValue(), getValue());
    }
    // Implements the general contract of Map.Entry.hashCode 
    @Override 
    public int hashCode() {
        return Objects.hashCode(getKey()) ^ Objects.hashCode(getValue());
    }
    @Override 
    public String toString() { 
        return getKey() + "=" + getValue();
    } 
}
```

Note that this skeletal implementation could not be implemented in the Map.Entry interface or as a subinterface because default methods are not permitted to override Object methods such as equals, hashCode, and toString.

Because skeletal implementations are designed for inheritance, you should follow all of the design and documentation guidelines inItem 19. For brevity’s sake, the documentation comments were omitted from the previous example, but **good documentation is absolutely essential in a skeletal implementation**, whether it consists of default methods on an interface or a separate abstract class.

A minor variant on the skeletal implementation is the simple implementation, exemplified by AbstractMap. SimpleEntry. A simple implementation is like a skeletal implementation in that it implements an interface and is designed for inheritance, but it differs in that it isn’t abstract: it is the simplest possible working implementation. You can use it as it stands or subclass it as circumstances warrant.

To summarize, an interface is generally the best way to define a type that permits multiple implementations. If you export a nontrivial interface, you should strongly consider providing a skeletal implementation to go with it. To the extent possible, you should provide the skeletal implementation via default methods on the interface so that all implementors of the interface can make use of it. That said, restrictions on interfaces typically mandate that a skeletal implementation take the form of an abstract class.

