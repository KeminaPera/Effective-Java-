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

这样的标签类有许多的缺陷。这些类里包含了混乱的模板代码，包括枚举声明，标签域，还有switch语句。可阅读性被严重破坏，因为多个实现都被塞进同一个类里了。由于标签类的实例负担着许多属于不同风格的不相关域，内存占用也随着增加。除非不同构造器初始化的域是不相关的，否则这些域不能设成final，而这又会导致更多的模板代码。构造器必须设置标签域，而且要在没有编译器的帮助下去初始化正确的数据域：如果你初始化的域错了，程序将会在运行时失败。除非你去修改源文件，否则你无法再往标签类里添加风格。如果你确实需要添加新的风格，那么你必须要记住，每个switch语句都要添加新的case分支，否则类在运行时会失败。最后，

Luckily, object-oriented languages such as Java offer a far better alternative for defining a single data type capable of representing objects of multiple flavors: subtyping. **A tagged class is just a pallid imitation of a class hierarchy.**

