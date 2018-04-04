### 第12条：始终要覆盖toString

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

**When practical, the toString method should return all of the interesting information contained in the object**, as shown in the phone number example. It is impractical if the object is large or if it contains state that is not conducive to string representation. Under these circumstances, toString should return a summary such as Manhattan residential phone directory \(1487536 listings\) or Thread\[main,5,main\]. Ideally, the string should be self-explanatory. \(The Thread example flunks this test.\) A particularly annoying penalty for failing to include all of an object’s interesting information in its string representation is test failure reports that look like this:

**在实际应用当中，toString方法应该返回类里面所有有用的信息**，就像前面所示的电话号码一样。如果对象太大或者对象中包含的状态信息难以用字符串表述，这么做就有点不切实际。在这种情况下，toString方法应该返回一个摘要信息，例如"Manhattan residential phone directory\(1487536 listings\)"或者“Thread\[main,5,main\]”。理想状态下，一个字符串应该是能自解释的。（Thread的例子不满足这个要求。）如果在字符串展示中没有包含对象的所有有用信息，那么在测试失败的报告中将会是这样子的：

```
Assertion failure: expected {abc, 123}, but was {abc, 123}.
```

One important decision you’ll have to make when implementing a toString method is whether to specify the format of the return value in the documentation. It is recommended that you do this for value classes, such as phone number or matrix. The advantage of specifying the format is that it serves as a standard, unambiguous, human-readable representation of the object. This representation can be used for input and output and in persistent human-readable data objects, such as CSV files. If you specify the format, it’s usually a good idea to provide a matching static factory or constructor so programmers can easily translate back and forth between the object and its string representation. This approach is taken by many value classes in the Java platform libraries, including BigInteger, BigDecimal, and most of the boxed primitive classes.

在实现toString方法时，我们必须做一个很重要的决定，那就是是否要在文档中指定返回值的格式。对于值类（value class），如电话号码或矩阵，是推荐这么做的。指定格式的好处是，它可以被用作标准的，明确的，可阅读的对象表示法。这种表示法可以被用于输入和输出，以及用在人类可阅读的数据对象上，如CSV文件。如果你指定了这个格式，最好再提供一个相匹配的静态工厂或者构造器，这样程序员能方便地在对象和它的字符串展示之间来回转换。Java类库里很多值类都采取了这种方式，包括BigInteger，BigDecimal和大多数装箱基础类。

The disadvantage of specifying the format of the toString return value is that once you’ve specified it, you’re stuck with it for life, assuming your class is widely used. Programmers will write code to parse the representation, to generate it, and to embed it into persistent data. If you change the representation in a future release, you’ll break their code and data, and they will yowl. By choosing not to specify a format, you preserve the flexibility to add information or improve the format in a subsequent release.

指定toString返回值的格式的缺点是，如果这个类已经被广泛使用，那么一旦指定了这个格式，接下来都很难摆脱它。程序员将编写程序来解析这种字符串表示，生成字符串表示，以及把字符串表示嵌入持久化数据里。如果在未来版本中你改变了这种表示法，你将会破坏他们的代码和数据，那样的话他们会咆哮起来的。若选择不指明格式，那我们就仍然持有添加信息或者在未来版本改善格式的灵活性。

**Whether or not you decide to specify the format, you should clearly document your intentions.**If you specify the format, you should do so precisely. For example, here’s a toString method to go with the PhoneNumber class inItem 11:

**无论我们是否决定要指明格式，我们都应该清晰地在文档中表达我们的意图。**如果我们指定了格式，则应该严格地去这样做。例如，下面是条目11里提到的PhoneNumber类的toString方法：

```
/**
 * Returns the string representation of this phone number.
 * The string consists of twelve characters whose format is
 * "XXX-YYY-ZZZZ", where XXX is the area code, YYY is the
 * prefix, and ZZZZ is the line number. Each of the capital
 * letters represents a single decimal digit. *
 * If any of the three parts of this phone number is too small
 * to fill up its field, the field is padded with leading zeros.
 * For example, if the value of the line number is 123, the last
 * four characters of the string representation will be "0123". 
 */
@Override 
public String toString() {
    return String.format("%03d-%03d-%04d",areaCode, prefix, lineNum); 
}
```

If you decide not to specify a format, the documentation comment should read something like this:

如果决定不指定格式，则文档注释应该是像这样子的：

```
/**
 * Returns a brief description of this potion. The exact details
 * of the representation are unspecified and subject to change,
 * but the following may be regarded as typical: *
 * "[Potion #9: type=love, smell=turpentine, look=india ink]" 
 */
@Override public String toString() { ... }
```

After reading this comment, programmers who produce code or persistent data that depends on the details of the format will have no one but themselves to blame when the format is changed.

对于那些依赖于格式细节进行编程或者持久化数据的程序员，在读完这段注释后，一旦格式改变了，则只能自己承担后果。

Whether or not you specify the format, provide programmatic access to the information contained in the value returned by toString. For example, the PhoneNumber class should contain accessors for the area code, prefix, and line number. If you fail to do this, you force programmers who need this information to parse the string. Besides reducing performance and making unnecessary work for programmers, this process is error-prone and results in fragile systems that break if you change the format. By failing to provide accessors, you turn the string format into a de facto API, even if you’ve specified that it’s subject to change.

无论是否指定格式，我们都应该为toString方法返回值中包含的所有信息提供一个编程式的访问途径。例如，PhoneNumber类应该包含area code，prefix和line number的访问方法。如果不这么做，将会强制需要这些信息的程序员去解析返回的字符串。除了降低性能和让程序员做不必要的工作外，这个解析过程还容易出错，而且还会导致系统变得脆弱，如果字符串格式变动了，系统甚至崩溃了。如果不提供这些访问方法，即使你已经说明了字符串的格式会变化，这个字符串格式也成了事实上的API。

It makes no sense to write a toString method in a static utility class \(Item 4\). Nor should you write a toString method in most enum types \(Item 34\) because Java provides a perfectly good one for you. You should, however, write a toString method in any abstract class whose subclasses share a common string representation. For example, the toString methods on most collection implementations are inherited from the abstract collection classes.

在静态工具类（条目4）里编写toString方法是无意义的。同时我们也不应该在大多数的枚举类型（条目34）里编写toString方法，因为Java为我们提供了一个很棒的实现。然而，我们应该为那些子类共享一个字符串表示的抽象类编写一个toString方法。例如，大多数集合实现的toString方法都是继承自抽象的集合类。



