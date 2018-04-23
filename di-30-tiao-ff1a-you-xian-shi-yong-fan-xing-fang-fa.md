### 第30条：优先使用泛型方法

Just as classes can be generic, so can methods. Static utility methods that operate on parameterized types are usually generic. All of the “algorithm” methods in Collections \(such as binarySearch and sort\) are generic.

就像类可以是泛型的那样，方法也是可以的。作用与参数化类型的静态工具方法通常都是泛型的。Collections类的所有“算法”方法（如binarySearch方法和sort方法）都是泛型的。

Writing generic methods is similar to writing generic types. Consider this deficient method, which returns the union of two sets:

编写泛型方法类似于编写泛型类型。考虑下面这个有缺陷的方法，它返回了两个集合的并集：



