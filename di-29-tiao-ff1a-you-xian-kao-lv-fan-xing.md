### 第29条：优先考虑泛型

It is generally not too difficult to parameterize your declarations and make use of the generic types and methods provided by the JDK. Writing your own generic types is a bit more difficult, but it’s worth the effort to learn how.



Consider the simple \(toy\) stack implementation fromItem 7:

```
// Object-based collection - a prime candidate for generics
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;
    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }
    public void push(Object e) { 
        ensureCapacity(); elements[size++] = e;
    }
    public Object pop() { 
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null; // Eliminate obsolete reference return result;
    }
    public boolean isEmpty() { 
        return size == 0;
    }
    private void ensureCapacity() { 
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1); 
    }
}
```



