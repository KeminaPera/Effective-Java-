### 第18条：组合优先于继承

Inheritance is a powerful way to achieve code reuse, but it is not always the best tool for the job. Used inappropriately, it leads to fragile software. It is safe to use inheritance within a package, where the subclass and the superclass implementations are under the control of the same programmers. It is also safe to use inheritance when extending classes specifically designed and documented for extension \(Item 19\). Inheriting from ordinary concrete classes across package boundaries, however, is dangerous. As a reminder, this book uses the word “inheritance” to mean implementation inheritance \(when one class extends another\). The problems discussed in this item do not apply to interface inheritance\(when a class implements an interface or when one interface extends another\).

继承（inheritance）是获得代码重用的一种有效途径，但不是最佳方式。使用不当的话，容易使做出来的软件变得脆弱。在一个包里使用继承是安全的，这样的话子类实现和父类实现都在同个程序员的控制之下。除此之外，当类是专门设计来继承而且具有良好文档话，采用继承来进行扩展也是安全的。但是，如果只是跨包继承一个普通具体的类，则是危险的。提示一下，本书采用“继承”来指实现继承（当一个类扩展了另一个类）。本条目讨论的问题不适用于接口继承（当一个类实现了一个接口或者一个接口扩展了另一个接口）。

**Unlike method invocation, inheritance violates encapsulation** \[Snyder86\]. In other words, a subclass depends on the implementation details of its superclass for its proper function. The superclass’s implementation may change from release to release, and if it does, the subclass may break, even though its code has not been touched. As a consequence, a subclass must evolve in tandem with its superclass, unless the superclass’s authors have designed and documented it specifically for the purpose of being extended.

**与方法调用不同的是，继承违反了封装原则**\[Snyder86\]。换句话说，一个子类依赖于它的父类的实现细节来实现它本身的功能。但随着版本的更新，父类的实现会产生变化，一旦这种情况发生了，子类将会被破坏，即便子类的代码没有变更过。这样产生的后果是，子类必须跟着父类一起演化，除非编写父类的作者本来就是为了专门将其设计成被继承，同时还准备了良好的文档。

To make this concrete, let’s suppose we have a program that uses a HashSet. To tune the performance of our program, we need to query the HashSet as to how many elements have been added since it was created \(not to be confused with its current size, which goes down when an element is removed\). To provide this functionality, we write a HashSet variant that keeps count of the number of attempted element insertions and exports an accessor for this count. The HashSet class contains two methods capable of adding elements, add and addAll, so we override both of these methods:

为了更具体地说明这一点，我们假设我们有一个程序，这个程序用了HashSet。为了调优程序的性能，我们需要查一下这个HashSet自从被创建以来添加了多少个元素进去（不要跟当前的大小混淆起来，元素的数目会随着删除而减少）。为了实现这个功能，我们写了一个HashSet变量，它记录着尝试插入的元素的数目，同时为这个数目提供一个访问方法。HashSet类包含了两个用来添加元素的方法，add和addAll，所以我们覆盖这两个方法：

```
// Broken - Inappropriate use of inheritance!
public class InstrumentedHashSet<E> extends HashSet<E> {
    // The number of attempted element insertions
    private int addCount = 0;
    public InstrumentedHashSet() {
    } 
    public InstrumentedHashSet(int initCap, float loadFactor) {
        super(initCap, loadFactor);
    } 
    @Override 
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    } 
    @Override 
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    } 
    public int getAddCount() {
        return addCount;
    }
}
```

This class looks reasonable, but it doesn’t work. Suppose we create an instance and add three elements using the addAll method. Incidentally, note that we create a list using the static factory method List.of, which was added in Java 9; if you’re using an earlier release, use Arrays.asList instead:

上述这个类看起来是合理的，但它无法工作。假设我们创建了一个实例并通过addAll方法添加了三个元素进去。顺便说一下，注意到我们创建列表时，使用了Java 9就开始添加进来的静态工厂方法List.of。如果你使用了更早的JDK版本，请用Arrays.asList来替换：

```
InstrumentedHashSet<String> s = new InstrumentedHashSet<>();
s.addAll(List.of("Snap", "Crackle", "Pop"));
```

We would expect the getAddCount method to return three at this point, but it returns six. What went wrong? Internally, HashSet’s addAll method is implemented on top of its add method, although HashSet, quite reasonably, does not document this implementation detail. The addAll method in InstrumentedHashSet added three to addCount and then invoked HashSet’s addAll implementation using super.addAll. This in turn invoked the add method, as overridden in InstrumentedHashSet, once for each element. Each of these three invocations added one more to addCount, for a total increase of six: each element added with the addAll method is double-counted.

此时，我们期待着getAddCount方法会返回3，但实际上它返回了6。哪里出错了？在HashSet内部，addAll方法是基于它的add方法来实现的，虽然HashSet并没有在文档里说明这个实现细节，但这是合理的。InstrumentedHashSet的addAll方法里

