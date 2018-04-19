### 第26条：不要使用原始类型

First, a few terms. A class or interface whose declaration has one or more type parameters is a generic class or interface \[JLS, 8.1.2, 9.1.2\]. For example, the List interface has a single type parameter, E, representing its element type. The full name of the interface is List&lt;E&gt; \(read “list of E”\), but people often call it List for short. Generic classes and interfaces are collectively known as generic types.

首先，来介绍几个术语。泛型类或接口是指，声明里有一个或多个类型参数的类或接口\[JLS, 8.1.2, 9.1.2\]。例如，List接口就有一个类型参数，E，它表示了List的元素类型。接口的全名是List&lt;E&gt;（读作“E的列表”），但人们通常简称它为列表。泛型类和接口都被称为泛型类型。

Each generic type defines a set of parameterized types, which consist of the class or interface name followed by an angle-bracketed list of actual type parameters corresponding to the generic type’s formal type parameters \[JLS, 4.4, 4.5\]. For example, List&lt;String&gt; \(read “list of string”\) is a parameterized type representing a list whose elements are of type String. \(String is the actual type parameter corresponding to the formal type parameter E.\)

每个泛型类型都定义了一组参数化的类型，它的组成形式为，类名或者接口名后面跟着由尖括号包围的类型参数列表，列表里的每个参数都对应着泛型类型的形式类型参数\[JLS, 4.4, 4.5\]。例如，List&lt;String&gt;（读作字符串列表）就是一个参数化的类型，代表了元素是String类型的列表。（String是形式类型参数E的实际类型参数。）

Finally, each generic type defines a raw type, which is the name of the generic type used without any accompanying type parameters \[JLS, 4.8\]. For example, the raw type corresponding to List&lt;E&gt; is List. Raw types behave as if all of the generic type information were erased from the type declaration. They exist primarily for compatibility with pre-generics code.

最后，每个泛型类型定义了一个原始类型，即不带任何类型参数的泛型类型的名称。例如，List&lt;E&gt;对应的原始类型是List。原始类型看起来就像将所有的泛型类型信息从类型声明了去除了一样。它们的存在主要是为了兼容那些之前在泛型未出现时写的代码。

Before generics were added to Java, this would have been an exemplary collection declaration. As of Java 9, it is still legal, but far from exemplary:

```
// Raw collection type - don't do this!
// My stamp collection. Contains only Stamp instances.
private final Collection stamps = ... ;
```

If you use this declaration today and then accidentally put a coin into your stamp collection, the erroneous insertion compiles and runs without error \(though the compiler does emit a vague warning\):

```
// Erroneous insertion of coin into stamp collection
stamps.add(new Coin( ... )); // Emits "unchecked call" warning
```

You don’t get an error until you try to retrieve the coin from the  
 stamp collection:

```
// Raw iterator type - don't do this!
for (Iterator i = stamps.iterator(); i.hasNext(); )
    Stamp stamp = (Stamp) i.next(); // Throws ClassCastException
stamp.cancel();
```



