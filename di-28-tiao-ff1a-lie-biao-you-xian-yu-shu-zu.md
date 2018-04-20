### 第28条：列表优先于数组

Arrays differ from generic types in two important ways. First, arrays are covariant. This scary-sounding word means simply that if Sub is a subtype of Super, then the array type Sub\[\] is a subtype ofthe array type Super\[\]. Generics, by contrast, are invariant: for any two distinct types Type1 and Type2, List&lt;Type1&gt; is neither a subtype nor a supertype of List&lt;Type2&gt; \[JLS, 4.10; Naftalin07, 2.5\]. You might think this means that generics are deficient, but arguably it is arrays that are deficient. This code fragment is legal:

```
// Fails at runtime!
Object[] objectArray = new Long[1];
objectArray[0] = "I don't fit in"; // Throws ArrayStoreException
```

but this one is not:

```
// Won't compile!
List<Object> ol = new ArrayList<Long>(); // Incompatible types
ol.add("I don't fit in");
```

Either way you can’t put a String into a Long container, but with an

array you find out that you’ve made a mistake at runtime; with a

list, you find out at compile time. Of course, you’d rather find out

at compile time.

The second major difference between arrays and generics is that

arrays are reified \[JLS, 4.7\]. This means that arrays know and

enforce their element type at runtime. As noted earlier, if you try to

put a String into an array of Long, you’ll get an ArrayStoreException.

Generics, by contrast, are implemented by erasure \[JLS, 4.6\]. This

means that they enforce their type constraints only at compile time

and discard \(or erase\) their element type information at runtime.

Erasure is what allowed generic types to interoperate freely withlegacy code that didn’t use generics \(Item 26\), ensuring a smooth

transition to generics in Java 5.

