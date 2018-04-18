### 第25条：将源文件限制为单个顶级类

While the Java compiler lets you define multiple top-level classes in a single source file, there are no benefits associated with doing so, and there are significant risks. The risks stem from the fact that defining multiple top-level classes in a source file makes it possible to provide multiple definitions for a class. Which definition gets used is affected by the order in which the source files are passed to the compiler.

虽然Java编译器能让你在一个源文件里定义多个顶级类，但这么做并没有什么好处，相反，会有一些严重的风险。这些风险都源于这么一个事实：在一个源文件里定义多个顶级类使得为类提供多个定义成为可能。使用哪个定义将会受源文件传递给编译器的顺序的影响。

To make this concrete, consider this source file, which contains only a Main class that refers to members of two other top-level classes \(Utensil and Dessert\):

为了更具体地说明这一点，考虑下面这个源文件，它只包含了一个Main类，这个类指向了另外两个顶级类（Utensil和Dessert）的成员：

```
public class Main {
    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME); 
    }
}
```

Now suppose you define both Utensil and Dessert in a single source file namedUtensil.java:

```
class Utensil {
    static final String NAME = "pan"; 
}
class Dessert {
    static final String NAME = "cake";
}
```



