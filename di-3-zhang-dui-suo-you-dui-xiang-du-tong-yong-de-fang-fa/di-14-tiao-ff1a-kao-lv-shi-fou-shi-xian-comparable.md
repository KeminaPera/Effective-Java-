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

Let’s go over the provisions of the compareTo contract. The first provision says that if you reverse the direction of a comparison between two object references, the expected thing happens: if the first object is less than the second, then the second must be greater than the first; if the first object is equal to the second, then the second must be equal to the first; and if the first object is greater than the second, then the second must be less than the first. The second provision says that if one object is greater than a second and the second is greater than a third, then the first must be greater than the third. The final provision says that all objects that compare as equal must yield the same results when compared to any other object.

让我们再来过一遍compareTo方法约定的内容。第一条约定里说到，如果调换了两个对象应用之间的比较方向，那么将会发生以下的情况：如果第一个对象小于第二个对象，那么第二个对象一定大于第一个对象；如果第一个对象等于第二个对象，那么第二个对象一定等于第一个对象；如果第一个对象大于第二个对象，那么第二个对象一定小于第一个对象。第二条约定里说到，如果第一个对象大于第二个对象，而且第二个对象大于第三个对象，那么第一个对象一定大于第三个对象。最后一条约定里说到，若两个对象的比较结果是相等，那么这两个对象在分别与别的对象进行比较时，分别产生的比较结果也是一样的。

One consequence of these three provisions is that the equality test imposed by a compareTo method must obey the same restrictions imposed by the equals contract: reflexivity, symmetry, and transitivity. Therefore, the same caveat applies: there is no way to extend an instantiable class with a new value component while preserving the compareTo contract, unless you are willing to forgo the benefits of object-oriented abstraction \(Item 10\). The same workaround applies, too. If you want to add a value component to a class that implements Comparable, don’t extend it; write an unrelated class containing an instance of the first class. Then provide a “view” method that returns the contained instance. This frees you to implement whatever compareTo method you like on the containing class, while allowing its client to view an instance of the containing class as an instance of the contained class when needed.

这三条约定的一个直接结果是，对于compareTo方法的相等性测试（equality test）一定和对于equals方法的同等性测试遵守着同样的限制条件：自反性，对称性和传递性。因而，相同的警告在此同样适用：在遵守compareTo约定的同时，无法用新的值组件来扩展一个可实例化的类，除非你愿意放弃面向对象抽象所带来的好处（条目10）。而相同的解决方案对此也同样适用。如果我们想在一个实现了Comparable接口的类上添加一个值组件，不要去继承它，而是编写一个包含了第一个类的实例的不相关类。然后提供一个“视图”方法用于返回包含的这个实例。这样我们就可以在包含类上实现任何compareTo方法，同时允许它的客户端在需要的时候将包含类的实例视同为被包含类的实例。

The final paragraph of the compareTo contract, which is a strong suggestion rather than a true requirement, simply states that the equality test imposed by the compareTo method should generally return the same results as the equals method. If this provision is obeyed, the ordering imposed by the compareTo method is said to be consistent with equals. If it’s violated, the ordering is said to be inconsistent with equals. A class whose compareTo method imposes an order that is inconsistent with equals will still work, but sorted collections containing elements of the class may not obey the general contract of the appropriate collection interfaces \(Collection,Set, or Map\). This is because the general contracts for these interfaces are defined in terms of the equals method, but sorted collections use the equality test imposed by compareTo in place of equals. It is not a catastrophe if this happens, but it’s something to be aware of.

compareTo方法约定的最后一段是个强烈的建议，而不是真的要求那么做，它只是说明了对于compareTo方法的相等性测试一般应该于equals方法返回的结果一致。如果遵守了这个建议，那么我们可以说由compareTo方法得出的顺序关系于equals方法得出的一致。反之，则是不一致。一个类的compareTo方法得出的顺序关系即使与它的equals方法得出的不同，它也仍然能正常工作，但包含了这个类的实例的排序集合可能就无法遵循相应集合接口（Collection，Set，或者Map）的通用约定了。这是因为，这些接口的通用约定是基于equals方法来定义的，但有序接口使用了compareTo方法的同等性测试，而不是equals方法的。虽然发生这种情况并不会导致灾难性后果，但还是应该有所了解。

For example, consider the BigDecimal class, whose compareTo method is inconsistent with equals. If you create an empty HashSet instance and then add new BigDecimal\("1.0"\) and new BigDecimal\("1.00"\), the set will contain two elements because the two BigDecimal instances added to the set are unequal when compared using the equals method. If, however, you perform the same procedure using a TreeSet instead of a HashSet, the set will contain only one element because the two BigDecimal instances are equal when compared using the compareTo method. \(See the BigDecimal documentation for details.\)

例如，BigDecimal类的compareTo方法就与equals方法的同等性测试结果不一样。如果我们创建了一个空的HashSet实例，然后往里添加new BigDecimal\("1.0"\)和new BidDecimal\("1.00"\)，那么这个集合将会包含两个元素，因为这两个添加进去的BigDecimal实例通过equals方法做比较时是不相等的。然而，如果我们一开始使用的是TreeSet而不是HashSet，那么这个集合将包含一个元素，因为这两个BigDecimal元素通过compareTo方法做比较时是相等的。（详情请见BidDecimal文档。）

Writing a compareTo method is similar to writing an equals method, but there are a few key differences. Because the Comparable interface is parameterized, the compareTo method is statically typed, so you don’t need to type check or cast its argument. If the argument is of the wrong type, the invocation won’t even compile. If the argument is null, the invocation should throw a NullPointerException, and it will, as soon as the method attempts to access its members.

编写compareTo方法与编写equals方法相类似，但也有几个关键的不同点。因为Comparable接口是参数化的，compareTo方法是静态的类型，所以我们不用进行类型检查或者对参数进行转型。如果参数不是要求的类型，那么调用不会被编译通过。如果参数是null，那么在调用时，方法一旦访问参数的成员，将立马抛出NullPointerException异常。

In a compareTo method, fields are compared for order rather than equality. To compare object reference fields, invoke the compareTo method recursively. If a field does not implement Comparable or you need a nonstandard ordering, use a Comparator instead. You can write your own comparator or use an existing one, as in this compareTo method for CaseInsensitiveString in Item 10:

在compareTo方法里，我们对属性进行比较是为了得到一个顺序而不是看其是否相等。为了比较对象引用的属性，我们可以递归地调用compareTo方法。如果一个属性没有实现Comparable接口或者我们需要一个非标准的顺序，可以使用Comparator来替代。我们可以写一个我们自己的comparator或直接使用现有的，下面例子中的compareTo方法就是为条目10的CaseInsensitiveString类而编写的：

```
// Single-field Comparable with object reference field
public final class CaseInsensitiveString implements Comparable<CaseInsensitiveString> {
    public int compareTo(CaseInsensitiveString cis) {
        return String.CASE_INSENSITIVE_ORDER.compare(s, cis.s);
    }
... // Remainder omitted 
}
```

Note that CaseInsensitiveString implements Comparable&lt;CaseInsensitiveString&gt;. This means that a CaseInsensitiveString reference can be compared only to another CaseInsensitiveString reference. This is the normal pattern to follow when declaring a class to implement Comparable.

注意到，CaseInsensitiveString实现了Comparable&lt;CaseInsensitiveString&gt;接口。这意味着，CaseInsensitiveString引用只能被另一个CaseInsensitiveString引用进行比较。声明一个类实现了Comparable接口是常见的模式。

Prior editions of this book recommended that compareTo methods compare integral primitive fields using the relational operators &lt; and &gt;, and floating point primitive fields using the static methods Double.compare and Float.compare. In Java 7, static compare methods were added to all of Java’s boxed primitive classes. **Use of the relational operators &lt; and &gt; in compareTo methods is verbose and error-prone and no longer recommended.**

本书的上一版中提到，在比较整型基本类型时，推荐使用关系运算符 &lt; 和 &gt;，在比较浮点基本类型时，推荐使用静态方法Double.compare和Float.compare。在Java 7中，静态比较方法被添加到所有的Java装箱基本类里了。**在compareTo方法中使用关系运算符 &lt; 和 &gt; 会显得冗余并且易于出错，不再推荐。**

If a class has multiple significant fields, the order in which you compare them is critical. Start with the most significant field and work your way down. If a comparison results in anything other than zero \(which represents equality\), you’re done; just return the result. If the most significant field is equal, compare the next-most-significant field, and so on, until you find an unequal field or compare the least significant field. Here is a compareTo method for the PhoneNumber class in Item 11demonstrating this technique:**  **

如果一个类里有很多个重要属性，那么我们对于属性的比较顺序也是很重要的。我们从最重要的属性开始逐个进行比较。中途任意一个属性的比较结果不是0（即相等），我们的比较就算完成了，返回该比较结果即可。如果最重要的属性相等，则比较次重要的属性，依此类推，直到我们找到一个不等的属性或比较到了最不重要的属性。下面是为条目11中的PhoneNumber类编写的compareTo方法就说明了上述方法：

```
// Multiple-field Comparable with primitive fields
public int compareTo(PhoneNumber pn) {
    int result = Short.compare(areaCode, pn.areaCode); 
    if (result == 0) {
        result = Short.compare(prefix, pn.prefix); 
        if (result == 0)
            result = Short.compare(lineNum, pn.lineNum); 
    }
    return result; 
}
```

In Java 8, the Comparator interface was outfitted with a set of comparator construction methods, which enable fluent construction of comparators. These comparators can then be used to implement a compareTo method, as required by the Comparable interface. Many programmers prefer the conciseness of this approach, though it does come at a modest performance cost: sorting arrays of PhoneNumber instances is about 10% slower on my machine. When using this approach, consider using Java’s static import facility so you can refer to static comparator construction methods by their simple names for clarity and brevity. Here’s how the compareTo method for PhoneNumber looks using this approach:

在Java 8中，Comparator接口提供了一系列的Comparator构建方法，这些构建方法能流畅地构建Comparator。然后这些构建出来的比较器能被用在compareTo方法里，就像Comparable接口里要求的那样。许多程序员喜欢这种简洁的方式，虽然这种方式的性能比较一般：在我的电脑上对PhoneNumber实例数组进行排序，大约慢了10%。在使用这种方式时，可以考虑使用Java的静态导入机制，这样我们就可以简洁明了通过他们的名字来使用静态Comparator构建方法。

```
// Comparable with comparator construction methods
private static final Comparator<PhoneNumber> COMPARATOR = comparingInt((PhoneNumber pn) -> pn.areaCode)
.thenComparingInt(pn -> pn.prefix)  
.thenComparingInt(pn -> pn.lineNum);
public int compareTo(PhoneNumber pn) { 
    return COMPARATOR.compare(this, pn);
}
```

This implementation builds a comparator at class initialization time, using two comparator construction methods. The first is comparingInt. It is a static method that takes a key extractor function that maps an object reference to a key of type int and returns a comparator that orders instances according to that key. In the previous example, comparingInt takes a lambda\(\) that extracts the area code from a PhoneNumber and returns a Comparator&lt;PhoneNumber&gt; that orders phone numbers according to their area codes. Note that the lambda explicitly specifies the type of its input parameter \(PhoneNumber pn\). It turns out that in this situation, Java’s type inference isn’t powerful enough to figure the type out for itself, so we’re forced to help it in order to make the program compile.

上述实现在类的初始化时通过使用两个Comparator构建方法创建了一个比较器。第一个是comparingInt，它是个静态方法，要求传入一个键提取函数，这个函数能将一个对象引用映射为一个int类型的键。这个静态方法返回一个比较器，这个比较器依据提取出来的键对实例进行排序。在上面的例子当中，comparingInt要求传入一个lambda\(\)，通过它将区域代码（area code）从PhoneNumber中提取出来，然后返回Comparator&lt;PhoneNumber&gt;，其可以依据区域代码对电话号码进行排序。注意，这里的lambda表达式显式指定了它所需的参数\(PhoneNumber pn\)。这是因为在这种情况下，Java的类型推断功能还不够强大，所以我们不得不显式指定参数类型来让它能编译通过。

If two phone numbers have the same area code, we need to further refine the comparison, and that’s exactly what the second comparator construction method, thenComparingInt, does. It is an instance method on Comparator that takes an int key extractor function, and returns a comparator that first applies the original comparator and then uses the extracted key to break ties. You can stack up as many calls to thenComparingInt as you like, resulting in a lexicographic ordering. In the example above, we stack up two calls to thenComparingInt, resulting in an ordering whose secondary key is the prefix and whose tertiary key is the line number. Note that we did not have to specify the parameter type of the key extractor function passed to either of the calls to thenComparingInt: Java’s type inference was smart enough to figure this one out for itself.

如果两个电话号码的区域代码相同，那么我们需要继续比较下去，这正是第二个比较器构造方法，thenComparingInt，做的事。它是Comparator的实例方法，要求输入一个int类型键提取函数，并返回一个比较器，这个比较器先应用第一层比较器，然后使用提取出来的键来进行最终的比较。只要你喜欢，你可以往上叠加任意多的thenComparingInt调用，从而产生一个字典排序。在上面的例子中，我们叠加了两次thenComparingInt调用，产生了一个排序，这个排序的第二个键是prefix，第三个键是line number。注意到，我们在在往thenComparingInt传参时，不用指明键提取函数的参数类型：Java的类型推断足够智能来推断出此处的参数类型。

The Comparator class has a full complement of construction methods. There are analogues to comparingInt and thenComparingInt for the primitive types long and double. The int versions can also be used for narrower integral types, such as short, as in our PhoneNumber example. The double versions can also be used for float. This provides coverage of all of Java’s numerical primitive types.

Comparator类有着完备的构建方法。对于基础类型long和double，它也有类似于comparingInt和thenComparingInt的方法。在Comparator里，int相关的方法同样适用于范围更小的整型类型，如short，例子可参见PhoneNumber。同样地，double相关的方法也适用于float类型。这些方法覆盖了Java里所有的数值基本类型。

There are also comparator construction methods for object reference types. The static method, named comparing, has two overloadings. One takes a key extractor and uses the keys’ natural order. The second takes both a key extractor and a comparator to be used on the extracted keys. There are three overloadings of the instance method, which is named thenComparing. One overloading takes only a comparator and uses it to provide a secondary order. A second overloading takes only a key extractor and uses the key’s natural order as a secondary order. The final overloading takes both a key extractor and a comparator to be used on the extracted keys.

Occasionally you may see compareTo or compare methods that rely on the fact that the difference between two values is negative if the first value is less than the second, zero if the two values are equal, and positive if the first value is greater. Here is an example:

```
// BROKEN difference-based comparator - violates transitivity!
static Comparator<Object> hashCodeOrder = new Comparator<>() {
    public int compare(Object o1, Object o2) { 
        return o1.hashCode() - o2.hashCode();
    } 
};
```

Do not use this technique. It is fraught with danger from integer overflow and IEEE 754 floating point arithmetic artifacts \[JLS 15.20.1, 15.21.1\]. Furthermore, the resulting methods are unlikely to be significantly faster than those written using the techniques described in this item. Use either a static compare method:

```
// Comparator based on static compare method
static Comparator<Object> hashCodeOrder = new Comparator<>() {
    public int compare(Object o1, Object o2) {
        return Integer.compare(o1.hashCode(), o2.hashCode());
    } 
};
```

or a comparator construction method:

```
// Comparator based on Comparator construction method
static Comparator<Object> hashCodeOrder = Comparator.comparingInt(o -> o.hashCode());
```

In summary, whenever you implement a value class that has a sensible ordering, you should have the class implement the Comparable interface so that its instances can be easily sorted, searched, and used in comparison-based collections. When comparing field values in the implementations of the compareTo methods, avoid the use of the&lt;and&gt;operators. Instead, use the static compare methods in the boxed primitive classes or the comparator construction methods in the Comparator interface.

