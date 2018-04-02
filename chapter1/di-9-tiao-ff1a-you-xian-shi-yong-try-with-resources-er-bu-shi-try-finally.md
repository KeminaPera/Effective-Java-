### 第9条：优先使用try-with-resources而不是try-finally

The Java libraries include many resources that must be closed manually by invoking a _close_ method. Examples  
 include _InputStream_,_OutputStream_, and _java.sql.Connection_. Closing resources is often overlooked by clients, with predictably dire performance consequences. While many of these resources use finalizers as a safety net, finalizers don’t work very well \(Item 8\). Historically, a _try-finally_ statement was the best way to guarantee that a resource would be closed properly, even in the face of an exception or return:

Java类库里包含了必须通过调用_close_方法来手动关闭的资源。比如_InputStream_，_OutputStream_还有_java.sql.Connection_。关闭资源这个动作通常被客户端忽视了，其性能表现也可想而知。虽然大部分这些资源都使用终结方法作为最后的安全线，但终结方法的效果并不是很好（条目8）。在过去的实践当中，_try-finally_语句是保证一个资源被恰当关闭的最好的方式，即使是在程序抛出异常或者返回的情况下：

```
// try-finally - No longer the best way to close resources!
static String firstLineOfFile(String path) throws IOException { 
    BufferedReader br = new BufferedReader(new
    FileReader(path)); 
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

Even the correct code for closing resources with try-finally statements, as illustrated in the previous two code examples, has a subtle deficiency. The code in both the try block and the finally block is capable of throwing exceptions. For example, in the firstLineOfFile method, the call to readLine could throw an exception due to a failure in the underlying physical device, and the call to close could then fail for the same reason.

即使对于正确使用了try-finally语句的代码，如前面所示，也有个不起眼的缺点。无论是try里面的代码还是finally里面的代码，都有可能抛出异常。例如，在firstLineOfFile方法里，如果底层物理设备出了故障，则在调用readLine方法时会抛出异常，而且由于相同的原因，调用close方法也会失败。





