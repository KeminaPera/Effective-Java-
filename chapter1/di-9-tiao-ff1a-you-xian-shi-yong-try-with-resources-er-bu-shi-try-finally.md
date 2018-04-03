### 第9条：优先使用try-with-resources而不是try-finally

The Java libraries include many resources that must be closed manually by invoking a _close_ method. Examples  
 include _InputStream_,_OutputStream_, and _java.sql.Connection_. Closing resources is often overlooked by clients, with predictably dire performance consequences. While many of these resources use finalizers as a safety net, finalizers don’t work very well \(Item 8\). Historically, a _try-finally_ statement was the best way to guarantee that a resource would be closed properly, even in the face of an exception or return:

Java类库里包含了必须通过调用_close_方法来手动关闭的资源。比如_InputStream_，_OutputStream_还有_java.sql.Connection_。关闭资源这个动作通常被客户端忽视了，其性能表现也可想而知。虽然大部分这些资源都使用终结方法作为最后的安全线，但终结方法的效果并不是很好（条目8）。在过去的实践当中，_try-finally_语句是保证一个资源被恰当关闭的最好的方式，即使是在程序抛出异常或者返回的情况下：

```
// try-finally - No longer the best way to close resources!
static String firstLineOfFile(String path) throws IOException { 
    BufferedReader br = new BufferedReader(new FileReader(path)); 
    try {
        return br.readLine(); 
    } finally {
        br.close(); 
    }
}
```

This may not look bad, but it gets worse when you add a second resource:

这么做看起来可能还没什么问题，但当你添加第二个资源时，情况就开始变得糟糕了：

```
// try-finally is ugly when used with more than one resource!
static void copy(String src, String dst) throws IOException {
    InputStream in = new FileInputStream(src); 
    try {
        OutputStream out = new FileOutputStream(dst); 
        try {
            byte[] buf = new byte[BUFFER_SIZE]; 
            int n;
            while ((n = in.read(buf)) >= 0)
                out.write(buf, 0, n); 
        } finally {
            out.close();
        }
    } finally {
        in.close(); 
    }
}
```

It may be hard to believe, but even good programmers got this wrong most of the time. For starters, I got it wrong on page 88 of _Java Puzzlers_\[Bloch05\], and no one noticed for years. In fact, two-thirds of the uses of the close method in the Java libraries were wrong in 2007.

这可能会有点难以置信，但即使是好的程序员在很多时候也会犯这个错误。首先，我在Java Puzzlers\[Bloch05\]这本书的第88页上搞错了，而且很多年过去了也没人发现。事实上，2007年的Java类库里三分之二的close方法都用错了。

Even the correct code for closing resources with try-finally statements, as illustrated in the previous two code examples, has a subtle deficiency. The code in both the try block and the finally block is capable of throwing exceptions. For example, in the firstLineOfFile method, the call to readLine could throw an exception due to a failure in the underlying physical device, and the call to close could then fail for the same reason. Under these circumstances, the second exception completely obliterates the first one. There is no record of the first exception in the exception stack trace, which can greatly complicate debugging in real systems—usually it’s the first exception that you want to see in order to diagnose the problem. While it is possible to write code to suppress the second exception in favor of the first, virtually no one did because it’s just too verbose.

即使对于正确使用了try-finally语句的代码，如前面所示，也有个不起眼的缺点。无论是try里面的代码还是finally里面的代码，都有可能抛出异常。例如，在firstLineOfFile方法里，如果底层物理设备出了故障，则在调用readLine方法时会抛出异常，而且由于相同的原因，调用close方法也会失败。在这种情况下，第二种异常覆盖了第一种异常。在异常错误栈里将没有第一种异常的记录，这会使实际系统的调试变得很复杂，因为很多时候你是想查看第一种异常来诊断问题。虽然我们可以通过编写代码抑制第二种异常来让第一种异常显现出来，但几乎没人会这么做，因为这样的话代码就变得太冗长了。

All of these problems were solved in one fell swoop when Java 7 introduced the try-with-resources statement \[JLS, 14.20.3\]. To be usable with this construct, a resource must implement the AutoCloseable interface, which consists of a single void-returning close method. Many classes and interfaces in the Java libraries and in third-party libraries now implement or extend AutoCloseable. If you write a class that represents a resource that must be closed, your class should implement AutoCloseable too.

当Java 7引入try-with-resources语句\[JLS, 14.20.3\]时，所有问题突然一下子解决了。若要使用这个语句，一个资源必须实现AutoCloseable接口，而这个接口只有一个返回类型为void的close（void-returning）方法。Java类库和第三方类库里面的许多类和接口现在都实现或继承了AutoCloseable接口。如果你写了一个类，这个类代表一个必须被关闭的资源，那么你的类也应该实现AutoCloseable接口。

Here’s how our first example looks using try-with-resources:

下面这个例子展示了如何使用try-with-resources语句：

```
// try-with-resources - the the best way to close resources!
static String firstLineOfFile(String path) throws IOException { 
    try (
        BufferedReader br = new BufferedReader(new FileReader(path))
    ) { 
        return br.readLine();
    } 
}
```

And here’s how our second example looks using try-with-resources:

这是我们使用try-with-resources的第二个例子：

```
// try-with-resources on multiple resources - short and sweet
static void copy(String src, String dst) throws IOException {
    try (
        InputStream in = new FileInputStream(src); 
        OutputStream out = new FileOutputStream(dst)
    ) {
        byte[] buf = new byte[BUFFER_SIZE]; int n;
        while ((n = in.read(buf)) >= 0)
            out.write(buf, 0, n); 
    }
}
```

Not only are the try-with-resources versions shorter and more readable than the originals, but they provide far better diagnostics. Consider the firstLineOfFile method. If exceptions are thrown by both the readLine call and the \(invisible\)close, the latter exception is suppressed in favor of the former. In fact, multiple exceptions may be suppressed in order to preserve the exception that you actually want to see. These suppressed exceptions are not merely discarded; they are printed in the stack trace with a notation saying that they were suppressed. You can also access them programmatically with the getSuppressed method, which was added toThrowable in Java 7.

比起try-finally，try-with-resources语句不仅更简短和更可读，而且它们更容易排查问题。考虑firstLineOfFile方法的情况，如果从readLine方法和close（不可见）方法都抛出异常，那么后者抛出的异常将被抑制而不是前者。事实上，为了保留我们实际想看的异常，多个异常都可能会被抑制。这些被抑制的异常并不仅仅是被忽略了，它们被打印在错误栈当中，并被标注为被抑制了。

You can put catch clauses on try-with-resources statements, just as you can on regular try-finally statements. This allows you to handle exceptions without sullying your code with another layer of nesting. As a slightly contrived example, here’s a version our firstLineOfFile method that does not throw exceptions, but takes a default value to return if it can’t open the file or read from it:

我们也可以像之前的try-finally语句那样，往try-with-resources里面添加catch子句。这能让我们无需在另一层嵌套污染代码就能处理异常。下面是一个比较刻意的例子，这个版本中的firstLineOfFile方法不会抛出异常，但如果它不能打开文件或者不能读打开的文件，它将返回一个默认值：

```
// try-with-resources with a catch clause
static String firstLineOfFile(String path, String defaultVal) { 
    try (
        BufferedReader br = new BufferedReader(new FileReader(path))
    ) { 
        return br.readLine();
    } catch (IOException e) { 
        return defaultVal;
    } 
}
```

The lesson is clear: Always use try-with-resources in preference to try-finally when working with resources that must be closed. The resulting code is shorter and clearer, and the exceptions that it generates are more useful. The try-with-resources statement makes it easy to write correct code using resources that must be closed, which was practically impossible using try-finally.

结论很明显：面对必须要关闭的资源，我们总是应该优先使用try-with-resources而不是try-finally。随之产生的代码更简短，更清晰，产生的异常对我们也更有用。try-with-resources语句让我们更容易编写必须要关闭的资源的代码，若采用try-finally则几乎做不到这点。

