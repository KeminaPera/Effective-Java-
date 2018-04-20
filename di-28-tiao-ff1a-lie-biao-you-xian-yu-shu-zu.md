### 第28条：列表优先于数组

Arrays differ from generic types in two important ways. First, arrays are covariant. This scary-sounding word means simply that if Sub is a subtype of Super, then the array type Sub\[\] is a subtype of the array type Super\[\]. Generics, by contrast, are invariant: for any two distinct types Type1 and Type2, List&lt;Type1&gt; is neither a subtype nor a supertype of List&lt;Type2&gt; \[JLS, 4.10; Naftalin07, 2.5\]. You might think this means that generics are deficient, but arguably it is arrays that are deficient. This code fragment is legal:

数组与泛型类型的不同体现在两个方面。首先，数组是协变的（covariant）。这个词看起来有点吓人，不过它也只是意味着，如果Sub是Super的一个子类型，那么数组类型Sub\[\]也是数组类型Super\[\]的子类型。相反，泛型是有约束的：对于任意的两个不同的类型Type1和Type2，List&lt;Type1&gt;既不是List&lt;Type2&gt;的子类也不是它的父类\[JLS, 4.10; Naftalin07, 2.5\]。你可能想，这是不是意味着泛型是有缺陷的，但实际上，可以说数组才是有缺陷的。下面的代码片段是合法的：

```
// Fails at runtime!
Object[] objectArray = new Long[1];
objectArray[0] = "I don't fit in"; // Throws ArrayStoreException
```

but this one is not:

但下面这个则不是：

```
// Won't compile!
List<Object> ol = new ArrayList<Long>(); // Incompatible types
ol.add("I don't fit in");
```

Either way you can’t put a String into a Long container, but with an array you find out that you’ve made a mistake at runtime; with a list, you find out at compile time. Of course, you’d rather find out at compile time.

任意一种方式你都无法将一个String放入一个Long容器里，但如果用数组的话你会在运行时才发现你犯了个错误，而如果用List，你就会在编译时发现它。当然，你会更想在编译时就发现问题。

The second major difference between arrays and generics is that arrays are reified \[JLS, 4.7\]. This means that arrays know and enforce their element type at runtime. As noted earlier, if you try to put a String into an array of Long, you’ll get an ArrayStoreException. Generics, by contrast, are implemented by erasure \[JLS, 4.6\]. This means that they enforce their type constraints only at compile time and discard \(or erase\) their element type information at runtime. Erasure is what allowed generic types to interoperate freely with legacy code that didn’t use generics \(Item 26\), ensuring a smooth transition to generics in Java 5.

数组和泛型的第二个主要区别是，数组是具化的\[JLS, 4.7\]。这意味着，数组在运行时才知道并检查元素类型。如前面所说，如果你将一个String放入Long数组里，你将会得到一个ArrayStoreException异常。想法，泛型则是通过擦除来实现的\[JLS, 4.6\]。这意味着泛型仅在编译时进行类型约束的检查并且在运行是忽略（或擦除）元素类型。擦除机制使得泛型类型可以自由地与那些未使用泛型（条目26）的遗留代码互用，确保平滑转换到Java 5的泛型。

