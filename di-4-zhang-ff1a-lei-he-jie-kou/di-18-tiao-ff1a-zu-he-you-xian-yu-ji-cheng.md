### 第18条：组合优先于继承

Inheritance is a powerful way to achieve code reuse, but it is not always the best tool for the job. Used inappropriately, it leads to fragile software. It is safe to use inheritance within a package, where the subclass and the superclass implementations are under the control of the same programmers. It is also safe to use inheritance when extending classes specifically designed and documented for extension \(Item 19\). Inheriting from ordinary concrete classes across package boundaries, however, is dangerous. As a reminder, this book uses the word “inheritance” to mean implementation inheritance \(when one class extends another\). The problems discussed in this item do not apply to interface inheritance\(when a class implements an interface or when one interface extends another\).

继承（inheritance）是获得代码重用的一种有效途径，但不是最佳方式。使用不当的话，容易使做出来的软件变得脆弱。在一个包里使用继承是安全的，这样的话子类实现和父类实现都在同个程序员的控制之下。除此之外，当类是专门设计来继承而且具有良好文档话，采用继承来进行扩展也是安全的。但是，如果只是跨包继承一个普通具体的类，则是危险的。提示一下，本书采用“继承”来指实现继承（当一个类扩展了另一个类）。本条目讨论的问题不适用于接口继承（当一个类实现了一个接口或者一个接口扩展了另一个接口）。

Unlike method invocation, inheritance violates

encapsulation \[Snyder86\]. In other words, a subclass depends

on the implementation details of its superclass for its proper

function. The superclass’s implementation may change from

release to release, and if it does, the subclass may break, even

though its code has not been touched. As a consequence, a subclass

must evolve in tandem with its superclass, unless the superclass’sauthors have designed and documented it specifically for the

purpose of being extended.

