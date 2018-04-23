### 第30条：优先使用泛型方法

Just as classes can be generic, so can methods. Static utility methods that operate on parameterized types are usually generic. All of the “algorithm” methods in Collections \(such as binarySearch and sort\) are generic.

就像类可以是泛型的那样，方法也是可以的。作用与参数化类型的静态工具方法通常都是泛型的。Collections类的所有“算法”方法（如binarySearch方法和sort方法）都是泛型的。

Writing generic methods is similar to writing generic types. Consider this deficient method, which returns the union of two sets:

编写泛型方法类似于编写泛型类型。考虑下面这个有缺陷的方法，它返回了两个集合的并集：

```
// Uses raw types - unacceptable! (Item 26)
public static Set union(Set s1, Set s2) {
    Set result = new HashSet(s1);
    result.addAll(s2);
    return result;
}
```

This method compiles but with two warnings:

这个方法可以编译通过，但会出现两条警告：

```
Union.java:5: warning: [unchecked] unchecked call to
HashSet(Collection<? extends E>) as a member of raw type
HashSet
Set result = new HashSet(s1);
^
Union.java:6: warning: [unchecked] unchecked call to
addAll(Collection<? extends E>) as a member of raw type Set
result.addAll(s2);
^
```

To fix these warnings and make the method typesafe, modify its declaration to declare a type parameter representing the element type for the three sets \(the two arguments and the return value\) and use this type parameter throughout the method.** The type parameter list, which declares the type parameters, goes between a method’s modifiers and its return type.** In this example, the type parameter list is &lt;E&gt;, and the return type is Set&lt;E&gt;. The naming conventions for type parameters are the same for generic methods and generic types \(Items 29, 68\):

为了修复这些警告并使得这个方法类型安全，我们可以修改方法声明，即声明一个类型参数来表示三个集合（两个参数和返回值）的元素类型，同时在整个方法体内都使用这个类型参数。**声明了类型参数的类型参数列表在方法修饰符和它的返回类型之间。**在这个例子中，类型参数列表是&lt;E&gt;，返回类型是Set&lt;E&gt;。类型参数的命名规则跟泛型方法和泛型类型一致（条目29，68）：

```
// Generic method
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```

At least for simple generic methods, that’s all there is to it. This method compiles without generating any warnings and provide stype safety as well as ease of use. Here’s a simple program to exercise the method. This program contains no casts and compiles without errors or warnings:

至少对于简单的泛型方法来说，就是这么做。这个方法编译后不会产生任何警告，并且提供了类型安全性和易用性。下面这段程序简单地示范了这个方法的使用。这段程序不用强转，而且编译不会产生错误或者警告：

```
// Simple program to exercise generic method
public static void main(String[] args) {
    Set<String> guys = Set.of("Tom", "Dick", "Harry");
    Set<String> stooges = Set.of("Larry", "Moe", "Curly");
    Set<String> aflCio = union(guys, stooges);
    System.out.println(aflCio);
}
```

When you run the program, it prints \[Moe, Tom, Harry, Larry, Curly, Dick\]. \(The order of the elements in the output is implementation-dependent.\)

当你运行这段程序时，它会打印\[Moe, Tom, Harry, Larry, Curly, Dick\]。（输出元素的次序基于实现。）

A limitation of the union method is that the types of all three sets \(both input parameters and the return value\) have to be exactly the same. You can make the method more flexible by using bounded wildcard types \(Item 31\).

这个union方法的一个限制是，三个集合（即两个输入参数和返回值）的类型都必须是完全一致的。通过使用有限制通配符类型，你可以让这个方法变得更灵活（条目31）。

On occasion, you will need to create an object that is immutable but applicable to many different types. Because generics are implemented by erasure \(Item 28\), you can use a single object for all required type parameterizations, but you need to write a static factory method to repeatedly dole out the object for each requested type parameterization. This pattern, called the generic singleton factory, is used for function objects \(Item 42\) such as Collections.reverseOrder, and occasionally for collections such as Collections.emptySet.

有时，你会需要创建一个不可变的对象，但又希望它适用于不同的类型。因为泛型是通过擦除来实现的（条目28），所以你可以用单个对象来进行所有所需类型的参数化，但你需要写一个静态工厂方法来重复地为每个类型参数化请求发放对象。这种模式我们称之为泛型单例工厂，它用于函数对象（条目42），如Collections.reverseOrder，并且有时也用于集合，如Collections.emptySet。

Suppose that you want to write an identity function dispenser. The libraries provide Function.identity, so there’s no reason to write your own \(Item 59\), but it is instructive. It would be wasteful to create a new identity function object time one is requested, because it’s stateless. If Java’s generics were reified, you would need one identity function per type, but since they’re erased a generic singleton will suffice. Here’s how it looks:

假设你需要编写一个恒等函数分发器（identity function dispenser）。类库里已经提供了Function.identity方法，所以没有理由再去写一个你自己的（条目59），但它对于我们是有启发的。如果每次请求过来的时候都去创建一个新的恒等函数对象，会造成很大的浪费，因为它是无状态的。如果Java的泛型被具化，那么你需要为每个类型都提供一个恒等函数，但由于这些类型被擦除了，所有提供一个泛型单例就足够了。以下是它的示例：

```
// Generic singleton factory pattern
private static UnaryOperator<Object> IDENTITY_FN = (t) -> t;
@SuppressWarnings("unchecked")
public static <T> UnaryOperator<T> identityFunction() {
    return (UnaryOperator<T>) IDENTITY_FN;
}
```

The cast of IDENTITY\_FN to \(UnaryFunction&lt;T&gt;\) generates an unchecked cast warning, as UnaryOperator&lt;Object&gt; is not a UnaryOperator&lt;T&gt; for every T. But the identity function is special: it returns its argument unmodified, so we know that it is typesafe to use it as a UnaryFunction&lt;T&gt;, whatever the value of T. Therefore, we can confidently suppress the unchecked cast warning generated by this cast. Once we’ve done this, the code compiles without error or warning.

将IDENTITY\_FN强转成\(UnaryFunction&lt;T&gt;\)会产生一个未检查强转警告，这是因为对于每个T，UnaryOperator&lt;Object&gt;并不一定是UnaryOperator&lt;T&gt;。但恒等函数是特别的：它返回的参数是未修改过的，所以我们知道无论将其作为UnaryOperator&lt;T&gt;来使用是类型安全的，无论T的值是什么。因此，我们可以自信地将这个强转产生的未检查强转警告禁止掉。一旦我们这么做之后，代码编译时就不会产生错误或者警告了。

Here is a sample program that uses our generic singleton as a UnaryOperator&lt;String&gt; and a UnaryOperator&lt;Number&gt;. As usual, it contains no casts and compiles without errors or warnings:

以下是一段示例程序，它使用了我们的泛型单例，将其作为UnaryOperator&lt;String&gt;和UnaryOperator&lt;Number&gt;。像往常一样，它不包含强转代码，而且编译时也不会产生错误或警告：

```
// Sample program to exercise generic singleton
public static void main(String[] args) {
    String[] strings = { "jute", "hemp", "nylon" };
    UnaryOperator<String> sameString = identityFunction();
    for (String s : strings)
        System.out.println(sameString.apply(s));
    Number[] numbers = { 1, 2.0, 3L };
    UnaryOperator<Number> sameNumber = identityFunction();
    for (Number n : numbers)
        System.out.println(sameNumber.apply(n));
}
```

It is permissible, though relatively rare, for a type parameter to be bounded by some expression involving that type parameter itself. This is what’s known as a recursive type bound. A common use of recursive type bounds is in connection with the Comparable interface, which defines a type’s natural ordering \(Item 14\). This interface is shown here:

通过某个包含类型参数本身的表达式来限制类型参数是允许的，虽然相对来说比较少这么做。我们称之为递归类型限制。递归类型限制的一个常见用法与Comparable接口有关，这个接口定义了一个类型的自然顺序（条目14）。这个接口如下所示：

```
public interface Comparable<T> {
    int compareTo(T o);
}
```

The type parameter T defines the type to which elements of the type implementing Comparable&lt;T&gt;can be compared. In practice, nearly all types can be compared only to elements of their own type. So, for example, String implements Comparable&lt;String&gt;, Integer implements Comparable&lt;Integer&gt;, and so on.

类型参数T定义了实现Comparable&lt;T&gt;接口的元素可以比较的类型。在实际当中，几乎所有的类型都只能与它们自身的类型进行比较。所以，String实现了Comparable&lt;String&gt;，Integer实现了Comparable&lt;Integer&gt;，等等。

Many methods take a collection of elements implementing Comparable to sort it, search within it, calculate its minimum or maximum, and the like. To do these things, it is required that every element in the collection be comparable to every other element in it, in other words, that the elements of the list be mutually comparable. Here is how to express that constraint:

许多方法接收元素实现了Comparable接口的集合，并将这个集合进行排序，在其内部进行查找，计算出它的最小值或最大值等。为了做这些事，它要求集合里的每一个元素都能与其内部的其它元素进行比较，换句话说，列表里的元素是可以互相比较的。以下展示了如何表达这个约束：

```
// Using a recursive type bound to express mutual comparability
public static <E extends Comparable<E>> E max(Collection<E> c);
```



