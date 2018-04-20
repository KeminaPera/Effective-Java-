### 第28条：列表优先于数组

Arrays differ from generic types in two important ways. First,

arrays are covariant. This scary-sounding word means simply that

if Sub is a subtype of Super, then the array type Sub\[\] is a subtype ofthe array type Super\[\]. Generics, by contrast, are invariant: for any

two distinct types Type1 and Type2, List&lt;Type1&gt; is neither a subtype

nor a supertype of List&lt;Type2&gt; \[JLS, 4.10; Naftalin07, 2.5\]. You

might think this means that generics are deficient, but arguably it

is arrays that are deficient. This code fragment is legal:

