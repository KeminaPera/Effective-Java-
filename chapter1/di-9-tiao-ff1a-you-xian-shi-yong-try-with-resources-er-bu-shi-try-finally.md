### 第9条：优先使用try-with-resources而不是try-finally

The Java libraries include many resources that must be closed manually by invoking a close method. Examples  
 include InputStream,OutputStream, and java.sql.Connection. Closing resources is often overlooked by clients, with predictably dire performance consequences. While many of these resources use finalizers as a safety net, finalizers don’t work very well \(Item 8\). Historically, a try-finally statement was the best way to guarantee that a resource would be closed properly, even in the face of an exception or return:

Java类库里包含了必须通过调用close方法来手动关闭的资源。比如InputStream，OutputStream还有java.sql.Connection。关闭资源这个动作通常被客户端忽视了，其性能表现也可想而知。虽然大部分这些资源都使用终结方法作为最后的安全线，但终结方法的效果并不是很好（条目8）。在过去的实践当中，try-finally语句是保证一个资源被恰当关闭的最好的方式，即使是在程序抛出异常或者返回的情况下：

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



