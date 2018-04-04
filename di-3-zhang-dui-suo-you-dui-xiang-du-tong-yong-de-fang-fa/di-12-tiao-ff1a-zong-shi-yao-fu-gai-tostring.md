### 第12条：总是要覆盖toString

While Object provides an implementation of the toString method, the string that it returns is generally not what the user of your class wants to see. It consists of the class name followed by an “at” sign \(@\) and the unsigned hexadecimal representation of the hash code, for example, PhoneNumber@163b91. The general contract for toString says that the returned string should be “a concise but informative representation that is easy for a person to read.” While it could be argued that PhoneNumber@163b91 is concise and easy to read, it isn’t very informative when compared to 707-867-5309.

虽然Object提供了一个toString方法的实现，但返回的字符串并不是用户想看的。这个返回的字符串包含了类名，随后跟着一个@符号以及无符号的十六进制的哈希码，例如，PhoneNumber@163b91。toString方法的通用约定里规定，返回的字符串应该是“简洁，但信息丰富，易于人们阅读的”。虽然你也可以说PhoneNumber@163b91简洁而且易于阅读，但比起707-867-5309，它的信息量就不够丰富了。

The toString contract goes on to say, “It is recommended that all subclasses override this method.” Good advice, indeed!

toString方法的通用约定接着说道，“建议所有的子类都覆盖这个方法”。这的确是条好的建议！

While it isn’t as critical as obeying the equals and hashCode contracts \(Items 10 and 11\), providing a good toString implementation makes your class much more pleasant to use and makes systems using the class easier to debug. The toString method is automatically invoked when an object is passed to println, printf, the string concatenation operator, or assert, or is printed by a debugger. Even if you never call toString on an object, others may. For example, a component that has a reference to your object may include the string representation of the object in a logged error message. If you fail to override toString, the message may be all but useless.

