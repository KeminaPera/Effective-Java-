### 第24条：优先考虑静态成员类

A nested class is a class defined within another class. A nested class should exist only to serve its enclosing class. If a nested class would be useful in some other context, then it should be a top-level class. There are four kinds of nested classes: static member classes, nonstatic member classes, anonymous classes, and local classes. All but the first kind are known as inner classes. This item tells you when to use which kind of nested class and why.

嵌套类是定义在另一个类中的类。一个嵌套类的存在应该是为了服务它的外围类。如果一个嵌套类还可以用于别的上下文，那么它就应该是个顶层类（top-level class）。有4种嵌套类：静态成员类，非静态成员类，匿名类以及局部类。除了第一种，其余的都被称为内部类。本条目将告诉你何时用哪种嵌套类以及这么做的原因。

A static member class is the simplest kind of nested class. It is best thought of as an ordinary class that happens to be declared inside another class and has access to all of the enclosing class’s members, even those declared private. A static member class is a static member of its enclosing class and obeys the same accessibility rules as other static members. If it is declared private, it is accessible only within the enclosing class, and so forth.

静态成员类是最简单的嵌套类。我们可以把它看成是一个普通的类，只是这个类恰好在别的类的内部声明，而且可以访问外围类的所有成员，即使是那些私有成员。静态成员类是它的外围类的静态成员，与其它的静态成员一样有着相同的可访问性。如果它被声明成私有的，那么它就只能在它的外围类里被访问，等等。

One common use of a static member class is as a public helper class, useful only in conjunction with its outer class. For example, consider an enum describing the operations supported by a calculator \(Item 34\). The Operation enum should be a public static member class of the Calculator class. Clients of Calculator could then refer to operations using names like Calculator.Operation.PLUS and Calculator.Operation.MINUS.

静态成员类通常的一个用法是作为一个公有的辅助类，只有与它的外围类一起用时才有意义。例如，考虑一个枚举，它描述了计算器所支持的所有操作（条目34）。操作枚举应该是作为Calculator类的一个公有静态成员类。然后Calculator的客户端可以通过使用名字如Calculator.Operation.PLUS和Calculator.Operations.MINUS来引用操作。

Syntactically, the only difference between static and nonstatic member classes is that static member classes have the modifier static in their declarations. Despite the syntactic similarity, these two kinds of nested classes are very different. Each instance of a nonstatic member class is implicitly associated with an enclosing instance of its containing class. Within instance methods of a nonstatic member class, you can invoke methods on the enclosing instance or obtain a reference to the enclosing instance using the qualified this construct \[JLS, 15.8.4\]. If an instance of a nested class can exist in isolation from an instance of its enclosing class, then the nested class must be a static member class: it is impossible to create an instance of a nonstatic member class without an enclosing instance.

从语法上来说，静态成员类和非静态成员类之间的区别是，静态成员类在声明上有static标识符。这两种嵌套类虽然语法上很相似，这但彼此之间却有很大的不同。非静态成员类的每个实例都是隐式地和它的外围类的实例关联在一起。在非静态成员类的实例方法内部，你可以调用外围实例的方法，或者通过标识了this的构造来获取外围实例的引用。

The association between a nonstatic member class instance and its enclosing instance is established when the member class instance is created and cannot be modified thereafter. Normally, the association is established automatically by invoking a nonstatic member class constructor from within an instance method of the enclosing class. It is possible, though rare, to establish the association manually using the expression enclosingInstance.new MemberClass\(args\). As you would expect, the association takes up space in the nonstatic member class instance and adds time to its construction.

在非静态成员类实例被创建时，非静态成员类实例和它的外围实例的关联就被建立了，而且建立后就不能被修改了。一般情况下，在外围类的实例方法内部调用非静态成员类的构造器时，这种关联就被自动建立。虽然可以手动使用表达式enclosingInstance.new MemberClass\(args\)来建立这个关联，但是很少这么做。正如你所料，这个关联需要消耗非静态成员类实例的空间，而且增加了它的构建时间。

One common use of a nonstatic member class is to define an Adapter \[Gamma95\] that allows an instance of the outer class to be viewed as an instance of some unrelated class. For example, implementations of the Map interface typically use nonstatic member classes to implement their collection views, which are returned by Map’s keySet, entrySet, and values methods. Similarly, implementations of the collection interfaces, such as Set and List, typically use nonstatic member classes to implement their iterators:

非静态成员类的一个通常的用法是，定义一个适配器 \[Gamma95\]，而这个适配器让外围类的实例被看成是某个不相关类的实例。例如，Map接口的实现通常会使用非静态成员类来实现它们的集合视图，这些视图由Map的keySet方法，entrySet方法以及值方法返回。类似地，一些集合接口（如Set和List）的实现通常用非静态成员类来实现它们的迭代器：

```
// Typical use of a nonstatic member class
public class MySet<E> extends AbstractSet<E> {
... // Bulk of the class omitted
    @Override 
    public Iterator<E> iterator() {
        return new MyIterator();
    } 
    private class MyIterator implements Iterator<E> {
        ...
    }
}
```

**If you declare a member class that does not require access to an enclosing instance, always put the static modifier in its declaration**, making it a static rather than a nonstatic member class. If you omit this modifier, each instance will have a hidden extraneous reference to its enclosing instance. As previously mentioned, storing this reference takes time and space. More seriously, it can result in the enclosing instance being retained when it would otherwise be eligible for garbage collection \(Item 7\). The resulting memory leak can be catastrophic. It is often difficult to detect because the reference is invisible.

**如果你声明了一个不需要访问外围实例的成员类，那你总是应该static修饰符加到声明里去**，使得这个成员类是静态的。如果你不加这个修饰符，那么每个实例都将包含一个隐藏的外围实例的引用。就如前面说的那样，存储这个引用将耗费时间和空间。更严重的是，当这个外围实例已经满足垃圾回收（条目7）的条件时，非静态成员类实例会导致外围实例被保留。因此而导致的内存泄露是灾难性的。由于这个引用是不可见的，所以这个问题也通常很难被检测到。

A common use of private static member classes is to represent components of the object represented by their enclosing class. For example, consider a Map instance, which associates keys with values. Many Map implementations have an internal Entry object for each key-value pair in the map. While each entry is associated with a map, the methods on an entry \(getKey, getValue, and setValue\) do not need access to the map. Therefore, it would be wasteful to use a nonstatic member class to represent entries: a private static member class is best. If you accidentally omit the static modifier in the entry declaration, the map will still work, but each entry will contain a superfluous reference to the map, which wastes space and time.

私有静态成员类通常被用来展示代表外围类对象的组件。例如，考虑一个Map实例，在这个实例里，每个键关联一个值。在许多Map实现里，每个键值对都有一个对应的内部Entry对象。虽然每个Entry对象都与一个Map对象关联，但是Entry对象的方法（getKey，getValue和setValue）无需访问Map对象。因而，采用非静态成员类来代表Entry对象是很浪费的，使用私有静态成员类会更好。如果你不小心忘了在Entry的声明里添加static修饰符，该Map仍然可以工作，但每个Entry对象将包含多余的Map对象引用，浪费空间又浪费时间。

It is doubly important to choose correctly between a static and a nonstatic member class if the class in question is a public or protected member of an exported class. In this case, the member class is an exported API element and cannot be changed from a nonstatic to a static member class in a subsequent release without violating backward compatibility.

如果所讨论的类是某个导出类的公有或受保护成员，那么选择让它成为静态成员类还是非静态成员类是非常重要的。在这种情况下，成员类是导出API的元素，

As you would expect, an anonymous class has no name. It is not a member of its enclosing class. Rather than being declared along with other members, it is simultaneously declared and instantiated at the point of use. Anonymous classes are permitted at any point in the code where an expression is legal. Anonymous classes have enclosing instances if and only if they occur in a nonstatic context. But even if they occur in a static context, they cannot have anystatic members other than constant variables, which are final primitive or string fields initialized to constant expressions \[JLS, 4.12.4\].

