### 第6条：避免创建不必要的对象

It is often appropriate to reuse a single object instead of creating a new functionally equivalent object each time it is needed. Reuse can be both faster and more stylish. An object can always be reused if it is immutable \(Item 17\).

As an extreme example of what not to do, consider this statement:

很多时候，我们最好去复用一个对象而不是每次在需要时都去创建一个新的功能相同的对象。复用的方式不仅更快速，而且更时尚。如果一个对象是不可变的，那么它总是能被复用（条目17）。

举一个极端的反面例子，考虑下面这个语句：

```
String s = new String("bikini"); // DON'T DO THIS!
```

The statement creates a new String instance each time it is executed, and none of those object creations is necessary. The argument to the String constructor \("bikini"\) is itself a String instance, functionally identical to all of the objects created by the constructor. If this usage occurs in a loop or in a frequently invoked method, millions of String instances can be created needlessly.

这条语句每次被执行时都会产生一个新的String实例，但这些对象的创建都是不必要的。

The improved version is simply the following:

```
String s = "bikini";
```



