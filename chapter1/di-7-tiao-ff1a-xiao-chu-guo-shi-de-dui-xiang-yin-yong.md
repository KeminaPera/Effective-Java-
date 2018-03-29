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

There’s nothing obviously wrong with this program \(but see Item 29 for a generic version\). You could test it exhaustively, and it would pass every test with flying colors, but there’s a problem lurking. Loosely speaking, the program has a “memory leak,” which can silently manifest itself as reduced performance due to increased garbage collector activity or increased memory footprint. In extreme cases, such memory leaks can cause diskpaging and even program failure with an OutOfMemoryError, but such failures are relatively rare.

