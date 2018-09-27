If the elements of an enumerated type are used primarily in sets, it is traditional to use the int enum pattern \(Item 34\), assigning a different power of 2 to each constant:

如果一个枚举类型的元素主要是用于集合，那么传统做法是采用int枚举模式（条目34），将2的不同倍数赋值给每个常量：

**//** **Bit field enumeration constants - OBSOLETE!**

```java
public class Text {
    public static final int STYLE_BOLD = 1 << 0; // 1
    public static final int STYLE_ITALIC = 1 << 1; // 2
    public static final int STYLE_UNDERLINE = 1 << 2; // 4 
    public static final int STYLE_STRIKETHROUGH = 1 << 3; // 8
    // Parameter is bitwise OR of zero or more STYLE_constants
    public void applyStyles(int styles) { ... } 
}
```

This representation lets you use the bitwise OR operation to combine several constants into a set, known as a _bit field_:

下面的这种用法表示采用OR位运算来将几个常量组合到一个集合里，我们把这些常量称为位域（bit field）：

```java
text.applyStyles(STYLE_BOLD | STYLE_ITALIC);
```

The bit field representation also lets you perform set operations such as union and intersection efficiently using bitwise arithmetic. But bit fields have all the disadvantages of int enum constants and more. It is even harder to interpret a bit field than a  
simple int enum constant when it is printed as a number. There is no easy way to iterate over all of the elements represented by a bit field. Finally, you have to predict the maximum number of bits you’ll ever need at the time you’re writing the API and choose a type for the bit field \(typically int or long\) accordingly. Once you’ve picked a type, you can’t exceed its width \(32 or 64 bits\) without changing the API.

通过位运算，域表示法也允许你高效完成诸如并集，交集之类的操作。但同时位域也拥有着int枚举常量的所有缺点，甚至还更多。就比如，在将其打印成一个数字时，位域将比一个简单的int枚举常量更难解析翻译。而且也无法方便地对位域表示的所有元素进行遍历。最终，你不得不在编写API时对你所需的位的最大个数进行预测，并为位域选择一个相应的类型（通常是int或者long）。一旦你选择了一个类型，你就不能超过该类型的最大位数（32位或者64位），除非对API做出改变。

Some programmers who use enums in preference to int constants still cling to the use of bit fields when they need to pass around sets of constants. There is no reason to do this, because a better alternative exists. The java.util package provides the EnumSet class to efficiently represent sets of values drawn from a single enum type. This class implements the Set interface, providing all of the richness, type safety, and interoperability you get with any other Set implementation. But internally, each EnumSet is represented as a bit vector. If the underlying enum type has sixty-four or fewer elements—and most do—the entire EnumSet is represented with a single long, so its performance is comparable to that of a bit field. Bulk operations, such as removeAll and retainAll, are implemented using bitwise arithmetic, just as you’d do manually for bit fields. But you are insulated from the ugliness and error-proneness of manual bit twiddling: the EnumSet does the hard work for you.

相比使用int常量，有些程序员在使用枚举时会倾向于使用枚举，但当他们需要传入多组常量时，他们依旧会倾向于使用位域。其实不用这样的，因为有一个更好的办法。java.util包提供了EnumSet类，它可以更好地表示从一个枚举类型里提取的值的集合。这个类实现了Set接口，提供了丰富的功能，不仅类型安全，而且能让你与其它的Set实现类进行互用。但每个EnumSet的内部都表示成一个位矢量。

Here is how the previous example looks when modified to use enums and enum sets instead of bit fields. It is shorter, clearer, and safer:

当前面的例子改成使用枚举以及枚举集合来替换位域时，代码将变成下面这副模样。修改后的代码更简短，干净，而且安全：

**// EnumSet - a modern replacement for bit fields**

```java
public class Text {
    public enum Style { 
        BOLD, ITALIC, UNDERLINE,STRIKETHROUGH 
    }
    // Any Set could be passed in, but EnumSet is clearly best
    public void applyStyles(Set<Style> styles) { ... } 
}
```

Here is client code that passes an EnumSet instance to the applyStyles method. The EnumSet class provides a rich set of static factories for easy set creation, one of which is illustrated in this code:

```java
text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
```

Note that the applyStyles method takes a Set&lt;Style&gt;rather than an EnumSet&lt;Style&gt;. While it seems likely that all clients would pass an EnumSet to the method, it is generally good practice to accept the interface type rather than the implementation type \(Item 64\). This allows for the possibility of an unusual client to pass in some other Set implementation.

In summary, **just because an enumerated type will be used in sets, there is no reason to represent it with bit fields. **The EnumSet class combines the conciseness and performance of bit fields with all the many advantages of enum types described inItem 34. The one real disadvantage of EnumSet is that it is not, as of Java 9, possible to create an immutable EnumSet, but this will likely be remedied in an upcoming release. In the meantime, you can wrap an EnumSet with Collections.unmodifiableSet, but conciseness and performance will suffer.

