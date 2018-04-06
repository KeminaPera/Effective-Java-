### 第14条：考虑是否实现comparable

Unlike the other methods discussed in this chapter, the compareTo method is not declared in Object. Rather, it is the sole method in the Comparable interface. It is similar in character to Object’s equals method, except that it permits order comparisons in addition to simple equality comparisons, and it is generic. By implementing Comparable, a class indicates that its instances have a natural ordering. Sorting an array of objects that implement Comparable is as simple as this:

不像本章里讨论的其它方法，compareTo方法并没有在Object里被声明。想法，它是Comparable接口声明的唯一方法。compareTo方法与Object的equals方法在特征上有点类似，只是前者不仅能做是否相等的比较，还能做谁大谁小的比较，而且还是范型的。假如一个类实现了Comparable接口，那就表明了这个类的实例之间具有自然顺序（natural ordering）。对一组实现了Comparable接口的对象数组进行排序是件很简单的事：

```
Arrays.sort(a);
```

It is similarly easy to search, compute extreme values, and maintain automatically sorted collections of Comparable objects. For example, the following program, which relies on the fact that String implements Comparable, prints an alphabetized list of its command-line arguments with duplicates eliminated:

同样地，对存储在集合中的Comparable对象进行搜索，计算极限值和自动维护也是很简单的。例如，下面的程序依赖于String实现了Comparable接口，它去掉了重复的命令行参数，然后根据字母顺序打印出来：

```
public class WordList {
    public static void main(String[] args) {
        Set<String> s = new TreeSet<>(); 
        Collections.addAll(s, args); 
        System.out.println(s);
    } 
}
```

By implementing Comparable, you allow your class to interoperate with all of the many generic algorithms and collection implementations that depend on this interface. You gain a tremendous amount of power for a small amount of effort. Virtually all of the value classes in the Java platform libraries, as well as all enum types \(Item 34\), implement Comparable. If you are writing a value class with an obvious natural ordering, such as alphabetical order, numerical order, or chronological order, you should implement the Comparable interface:

通过实现Comparable接口，我们的类能与许多范型算法（generic algorithm）以及依赖于该接口的集合实现进行协作。我们只要付出点小小的努力就可以获得很多很强的功能。事实上，几乎所有的Java类库，包括枚举类型（条目34），都实现了Comparable接口。如果你正在写一个明显具有自然顺序的值类，如字母顺序，数字大小顺序或者时间先后顺序，那么你就应该实现Comparable接口：

```
public interface Comparable<T> { 
    int compareTo(T t);
}
```

The general contract of the compareTo method is similar to that of equals:

compareTo方法的通用约定与equals方法的相类似：

Compares this object with the specified object for order. Returns a negative integer, zero, or a positive integer as this object is less than, equal to, or greater than the specified object. Throws ClassCastException if the specified object’s type prevents it from being compared to this object.

一个对象在与指定的对象比较顺序时，当该对象小于，等于或者大于指定的对象时，相应地返回一个负的整型值，0或者正的整型值。若指定对象的类型不能与发起比较的对象进行比较，则抛出ClassCastException异常。

In the following description, the notation sgn\(expression\) designates the mathematical signum function, which is defined to return-1, 0, or 1, according to whether the value of expression is negative, zero, or positive.

* The implementor must ensure that sgn\(x.compareTo\(y\)\) == -sgn\(y. compareTo\(x\)\) for all x and y. \(This implies that x.compareTo\(y\) must throw an exception if and only if y.compareTo\(x\) throws an exception.\)
* The implementor must also ensure that the relation is transitive: \(x. compareTo\(y\) &gt; 0 && y.compareTo\(z\) &gt; 0\) implies x.compareTo\(z\) &gt; 0.
* Finally, the implementor must ensure that x.compareTo\(y\) == 0 implies that sgn\(x.compareTo\(z\)\) == sgn\(y.compareTo\(z\)\), for all z.
* It is strongly recommended, but not required, that\(x.compareTo\(y\) == 0\) == \(x.equals\(y\)\). Generally speaking, any class that implements the Comparable interface and violates this condition should clearly indicate this fact. The recommended language is “Note: This class has a natural ordering that is inconsistent with equals.”

在下面的表述中，符号sgn（expression）表示数学中的signum函数，返回值为-1，0，1，分别代表表达式的值为负数，0或者正数。

* 实现类必须确保对于所有的x和y，sgn\(x.compareTo\(y\)\) == -sgn\(y. compareTo\(x\)\)成立。（这意味着，当且仅当y.compareTo\(x\)抛出了异常，x.compareTo\(y\)也必须抛出异常。）
* 实现类必须确保关系的传递性：若\(x. compareTo\(y\) &gt; 0 && y.compareTo\(z\) &gt; 0\)，则x.compareTo\(z\)&gt;0。
* 最后，实现类必须确保若x.compareTo\(y\) == 0，则对于所有的z，sgn\(x.compareTo\(z\)\) == sgn\(y.compareTo\(z\)\)成立。
* 强烈建议\(x.compareTo\(y\) == 0\) == \(x.equals\(y\)\)成立，但这并不是必须的，通常来说，任何实现了Comparable接口的类如果违反了这个条件，那么应该做个说明。推荐的说法是“注意：该类具有自然排序，但是与equals方法不一致。”

Don’t be put off by the mathematical nature of this contract. Like the equals contract \(Item 10\), this contract isn’t as complicated as it looks. Unlike the equals method, which imposes a global equivalence relation on all objects, compareTo doesn’t have to work across objects of different types: when confronted with objects of different types, compareTo is permitted to throw ClassCastException. Usually, that is exactly what it does. The contract does permit intertype comparisons, which are typically defined in an interface implemented by the objects being compared. Just as a class that violates the hashCode contract can break other classes that depend on hashing, a class that violates the compareTo contract can break other classes that depend on comparison. Classes that depend on comparison include the sorted collections TreeSet and TreeMap and the utility classes Collections and Arrays, which contain searching and sorting algorithms.

不要被上述约定的数学特性给吓到了。就像equals方法的约定（条目10），这里的约定也不像它看起来的那么复杂。与equals方法不同的是，compareTo方法可以不用做跨类比较，即当遇到不同类型的对象时，compareTo方法可以抛出ClassCastException异常，而equals方法则对所有对象都做了等价关系的判断。通常，这正是compareTo在这种情况下应该做的事，约定的确是允许不同类型之间进行比较，这种比较定义在一个接口中，而被比较的两个对象则实现了这个接口。就像一个类假如违反了hashCode方法约定将破坏其它基于哈希的类一样，一个违反了compareTo方法约定的类也将破坏其它基于比较的类。基于比较的类包括一些有序集合如TreeSet和TreeMap，还有一些工具类如Collections和Arrays，它们内部都包含了查找和排序算法。

