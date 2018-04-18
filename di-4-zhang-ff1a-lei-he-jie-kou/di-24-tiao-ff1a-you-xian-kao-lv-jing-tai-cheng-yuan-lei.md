### 第24条：优先考虑静态成员类

A nested class is a class defined within another class. A nested

class should exist only to serve its enclosing class. If a nested class

would be useful in some other context, then it should be a top-level

class. There are four kinds of nested classes: static member

classes, nonstatic member classes, anonymous classes, and localclasses. All but the first kind are known as inner classes. This item

tells you when to use which kind of nested class and why.

A static member class is the simplest kind of nested class. It is best

thought of as an ordinary class that happens to be declared inside

another class and has access to all of the enclosing class’s members,

even those declared private. A static member class is a static

member of its enclosing class and obeys the same accessibility

rules as other static members. If it is declared private, it is

accessible only within the enclosing class, and so forth.

