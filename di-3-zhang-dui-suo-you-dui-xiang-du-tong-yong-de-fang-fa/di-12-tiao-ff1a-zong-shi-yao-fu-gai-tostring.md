### 第12条：总是要覆盖toString

While Object provides an implementation of the toString method, the string that it returns is generally not what the user of your class wants to see. It consists of the class name followed by an “at” sign \(@\) and the unsigned hexadecimal representation of the hash code, for example, PhoneNumber@163b91. The general contract for toString says that the returned string should be “a concise but informative representation that is easy for a person to read.” While it could be argued that PhoneNumber@163b91 is concise and easy to read, it isn’t very informative when compared to 707-867-5309.

虽然Object提供了一个toString方法的实现，但返回的字符串并不是用户想看的。这个返回的字符串包含了类名，随后跟着一个@符号以及无符号的十六进制的哈希码，例如，PhoneNumber@163b91。toString方法的通用约定里规定，返回的字符串应该是“简洁，但信息丰富，易于人们阅读的”。虽然你也可以说PhoneNumber@163b91简洁而且易于阅读，但比起707-867-5309，它的信息量就不够丰富了。

The toString contract goes on to say, “It is recommended that all subclasses override this method.” Good advice, indeed!

toString方法的通用约定接着说道，“建议所有的子类都覆盖这个方法”。这的确是条好的建议！

While it isn’t as critical as obeying the equals and hashCode contracts \(Items 10 and 11\), providing a good toString implementation makes your class much more pleasant to use and makes systems using the class easier to debug. The toString method is automatically invoked when an object is passed to println, printf, the string concatenation operator, or assert, or is printed by a debugger. Even if you never call toString on an object, others may. For example, a component that has a reference to your object may include the string representation of the object in a logged error message. If you fail to override toString, the message may be all but useless.

虽然遵守toString方法的约定不像遵守equals和hashCode（条目10和条目11）的那么重要，但提供一个好的toString方法实现能让我们的类用起来更舒适，而且让使用这个类的系统更好调试。当一个对象被传入到println，printf方法时，toString方法就自动被调用，遇到字符串连接符（+）或者assert或者调试器打印，这个方法同样被调用。即使我们自己不调用对象上的toString方法，别人也会调用。例如，某个引用了我们开发的对象的组件可能会在错误日志中包含这个对象的字符串信息。如果没有覆盖toString方法，那么这条信息可能会变得没什么用。

If you’ve provided a good toString method for PhoneNumber, generating a useful diagnostic message is as easy as this:

如果我们能够为PhoneNumber提供一个好的toString方法，那么生成一个有用的诊断信息将会变得很容易：

```
System.out.println("Failed to connect to " + phoneNumber);
```

Programmers will generate diagnostic messages in this fashion whether or not you override toString, but the messages won’t be useful unless you do. The benefits of providing a good toString method extend beyond instances of the class to objects containing references to these instances, especially collections. Which would you rather see when printing a map, {Jenny=PhoneNumber@163b91} or {Jenny=707-867-5309}?

无论是否覆盖toString方法，程序员都将以这种方式来生成诊断信息，但如果不覆盖的话生成的信息将会变得用途不大。提供一个好的toString方法的好处是，不仅有益于该类的实例，而且有益于包含这些实例的引用的对象，尤其是集合。在打印一个map的时候，你更想看到哪一个结果，{Jenny=PhoneNumber@163b91} 还是{Jenny=707-867-5309}？

