### 第20条：接口优于抽象类

Java has two mechanisms to define a type that permits multiple implementations: interfaces and abstract classes. Since the introduction of default methods for interfaces in Java 8 \[JLS 9.4.3\], both mechanisms allow you to provide implementations for some instance methods. A major difference is that to implement the type defined by an abstract class, a class must be a subclass of the abstract class. Because Java permits only single inheritance, this restriction on abstract classes severely constrains their use as type definitions. Any class that defines all the required methods and obeys the general contract is permitted to implement an interface, regardless of where the class resides in the class hierarchy.

Java有两种机制可以定义一个允许多实现的类型：接口和抽象类。由于在Java 8\[JLS 9.4.3\]中为接口引入了默认方法，所以现在两种机制都能让你为一些实例方法提供实现。一个主要的不同点是，若要实现由一个抽象类定义的类型，那么这个类必须是抽象类的一个子类。因为Java只允许单继承，所以这严格约束了抽象类作为类型定义的使用。

