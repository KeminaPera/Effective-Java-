### 第31条：使用有限制通配符来增加API的灵活性

As noted in Item 28, parameterized types are invariant. In other words, for any two distinct types Type1 and Type2, List&lt;Type1&gt; is neither a subtype nor a supertype of List&lt;Type2&gt;. Although it is counterintuitive that List&lt;String&gt; is not a subtype of List&lt;Object&gt;, it really does make sense. You can put any object into a List&lt;Object&gt;, but you can put only strings into a List&lt;String&gt;. Since a List&lt;String&gt; can’t do everything a List&lt;Object&gt; can, it isn’t a subtype \(by the Liskov substitution principal, Item 10\).

就像条目28里提到的，参数化类型是受约束的。换句话说，对于任意两个不同的类型Type1和Type2，List&lt;Type1&gt;既不是List&lt;Type2&gt;的子类型也不是List&lt;Type2&gt;的父类型。List&lt;String&gt;不是List&lt;Object&gt;的子类型，虽然这看起来有点违反直觉，但这的确是有道理的。你可以将任意对象放入List&lt;Object&gt;，但你只能将字符串放入List&lt;String&gt;。由于List&lt;String&gt;并不具备List&lt;Object&gt;的每一项功能，所以它并不是List&lt;String&gt;并不是List&lt;Object&gt;的子类型（条目 10 中的里氏替代原则）。

Sometimes you need more flexibility than invariant typing can provide. Consider the Stack class from Item 29. To refresh your memory, here is its public API:

有时，不可变类型不足以为我们提供足够的灵活性。考虑条目29的Stack类。我们来回忆一下，下面是它的公共API：

```
public class Stack<E> {
    public Stack();
    public void push(E e);
    public E pop();
    public boolean isEmpty();
}
```

Suppose we want to add a method that takes a sequence of elements and pushes them all onto the stack. Here’s a first attempt:

假设我们需要添加一个方法，这个方法接受一系列元素并将这些元素推入栈顶。以下是第一个尝试：

```
// pushAll method without wildcard type - deficient!
public void pushAll(Iterable<E> src) {
    for (E e : src)
        push(e);
}
```

This method compiles cleanly, but it isn’t entirely satisfactory. If the element type of the Iterable src exactly matches that of the stack, it works fine. But suppose you have a Stack&lt;Number&gt; and you invoke push\(intVal\), where intVal is of type Integer. This works because Integer is a subtype of Number. So logically, it seems that this should work, too:

这个方法编译时不会有任何问题，但它并不完全满足我们的需求。如果src的元素类型与栈的元素类型相匹配，这个方法就能正确运行。但假设你有一个Stack&lt;Number&gt;并且调用了push\(intVal\)，intVal是Integer类型。这也应该能运行，因为Integer是Number的子类型。所以从逻辑上看，这么做也应该是可行的：

```
Stack<Number> numberStack = new Stack<>();
Iterable<Integer> integers = ... ;
numberStack.pushAll(integers);
```

If you try it, however, you’ll get this error message because parameterized types are invariant:

然而，假如你真这么做，你将会得到以下的错误信息，因为参数化类型是不可变的：

```
StackTest.java:7: error: incompatible types: Iterable<Integer>
cannot be converted to Iterable<Number>
numberStack.pushAll(integers);
^
```

Luckily, there’s a way out. The language provides a special kind of parameterized type call a bounded wildcard type to deal with situations like this. The type of the input parameter to pushAll should not be “Iterable of E” but “Iterable of some subtype of E,” and there is a wildcard type that means precisely that: Iterable&lt;? extends E&gt;. \(The use of the keyword extends is slightly misleading: recall from Item 29 that subtype is defined so that every type is a subtype of itself, even though it does not extend itself.\) Let’s modify pushAll to use this type:

幸运的是，有个办法可以解决这个问题。Java语言提供了一种特殊的参数化类型来处理这类情况，它就是有限制通配符类型。pushAll方法的输入参数类型不应该是“E类型的Iterable”，而应该是“E子类型的Iterable”，并且有一个通配符类型能恰当地表述这一点：Iterable&lt;? extends E&gt;。（关键字extends的使用会带点误导性：回忆一下条目29，子类型确定后，每个类型就是其自身的子类型，即便没有扩展其自身。）让我们修改下pushAll方法来用上这个类型：

```
// Wildcard type for a parameter that serves as an E producer
public void pushAll(Iterable<? extends E> src) {
    for (E e : src)
        push(e);
}
```



