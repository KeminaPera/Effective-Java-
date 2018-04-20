### 第26条：不要使用原始类型

First, a few terms. A class or interface whose declaration has one or more type parameters is a generic class or interface \[JLS, 8.1.2, 9.1.2\]. For example, the List interface has a single type parameter, E, representing its element type. The full name of the interface is List&lt;E&gt; \(read “list of E”\), but people often call it List for short. Generic classes and interfaces are collectively known as generic types.

首先，来介绍几个术语。泛型类或接口是指，声明里有一个或多个类型参数的类或接口\[JLS, 8.1.2, 9.1.2\]。例如，List接口就有一个类型参数，E，它表示了List的元素类型。接口的全名是List&lt;E&gt;（读作“E的列表”），但人们通常简称它为列表。泛型类和接口都被称为泛型类型。

Each generic type defines a set of parameterized types, which consist of the class or interface name followed by an angle-bracketed list of actual type parameters corresponding to the generic type’s formal type parameters \[JLS, 4.4, 4.5\]. For example, List&lt;String&gt; \(read “list of string”\) is a parameterized type representing a list whose elements are of type String. \(String is the actual type parameter corresponding to the formal type parameter E.\)

每个泛型类型都定义了一组参数化的类型，它的组成形式为，类名或者接口名后面跟着由尖括号包围的类型参数列表，列表里的每个参数都对应着泛型类型的形式类型参数\[JLS, 4.4, 4.5\]。例如，List&lt;String&gt;（读作字符串列表）就是一个参数化的类型，代表了元素是String类型的列表。（String是形式类型参数E的实际类型参数。）

Finally, each generic type defines a raw type, which is the name of the generic type used without any accompanying type parameters \[JLS, 4.8\]. For example, the raw type corresponding to List&lt;E&gt; is List. Raw types behave as if all of the generic type information were erased from the type declaration. They exist primarily for compatibility with pre-generics code.

最后，每个泛型类型定义了一个原始类型，即不带任何类型参数的泛型类型的名称。例如，List&lt;E&gt;对应的原始类型是List。原始类型看起来就像将所有的泛型类型信息从类型声明了去除了一样。它们的存在主要是为了兼容那些之前在泛型未出现时写的代码。

Before generics were added to Java, this would have been an exemplary collection declaration. As of Java 9, it is still legal, but far from exemplary:

在泛型被添加进Java之前，下面的例子是一个标准的集合声明。对于Java 9，这么声明仍然是合法的，但就并不是典型的声明了：

```
// Raw collection type - don't do this!
// My stamp collection. Contains only Stamp instances.
private final Collection stamps = ... ;
```

If you use this declaration today and then accidentally put a coin into your stamp collection, the erroneous insertion compiles and runs without error \(though the compiler does emit a vague warning\):

如果到今天你还是用这种声明然后不小心往Stamp集合里放入了一个Coin对象，这种错误插入仍然可以编译而且运行也不会出错（虽然编译器会发出一个不明确的警告）：

```
// Erroneous insertion of coin into stamp collection
stamps.add(new Coin( ... )); // Emits "unchecked call" warning
```

You don’t get an error until you try to retrieve the coin from the stamp collection:

在你尝试从这个Stamp集合里获取Coin对象之前，你都不会遇到程序错误：

```
// Raw iterator type - don't do this!
for (Iterator i = stamps.iterator(); i.hasNext(); ) ｛
    Stamp stamp = (Stamp) i.next(); // Throws ClassCastException
    stamp.cancel();
｝
```

As mentioned throughout this book, it pays to discover errors as soon as possible after they are made, ideally at compile time. In this case, you don’t discover the error until runtime, long after it has happened, and in code that may be distant from the code containing the error. Once you see the ClassCastException, you have to search through the codebase looking for the method invocation that put the coin into the stamp collection. The compiler can’t help you, because it can’t understand the comment that says, “Contains only Stamp instances.”

正如本书提到的，出现错误后最好尽快发现，理想情况是在编译时就发现。在上面例子的情况下，直到运行时你才能发现问题，那时问题已经出现很久了，而且真正有问题的代码可能离直接出错的地方很远。在你看到ClassCastException后，你不得不搜索整个代码库，找出将Coin对象放入Stamp集合的方法调用。编译器无法提供帮助，因为它无法理解那句注释：只能包含Stamp实例。

With generics, the type declaration contains the information, not the comment:

而用了泛型后，类型声明里就包含了元素信息，而不仅仅是用注解来说明：

```
// Parameterized collection type - typesafe
private final Collection<Stamp> stamps = ... ;
```

From this declaration, the compiler knows that stamps should contain only Stamp instances and guarantees it to be true, assuming your entire codebase compiles without emitting \(or suppressing; see Item 27\) any warnings. When stamps is declared with a parameterized type declaration, the erroneous insertion generates a compile-time error message that tells you exactly what is wrong:

这么声明后，编译器知道了stamps应该只包含Stamp实例并且会保证这一点，假设你的整个代码库编译时不发出任何警告（或抑制，见条目27）。若声明stamps时用了参数化类型声明，则插入不符类型的元素时将会生成编译时错误信息，告诉你哪里错了：

```
Test.java:9: error: incompatible types: Coin cannot be converted to Stampc.add(new Coin());
                                                                               ^
```

The compiler inserts invisible casts for you when retrieving elements from collections and guarantees that they won’t fail \(assuming, again, that all of your code did not generate or suppress any compiler warnings\). While the prospect of accidentally inserting a coin into a stamp collection may appear far-fetched, the problem is real. For example, it is easy to imagine putting a BigInteger into a collection that is supposed to contain only BigDecimal instances.

当你从集合里获取元素时，编译器默默帮你做了强转的工作，保证了取出的元素是符合要求的（假设你所有的代码没有生成或抑制任何编译警告）。虽然不大可能将Coin对象插入Stamp集合里，但这个问题的确是存在的。例如，不难想象出将一个BigInteger放入一个本应该只能包含BigDecimal的集合。

As noted earlier, it is legal to use raw types \(generic types without their type parameters\), but you should never do it.** If you use raw types, you lose all the safety and expressiveness benefits of generics.** Given that you shouldn’t use them, why did the language designers permit raw types in the first place? For compatibility. Java was about to enter its second decade when generics were added, and there was an enormous amount of code in existence that did not use generics. It was deemed critical that all of this code remain legal and interoperate with newer code that does use generics. It had to be legal to pass instances of parameterized types to methods that were designed for use with raw types, and vice versa. This requirement, known as migration compatibility, drove the decisions to support raw types and to implement generics using erasure \(Item 28\).

如前面所说，使用原始类型（不包含类型参数的泛型类）是合法的，但你永远都不应该这么做。**如果你使用了原始类型，你将会失去泛型所带来的安全性和可读性。**既然不应该使用它们，那为什么Java语言设计者还要允许使用它们呢？这么做其实是为了兼容性。在泛型加入Java时，Java即将步入它的第二个十年，那会已经存在了大量没有使用泛型的代码。当时人们认为，让这些代码依旧合法存在而且能与使用了泛型的新代码互用这点是很重要的。将参数类型的实例传入之前那些为了使用原始类型而设计的方法，这必须是合法的，反之亦然。这种需求被称为移植兼容性（migration compatibility），它驱使了支持原始类型和使用擦除来实现泛型的决定（条目28）。

While you shouldn’t use raw types such as List, it is fine to use types that are parameterized to allow insertion of arbitrary objects, such as List&lt;Object&gt;. Just what is the difference between the raw type List and the parameterized type List&lt;Object&gt;? Loosely speaking, the former has opted out of the generic type system, while the latter has explicitly told the compiler that it is capable of holding objects of any type. While you can pass a List&lt;String&gt; to a parameter of type List, you can’t pass it to a parameter oftype List&lt;Object&gt;. There are sub-typing rules for generics, and List&lt;String&gt;is a subtype of the raw type List, but not of the parameterized type List&lt;Object&gt; \(Item 28\). As a consequence, **you lose type safety if you use a raw type such as List, but not if you use a parameterized type such as List&lt;Object&gt;.** To make this concrete, consider the following program:

```
// Fails at runtime - unsafeAdd method uses a raw type (List)!
public static void main(String[] args) {
    List<String> strings = new ArrayList<>();
    unsafeAdd(strings, Integer.valueOf(42));
    String s = strings.get(0); // Has compiler-generated cast
} 
private static void unsafeAdd(List list, Object o) {
    list.add(o);
}
```

This program compiles, but because it uses the raw type List, you

get a warning:

