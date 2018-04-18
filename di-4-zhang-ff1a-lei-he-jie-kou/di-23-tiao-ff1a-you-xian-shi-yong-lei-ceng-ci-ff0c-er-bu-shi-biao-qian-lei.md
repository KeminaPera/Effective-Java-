### 第23条：优先使用类层次，而不是标签类

Occasionally you may run across a class whose instances come in two or more flavors and contain a tag field indicating the flavor of the instance. For example, consider this class, which is capable of representing a circle or a rectangle:

有时候，你可能会遇到这么一个情况：一个类有两种或多种风格的实例，这个类包含了一个指明实例风格的标签。例如，考虑下面这个类，它能表示一个圆形或者一个矩形：

```
// Tagged class - vastly inferior to a class hierarchy!
class Figure {
    enum Shape { RECTANGLE, CIRCLE };
    // Tag field - the shape of this figure
    final Shape shape;
    // These fields are used only if shape is RECTANGLE
    double length;
    double width;
    // This field is used only if shape is CIRCLE
    double radius;
    // Constructor for circle
    Figure(double radius) {
        shape = Shape.CIRCLE;
        this.radius = radius;
    } 
    // Constructor for rectangle
    Figure(double length, double width) {
        shape = Shape.RECTANGLE;
        this.length = length;
        this.width = width;
    } 
    double area() {
        switch(shape) {
            case RECTANGLE:
                return length * width;
            case CIRCLE:
                return Math.PI * (radius * radius);
            default:
                throw new AssertionError(shape);
        }
    }
}
```

Such tagged classes have numerous shortcomings. They are cluttered with boilerplate, including enum declarations, tag fields, and switch statements. Readability is further harmed because multiple implementations are jumbled together in a single class. Memory footprint is increased because instances are burdened with irrelevant fields belonging to other flavors. Fields can’t be made final unless constructors initialize irrelevant fields, resulting in more boilerplate. Constructors must set the tag field and initialize the right data fields with no help from the compiler: if you initialize the wrong fields, the program will fail at runtime. You can’t add a flavor to a tagged class unless you can modify its sourcefile. If you do add a flavor, you must remember to add a case to every switch statement, or the class will fail at runtime. Finally, the data type of an instance gives no clue as to its flavor. In short, **tagged classes are verbose, error-prone, and inefficient.**

这样的标签类有许多的缺陷。这些类里包含了混乱的模板代码，包括枚举声明，标签域，还有switch语句。可阅读性被严重破坏，因为多个实现都被塞进同一个类里了。由于标签类的实例负担着许多属于不同风格的不相关域，内存占用也随着增加。除非不同构造器初始化的域是不相关的，否则这些域不能设成final，而这又会导致更多的模板代码。构造器必须设置标签域，而且要在没有编译器的帮助下去初始化正确的数据域：如果你初始化的域错了，程序将会在运行时失败。除非你去修改源文件，否则你无法再往标签类里添加风格。如果你确实需要添加新的风格，那么你必须要记住，每个switch语句都要添加新的case分支，否则类在运行时会失败。最后，我们无法从实例的数据类型看出它是属于哪个风格。简而言之，标签类不仅冗长，容易出错，而且效率也不高。

Luckily, object-oriented languages such as Java offer a far better alternative for defining a single data type capable of representing objects of multiple flavors: subtyping. **A tagged class is just a pallid imitation of a class hierarchy.**

幸运的是，像Java这类的面向对象语言提供了一个更好的方式来定义一个能表示多个风格对象的数据类型：子类型（subtyping）。**标签类仅仅是类层次的一个简单模仿。**

To transform a tagged class into a class hierarchy, first define an abstract class containing an abstract method for each method in the tagged class whose behavior depends on the tag value. In the Figure class, there is only one such method, which is area. This abstract class is the root of the class hierarchy. If there are any methods whose behavior does not depend on the value of the tag, put them in this class. Similarly, if there are any data fields used by all the flavors, put them in this class. There are no such flavor-independent methods or fields in the Figure class. Next, define a concrete subclass of the root class for each flavor of the original tagged class. In our example, there are two: circle and rectangle. Include in each subclass the data fields particular to its flavor. In our example, radius is particular to circle, and length and width are particular to rectangle. Also include in each subclass the appropriate implementation of each abstract method in the root class. Here is the class hierarchy corresponding to the original Figure class:

为了将标签类转换成类层次，我们首先要定义好抽象类，这个抽象类应该为标签类里的每个行为依赖于标签值的方法编写一个对应的抽象方法。在Figure类里，只有一个这种方法，即是area方法。抽象类是类层次的根。对于有任一行为基于标签值的方法，都将其加入抽象类。类似地，对于所有风格都会用到的数据域，也将其放入抽象类。然后，为原本的标签类的每一个风格都定义一个根类的子类。在我们的例子当中，有两个风格：圆形和矩形。每个子类中包含对应风格各自特有的数据域。在我们的例子当中，半径是圆形特有的，长和宽是矩形特有的。当然，每个子类也要为根类中每个抽象方法编写一个恰当的实现。下面是原本的Figure类所对应的类层次：

```
// Class hierarchy replacement for a tagged class
abstract class Figure {
    abstract double area();
}
class Circle extends Figure {
    final double radius;
    Circle(double radius) { 
        this.radius = radius; 
    }
    @Override 
    double area() { 
        return Math.PI * (radius * radius); 
    }
} 
class Rectangle extends Figure {
    final double length;
    final double width;
    Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    } 
    @Override 
    double area() { 
        return length * width;
    }
}
```

This class hierarchy corrects every shortcoming of tagged classes noted previously. The code is simple and clear, containing none of the boilerplate found in the original. The implementation of each flavor is allotted its own class, and none of these classes is encumbered by irrelevant data fields. All fields are final. The compiler ensures that each class’s constructor initializes its data fields and that each class has an implementation for every abstract method declared in the root class. This eliminates the possibility of a runtime failure due to a missing switch case. Multiple programmers can extend the hierarchy independently and interoperably without access to the source for the root class. There is a separate data type associated with each flavor, allowing programmers to indicate the flavor of a variable and to restrict variables and input parameters to a particular flavor.

这个类层次克服了前面提到的标签类的每个缺陷。这份代码简单而且清晰，没有原本Figure类的那些模板代码。每个风格的实现都在各自的类里，而且这些类都没有不相关的数据域。所有的域都是final的。编译器可以确保每个类的构造器都是构造它自己的数据域，而且每个类都实现了根类里声明的每一个抽象方法。由于switch case代码的消除，运行时失败的可能性也随之减少。不同程序员可以独自去扩展类层次，而且不用访问根类的源码就可以相互合作。每个风格分别关联了一个不同的数据类型，允许程序员指明一个变量的风格类型，并将变量和输入参数限定为指定的风格类型。

Another advantage of class hierarchies is that they can be made to reflect natural hierarchical relationships among types, allowing for increased flexibility and better compile-time type checking. Suppose the tagged class in the original example also allowed for squares. The class hierarchy could be made to reflect the fact that a square is a special kind of rectangle \(assuming both are immutable\):

类层次的另一个优点是，它们反应了类型之间的自然层次关系，有助于更好的灵活性，并进行更好的编译时类型检查。假设Figure类也支持正方形，那么类层次可以反应出正方形是一种特殊的长方形这么一个事实（假设两者都是不可变的）：

```
class Square extends Rectangle {
    Square(double side) {
        super(side, side);
    }
}
```

Note that the fields in the above hierarchy are accessed directly rather than by accessor methods. This was done for brevity and would be a poor design if the hierarchy were public \(Item 16\).

In summary, tagged classes are seldom appropriate. If you’re tempted to write a class with an explicit tag field, think about whether the tag could be eliminated and the class replaced by a hierarchy. When you encounter an existing class with a tag field, consider refactoring it into a hierarchy.

