### 第18条：组合优先于继承

Inheritance is a powerful way to achieve code reuse, but it is not

always the best tool for the job. Used inappropriately, it leads to

fragile software. It is safe to use inheritance within a package,

where the subclass and the superclass implementations are under

the control of the same programmers. It is also safe to use

inheritance when extending classes specifically designed and

documented for extension \(Item 19\). Inheriting from ordinary

concrete classes across package boundaries, however, is dangerous.

As a reminder, this book uses the word “inheritance” to

mean implementation inheritance \(when one class extends

another\). The problems discussed in this item do not apply

to interface inheritance\(when a class implements an interface or

when one interface extends another\).

