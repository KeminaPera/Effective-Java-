### 第32条：合理结合泛型和变长参数

Varargs methods \(Item 53\) and generics were both added to the platform in Java 5, so you might expect them to interact gracefully; sadly, they do not. The purpose of varargs is to allow clients to pass a variable number of arguments to a method, but it is a leaky abstraction: when you invoke a varargs method, an array is created to hold the varargs parameters; that array, which should be an implementation detail, is visible. As a consequence, you get confusing compiler warnings when varargs parameters have generic or parameterized types.

变长参数方法（条目53）和泛型都在Java 5的时候被加进来，所以你可能会期望它们彼此间能优雅地进行交互；但不幸地是，事实并不是这样的。变长参数的目的是为了允许客户端可以往方法里传入数量可变的参数，但它的抽象做的其实并不好：当你调用一个变长参数方法时，一个数组也会被创建并用其来存储传进来的变长参数；这个本应该属于实现细节的数组是可见的。结果，当变长参数是泛型类型或者参数化类型时，你就会得到编译器警告。

Recall from Item 28 that a non-reifiable type is one whose runtime representation has less information than its compile-time representation, and that nearly all generic and parameterized types are non-reifiable. If a method declares its varargs parameter to be of a non-reifiable type, the compiler generates a warning on the declaration. If the method is invoked on varargs parameters whose inferred type is non-reifiable, the compiler generates a warning on the invocation too. The warnings look something like this:

回顾下条目28，不可具化类型是指运行时的表示比编译时的表示具有更少信息的类型，并且几乎所有的泛型和参数化类型都是不可具化的。如果一个方法将它的变长参数声明为不可具化类型，那么编译器会对这个声明产生一个警告。如果

