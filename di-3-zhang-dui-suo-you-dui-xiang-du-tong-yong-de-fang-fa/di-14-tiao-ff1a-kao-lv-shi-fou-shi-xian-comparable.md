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

Compares this object with the specified object for order. Returns a negative integer, zero, or a positive integer as this object is less than, equal to, or greater than the specified object.

一个对象在与指定的对象比较顺序时，当该对象小于，等于或者大于指定的对象时，相应地返回一个负的整型值，0或者正的整型值。

Throws ClassCastException if the specified object’s type prevents it from being compared to this object.  
若指定对象的类型不能与发起比较的对象进行比较，则抛出ClassCastException异常：



