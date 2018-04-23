### 第30条：优先使用泛型方法

Just as classes can be generic, so can methods. Static utility methods that operate on parameterized types are usually generic. All of the “algorithm” methods in Collections \(such as binarySearchand sort\) are generic.

Writing generic methods is similar to writing generic types. Consider this deficient method, which returns the union of two sets:

