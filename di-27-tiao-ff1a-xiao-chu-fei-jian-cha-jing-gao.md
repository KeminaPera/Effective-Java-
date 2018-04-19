### 第27条：消除未检查警告

When you program with generics, you will see many compiler warnings: unchecked cast warnings, unchecked method invocation warnings, unchecked parameterized vararg type warnings, and unchecked conversion warnings. The more experience you acquire with generics, the fewer warnings you’ll get, but don’t expect newly written code to compile cleanly.

当你用泛型编程时，你将会看到很多编译器发出的警告：未检查强转警告，未检查方法调用警告，未检查参数化可变参数类型警告，以及未检查转换警告。你使用泛型的经验越多，获得的警告就会越少，但不要指望新编写的代码可以完全没问题地被编译。

Many unchecked warnings are easy to eliminate. For example, suppose you accidentally write this declaration:

许多未检查警告都容易去除。例如，假设你不小心写了这个声明：

```
Set<Lark> exaltation = new HashSet();
```

The compiler will gently remind you what you did wrong:

编译器会细致地告诉你哪里出错了：

```
Venery.java:4: warning: [unchecked] unchecked conversion Set<Lark> exaltation = new HashSet();
                                                                   ^ 
required: Set<Lark>
found: HashSet
```

You can then make the indicated correction, causing the warning to disappear. Note that you don’t actually have to specify the type parameter, merely to indicate that it’s present with the diamond operator\(&lt;&gt;\), introduced in Java 7. The compiler will then infer the correct actual type parameter \(in this case, Lark\):

然后你可以根据编译器的指示来进行修正，让这个警告消失。注意，你实际上无需指明类型参数，只用Java 7引入的钻石操作符（&lt;&gt;，diamond operator）来表示即可。编译器就会推测出实际的类型参数（在本例中为Lark）：

```
Set<Lark> exaltation = new HashSet<>();
```

Some warnings will be much more difficult to eliminate. This chapter is filled with examples of such warnings. When you get warnings that require some thought, persevere! Eliminate every unchecked warning that you can.If you eliminate all warnings, you are assured that your code is type safe, which is a very good thing. It means that you won’t get a ClassCastException at runtime, and it increases your confidence that your program will behave as you intended.

有些警告会很难消除。本章将介绍很多这种警告的例子。当你遇到一些需要思考的警告时，请不要就此放弃消除！要尽可能消除每一个非检查警告。如果你消除了所有的警告，就可以确保你的代码是类型安全的，这是一件非常好的事。这意味着在程序运行时你不会遇到ClassCastException异常，而且你会更加自信你的程序可以实现预期的功能。

If you can’t eliminate a warning, but you can prove that the code that provoked the warning is type safe, then \(and only then\) suppress the warning with an @SuppressWarnings\("unchecked"\) annotation. If you suppress warnings without first proving that the code is type safe, you are giving yourself a false sense of security. The code may compile without emitting any warnings, but it can still throw a ClassCastException at runtime. If, however, you ignore unchecked warnings that you know to be safe \(instead of suppressing them\), you won’t notice when a new warning crops up that represents a real problem. The new warning will get lost amidst all the false alarms that you didn’t silence.

如果你无法消除某个警告，但你能证明引起这个警告的代码是类型安全的话，那么这时候（也只有这时候）可以用@SuppressWarnings\("unchecked"\)注解来禁止这个警告。如果你在没有事先证明代码是类型安全的情况下就禁止了警告，那么你就给了自己代码是安全的错误感觉。也许这么做之后在编译代码时不会出现任何警告，但它仍可以在运行时抛出ClassCastException异常。然而，如果你忽略了（注意，是忽略，不是禁止）那些你知道认为是安全的未检查警告，那么当出现一个真的有问题的警告时你也不会发现。新的警告将会淹没在那些你没有禁止掉的错误警告里。

