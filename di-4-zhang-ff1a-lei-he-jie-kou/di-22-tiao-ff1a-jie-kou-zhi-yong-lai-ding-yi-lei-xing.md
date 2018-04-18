### 第22条：接口只用来定义类型

When a class implements an interface, the interface serves as  
 a type that can be used to refer to instances of the class. That a  
 class implements an interface should therefore say something  
 about what a client can do with instances of the class. It is  
 inappropriate to define an interface for any other purpose.

当一个类实现了一个接口，这个接口可以作为一个类型，并作为实现它的类实例的引用。因此，若一个类实现了一个接口，则表明了客户端可以如何处理该类的实例。为其它目的来定义一个接口是不合适的。

One kind of interface that fails this test is the so-called constant interface. Such an interface contains no methods; it consists solely of static final fields, each exporting a constant. Classes using these constants implement the interface to avoid the need to qualify constant names with a class name. Here is an example:

有种接口并不符合上述的目的，它就是所谓的常量接口。这种接口不包含方法；它仅有静态final域组成，每个域导出一个常量。需要使用这些常量的类通过实现接口而避免了需要用类名来限定常量名。下面是一个例子：

```
// Constant interface antipattern - do not use!
public interface PhysicalConstants {
    // Avogadro's number (1/mol)
    static final double AVOGADROS_NUMBER = 6.022_140_857e23;
    // Boltzmann constant (J/K)
    static final double BOLTZMANN_CONSTANT = 1.380_648_52e-23;
    // Mass of the electron (kg)
    static final double ELECTRON_MASS = 9.109_383_56e-31;
}
```

**The constant interface pattern is a poor use of interfaces.** That a class uses some constants internally is an implementation detail. Implementing a constant interface causes this implementation detail to leak into the class’s exported API. It is of no consequence to the users of a class that the class implements a constant interface. In fact, it may even confuse them. Worse, it represents a commitment: if in a future release the class is modified so that it no longer needs to use the constants, it still must implement the interface to ensure binary compatibility. If a nonfinal class implements a constant interface, all of its subclasses will have their namespaces polluted by the constants in the interface.

**通过常量接口模式来使用接口是很糟糕的。**一个类是否要使用内部常量是属于它的实现细节。实现一个常量接口会导致类的导出API里泄露了这个实现细节。一个类是否实现一个常量接口，对于该类的用户是没有影响的。事实上，这甚至会使他们感到困惑。更糟糕的是，它还代表了一个承诺：在未来的版本中，如果这个类被修改了，从此不再需要用到这些常量，但它为了保持二进制兼容性仍然必须实现这个接口。如果一个非final类实现了一个常量接口，那么它的所有子类的命名空间都将被接口的常量污染。

There are several constant interfaces in the Java platform libraries, such as java.io.ObjectStreamConstants. These interfaces should be regarded as anomalies and should not be emulated.

Java类库里有几个常量类，如java.io.ObjectStreamConstants。这些接口是不规范的，不要去模仿它们。

If you want to export constants, there are several reasonable choices. If the constants are strongly tied to an existing class or interface, you should add them to the class or interface. For example, all of the boxed numerical primitive classes, such as Integer and Double, export MIN\_VALUE and MAX\_VALUE constants. If the constants are best viewed as members of an enumerated type, you should export them with an enum type \(Item 34\). Otherwise, you should export the constants with a noninstantiable utility class \(Item 4\). Here is a utility class version of the PhysicalConstants example shown earlier:

如果你想导出常量，有几种合适的方式。如果常量与现有类或现有接口紧密相关，你应该将它们添加到类里或接口里。例如，所有的装箱数值基本类，如Integer和Double，都导出了MIN\_VALUE和MAX\_VALUE常量。如果这些常量最好是被看成一个枚举类型的成员，你应该将它们用一个枚举类型来导出（条目34）。否则，你应该将这些常量用一个不可初始化的工具类来导出（条目4）。下面是PyscialConstants的工具类版本：

```
// Constant utility class
package com.effectivejava.science;
public class PhysicalConstants {
    private PhysicalConstants() { } // Prevents instantiation
    public static final double AVOGADROS_NUMBER = 6.022_140_857e23;
    public static final double BOLTZMANN_CONST =1.380_648_52e-23;
    public static final double ELECTRON_MASS = 9.109_383_56e-31;
}
```

Incidentally, note the use of the underscore character \(\_\) in the numeric literals. Underscores, which have been legal since Java 7, have no effect on the values of numeric literals, but can make them much easier to read if used with discretion. Consider adding underscores to numeric literals, whether fixed of floating point, if they contain five or more consecutive digits. For base ten literals, whether integral or floating point, you should use underscores to separate literals into groups of three digits indicating positive and negative powers of one thousand.

顺便说一下，注意到上述例子中数值字面值的下划线字符（\_）。从Java 7开始，下划线是合法的，对数值字面值的值没有影响，使用得当的话还能使它们更容易阅读。无论是否是固定浮点数，如果它们包含一个或多个连续数字，则可以考虑往数值字面值里添加下划线。对于底数为10的字面值，无论是整型还是浮点，我们应该用下划线来把字面值分成三组数字，表示一千的正负幂。

Normally a utility class requires clients to qualify constant names with a class name, for example, PhysicalConstants.AVOGADROS\_NUMBER. If you make heavy use of the constants exported by a utility class, you can avoid the need for qualifying the constants with the class name by making use of the static import facility:

一般情况下，工具类要求客户端用类名来限制常量名，例如，PhysicalConstants.AVOGADROS\_NUMBER。如果你需要经常用到某个工具类的导出常量，你可以通过静态导入来避免用类名去限制常量名。

```
// Use of static import to avoid qualifying constants
import static com.effectivejava.science.PhysicalConstants.*;
public class Test {
    double atoms(double mols) {
        return AVOGADROS_NUMBER * mols;
    } 
    ... // Many more uses of PhysicalConstants justify static import
}
```

In summary, interfaces should be used only to define types. They should not be used merely to export constants.

总之，接口应该只被用来定义类型。它们不能仅仅是用来导出常量。

