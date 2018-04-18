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

The constant interface pattern is a poor use of interfaces. That a class uses some constants internally is an implementation detail. Implementing a constant interface causesthis implementation detail to leak into the class’s exported API. It is of no consequence to the users of a class that the class implements a constant interface. In fact, it may even confuse them. Worse, it represents a commitment: if in a future release the class is modified so that it no longer needs to use the constants, it still must implement the interface to ensure binary compatibility. If a nonfinal class implements a constant interface, all of its subclasses will have their namespaces polluted by the constants in the interface.

There are several constant interfaces in the Java platform libraries,

such as java.io.ObjectStreamConstants. These interfaces should be

regarded as anomalies and should not be emulated.

