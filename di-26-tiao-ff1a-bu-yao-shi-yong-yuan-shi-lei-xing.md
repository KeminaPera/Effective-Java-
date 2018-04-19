### 第26条：不要使用原始类型

First, a few terms. A class or interface whose declaration has one or more type parameters is a genericclass or interface \[JLS, 8.1.2, 9.1.2\]. For example, the List interface has a single type parameter, E, representing its element type. The full name of the interface is List&lt;E&gt; \(read “list of E”\), but people often call it List for short. Generic classes and interfaces are collectively known as generic types.

Each generic type defines a set of parameterized types, which consist of the class or interface name followed by an angle-bracketed list of actual type parameters corresponding to the generic type’s formal type parameters \[JLS, 4.4, 4.5\]. Forexample, List&lt;String&gt; \(read “list of string”\) is a parameterized type representing a list whose elements are of type String. \(String is the actual type parameter corresponding to the formal type parameter E.\)

Finally, each generic type defines a raw type, which is the name of the generic type used without any accompanying type parameters \[JLS, 4.8\]. For example, the raw type corresponding to List&lt;E&gt; is List. Raw types behave as if all of the generic type information were erased from the type declaration. They exist primarily for compatibility with pre-generics code.

Before generics were added to Java, this would have been an exemplary collection declaration. As of Java 9, it is still legal, but far from exemplary:

```
// Raw collection type - don't do this!
// My stamp collection. Contains only Stamp instances.
private final Collection stamps = ... ;
```



