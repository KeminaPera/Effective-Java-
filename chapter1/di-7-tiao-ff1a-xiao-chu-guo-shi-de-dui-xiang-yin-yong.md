### 第7条：消除过时的对象引用

If you switched from a language with manual memory management, such as C or C++, to a garbage-collected language such as Java, your job as a programmer was made much easier by the fact that your objects are automatically reclaimed when you’re through with them. It seems almost like magic when you first experience it. It can easily lead to the impression that you don’t have to think about memory management, but this isn’t quite true. Consider the following simple stack implementation:

如果你从需要手动管理内存的语言，如C或C++，转换到自带垃圾回收机制的语言，如Java，你的编码工作变得更容易了，因为当你用了对象后，它们将会被自动回收。当你第一次体验这种机制时，可能会觉得这很神奇。这可能会让你以为不用再考虑内存管理了，但事实并不完全是这样的。让我们来看看下面一个例子，它是个栈的简单实现：

```
// Can you spot the "memory leak"?
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;
    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    } 
    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    } 
    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        return elements[--size];
    } 
    /**
     * Ensure space for at least one more element, roughly
     * doubling the capacity each time the array needs to grow.
     */
    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```

There’s nothing obviously wrong with this program \(but see Item 29 for a generic version\). You could test it exhaustively, and it would pass every test with flying colors, but there’s a problem lurking. Loosely speaking, the program has a “memory leak,” which can silently manifest itself as reduced performance due to increased garbage collector activity or increased memory footprint. In extreme cases, such memory leaks can cause disk paging and even program failure with an _OutOfMemoryError_, but such failures are relatively rare.

这段代码并没有明显的错误（它的泛型版本可参见条目29）。无论你如何测试它，它都能通过每一次测试，但这里面潜伏着一个问题。不严格地讲，这段程序里面有个“内存泄露”的问题，随着垃圾回收活动的增加或者内存占用的增多，将会逐渐显现出性能下降的现象。在极端的情况下，这种内存泄露会引发磁盘分页（disk paging），甚至还会导致程序失败并抛出_OutOfMemoryError_错误，但这种错误并不多见。

So where is the memory leak? If a stack grows and then shrinks, the objects that were popped off the stack will not be garbage collected, even if the program using the stack has no more references to them. This is because the stack maintains _obsolete references_ to these objects. An obsolete reference is simply a reference that will never be dereferenced again. In this case, any references outside of the “active portion” of the element array are obsolete. The active portion consists of the elements whose index is less than size.

所以代码中内存泄露的部分在哪里？如果一个栈先增长后收缩，那些被弹出栈的对象将不会被回收，即使使用栈的程序不再引用它们。这是因为栈里面包含着这些对象的过期引用（_obsolete reference_）。过期引用是指永远不会被再次解除的引用。在这个例子当中，任何在elements数组的活动区域（active portion）之外的引用都是过期的。活动部分由elements数组里面下标小于数据长度的元素组成。

Memory leaks in garbage-collected languages \(more properly known as unintentional object retentions\) are insidious. If an object reference is unintentionally retained, not only is that object excluded from garbage collection, but so too are any objects referenced by that object, and so on. Even if only a few object references are unintentionally retained, many, many objects may be prevented from being garbage collected, with potentially large effects on performance.

在支持垃圾回收的语言中，内存泄露的问题（更确切地说，是无意的对象保留）是很隐蔽的。如果一个对象的引用被无意保留了，不仅这个对象无法被回收，其它被这个对象引用的对象也无法被回收。即使只有少数几个对象引用被无意保留了，那么许许多多的对象也将跟着无法被回收，这里面潜伏着对性能重大的影响。

The fix for this sort of problem is simple: null out references once they become obsolete. In the case of our Stack class, the reference to an item becomes obsolete as soon as it’s popped off the stack. The corrected version of the _pop_ method looks like this:

这类问题的解决办法很简单：一旦一个对象过时了，只需清空对象引用就可以了。在上述的例子当中，栈里面的元素一旦被弹出，其引用就已经过时了。_pop_方法的正确版本应该像这样：

```
public Object pop() {
    if (size == 0)
        throw new EmptyStackException();
    Object result = elements[--size];
    elements[size] = null; // Eliminate obsolete reference
    return result;
}
```

An added benefit of nulling out obsolete references is that if they are subsequently dereferenced by mistake, the program will immediately fail with a _NullPointerException_, rather than quietly doing the wrong thing. It is always beneficial to detect programming errors as quickly as possible.

清空引用的一个额外好处是，假如它们后来又被误解除引用，程序将会立即抛出_NullPointerException_异常，而不是默默地错误下去。这么做通常也有利于快速地查出错误。

When programmers are first stung by this problem, they may overcompensate by nulling out every object reference as soon as the program is finished using it. This is neither necessary nor desirable; it clutters up the program unnecessarily. **Nulling out object references should be the exception rather than the norm. **The best way to eliminate an obsolete reference is to let the variable that contained the reference fall out of scope. This occurs naturally if you define each variable in the narrowest possible scope \(Item 57\).

程序员第一次被内存泄露的问题困扰时，他们往往会过分小心，当每个对象引用用完之后就立即清空掉。其实这样做既没必要也不需要，这么做会给程序带来不必要的混乱。**清空对象应该是例外而不是规范行为。**消除过期引用最佳的方式是让包含这个引用的变量离开作用域。如果我们将每个变量定义在最小的作用域里，那么这种机制就会自然生效。

So when should you null out a reference? What aspect of the _Stack_ class makes it susceptible to memory leaks? Simply put, it manages its own memory. The storage pool consists of the elements of the elements array \(the object reference cells, not the objects themselves\). The elements in the active portion of the array \(as defined earlier\) are allocated, and those in the remainder of the array are free. The garbage collector has no way of knowing this; to the garbage collector, all of the object references in the elements array are equally valid. Only the programmer knows that the inactive portion of the array is unimportant. The programmer effectively communicates this fact to the garbage collector by manually nulling out array elements as soon as they become part of the inactive portion.

所以我们什么时候应该清空一个引用？_Stack_类的哪个地方使得它有可能发生内存泄露？简单来说，因为它自己管理它自己的内存。存储池里都是elements数组里的元素（对象引用单元，而不是对象本身）。数组里活动区域的元素都是已分配的，数组其余部分的元素都是自由的。垃圾回收器无法知道这一点，因为对于垃圾回收器，elements数组里的所有对象引用都是等同的。只有程序员才知道数组非活动区域里的元素是不重要的。当某些元素变成非活动区域的一部分时，程序员可以立即手动将其清空。通过这种方式，程序员可以有效地告诉垃圾回收器可以回收了。

Generally speaking, **whenever a class manages its own memory, the programmer should be alert for memory leaks.** Whenever an element is freed, any object references contained in the element should be nulled out.

一般来说，**每当一个类自己管理它的内存时，程序员就要小心内存泄露的问题了。**无论什么时候，只要一个元素被释放了，这个元素包含的所有引用都应该清空。

Another common source of memory leaks is caches. Once you put an object reference into a cache, it’s easy to forget that it’s there and leave it in the cache long after it becomes irrelevant. There are several solutions to this problem. If you’re lucky enough to implement a cache for which an entry is relevant exactly so long as there are references to its key outside of the cache, represent the cache as a WeakHashMap; entries will be removed automatically after they become obsolete. Remember that WeakHashMap is useful only if the desired lifetime of cache entries is determined by external references to the key, not the value.

缓存是内存泄露的另一个常见来源。当你往缓存里放了一个对象引用，就很容易忘记它还在那，使得它即使不再有用后还很长一段时间留在那里。有几个办法可以解决这个问题。如果你刚好要实现这么一个缓存，只要缓存之外存在只对某个key的引用，相应的项就有意义，那么就可以用WeakHashMap来充当这个缓存。但缓存中的项过期后，它们就会被自动清除。不过我们要记住，只有当所要的缓存项的生命周期是由该键的外部引用而不是值来决定时，WeakHashMap才有用。



