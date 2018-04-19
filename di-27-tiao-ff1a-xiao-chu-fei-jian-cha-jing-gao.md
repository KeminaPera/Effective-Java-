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

The SuppressWarnings annotation can be used on any declaration, from an individual local variable declaration to an entire class. Always use the SuppressWarnings annotation on the smallest scope possible. Typically this will be a variable declaration or a very short method or constructor. Never use SuppressWarnings on an entire class. Doing so could mask critical warnings.

SuppressWarnings注解可以在任意声明上使用，从单独的局部变量到整个类都可以。应该在尽可能小的作用域上使用SuppressWarnings注解。它通常是个变量声明或者是一个非常短的方法或者是构造器。永远不要在整个类上使用SuppressWarnings注解，这么做会掩盖某些重要的警告。

If you find yourself using the SuppressWarnings annotation on a method or constructor that’s more than one line long, you may be able to move it onto a local variable declaration. You may have to declare a new local variable, but it’s worth it. For example, consider this toArray method, which comes from ArrayList:

如果你发现你正在一个超过一行的方法或者构造器上使用SuppressWarnings注解，那么你可能需要将它移到某个局部变量声明上。也许你需要声明一个新的局部变量，但这么做是值得的。例如，考虑下面的toArray方法，这个方法来自与ArrayList：

```
public <T> T[] toArray(T[] a) { 
    if (a.length < size)
        return (T[]) Arrays.copyOf(elements, size, a.getClass()); 
    System.arraycopy(elements, 0, a, 0, size);
    if (a.length > size)
        a[size] = null; 
    return a;
}
```

If you compile ArrayList, the method generates this warning:

如果你编译这个ArrayList，那么这个方法将会生成这个警告：

```
ArrayList.java:305: warning: [unchecked] unchecked cast return (T[]) Arrays.copyOf(elements, size, a.getClass());
                                                                     ^ 
required: T[]
found: Object[]
```

It is illegal to put a SuppressWarnings annotation on the return statement, because it isn’t a declaration \[JLS, 9.7\]. You might be tempted to put the annotation on the entire method, but don’t. Instead, declare a local variable to hold the return value and annotate its declaration, like so:

将SuppressWarnings注解放在返回语句上是合法的，因为它不是一个声明\[JLS, 9.7\]。你可能会想将这个注解放在整个方法之上，但别这么做，而是应该声明一个局部变量来保存返回值，并且注解这个局部变量的声明，就像下面这样：

```
// Adding local variable to reduce scope of @SuppressWarnings
public <T> T[] toArray(T[] a) {
    if (a.length < size) {
        // This cast is correct because the array we're creating
        // is of the same type as the one passed in, which is T[].
        @SuppressWarnings("unchecked") 
        T[] result = (T[]) Arrays.copyOf(elements, size, a.getClass());
        return result;
    }
    System.arraycopy(elements, 0, a, 0, size); 
    if (a.length > size)
        a[size] = null; 
    return a;
}
```

The resulting method compiles cleanly and minimizes the scope in which unchecked warnings are suppressed.  
Every time you use a @SuppressWarnings\("unchecked"\) annotation, add a comment saying why it is safe to do so.This will help others understand the code, and more importantly, it will decrease the odds that someone will modify the code so as to make the computation unsafe. If you find it hard to write such a comment, keep thinking. You may end up figuring out that the unchecked operation isn’t safe after all.

这样的方法在编译时不会产生警告，而且禁止非检查警告的范围也被最小化。每次你使用@SuppressWarnings\("unchecked"\)注解，都应该添加一句注释，说明加这个注解是安全的。这么做可以帮助别人理解这段代码，而且更重要的是，它将减少别人修改代码后导致计算不安全的概率。如果你发现难以写这段注释，那么就继续思考思考吧。最终你可能会发现该非检查操作是不安全的。

In summary, unchecked warnings are important. Don’t ignore them. Every unchecked warning represents the potential for a ClassCastException at runtime. Do your best to eliminate these warnings. If you can’t eliminate an unchecked warning and you can prove that the code that provoked it is type safe, suppress the warning with a @SuppressWarnings\("unchecked"\) annotation in the narrowest possible scope. Record the rationale for your decision to suppress the warning in a comment.

总之，未检查警告是很重要的。别忽略它们。每个未检查警告都表示可能在运行时出现ClassCastException异常。尽你所能去消除这些警告。如果你无法消除某个未检查警告而且你能证明引起这个警告的代码是类型安全的，那么就在最小的范围内用@SuppressWarnings\("unchecked"\)注解去禁止这个警告，并且在注释里记录你禁止警告的理由。

