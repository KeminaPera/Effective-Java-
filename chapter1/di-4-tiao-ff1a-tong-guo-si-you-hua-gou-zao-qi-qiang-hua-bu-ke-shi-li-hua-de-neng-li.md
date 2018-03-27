### 第4条：通过私有化构造器强化不可实例化的能力

Occasionally you’ll want to write a class that is just a grouping of static methods and static fields. Such classes have acquired a bad reputation because some people abuse them to avoid thinking in terms of objects, but they do have valid uses. They can be used to group related methods on primitive values or arrays, in the manner of _java.lang.Math_ or _java.util.Arrays_. They can also be used to group static methods, including factories \(Item 1\), for objects that implement some interface, in the manner of _java.util.Collections_. \(As of Java 8, you can also put such methods in the interface, assuming it’s yours to modify.\) Lastly, such classes can be used to group methods on a final class, since you can’t put them in a subclass.





