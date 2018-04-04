### 第11条：覆盖equals方法时总要覆盖hashCode方法

**You must override hashCode in every class that overrides equals.** If you fail to do so, your class will violate the general contract for hashCode, which will prevent it from functioning properly in collections such as HashMap and HashSet. Here is the contract, adapted from the Object specification :

* When the hashCode method is invoked on an object repeatedly during an execution of an application, it must consistently return the same value, provided no information used in equals comparisons is modified. This value need not remain consistent from one execution of an application to another.

* If two objects are equal according to the equals\(Object\) method, then calling hashCode on the two objects must produce the same integer result.

* If two objects are unequal according to the equals\(Object\) method, it is not required that calling hashCode on each of the objects must produce distinct results. However, the programmer should be aware that producing distinct results for unequal objects may improve the performance of hash tables.

**在每个覆盖了equals方法的类里，我们也必须覆盖hashCode方法。**如果我们不这么做，我们的类将会违反hashCode的通用约定，而这也将导致我们的类无法在诸如HashMap和HashSet这样的集合中正常运作。下面是约定的内容，依据于Object规范：

* 只要equals方法里用到的信息没有被更改，那么在一个应用的执行期间，不管调用hashCode方法多少次，hashCode方法都必须持续返回相同的值。但在一个应用程序的多次执行过程中，这个值可以不一致。
* 若两个对象根据equals\(Object\)方法比较是相等的，那么分别调用这两个对象的hashCode方法时必须产生相同的整型数值。
* 若两个对象根据equals\(Object\)方法比较是不等的，那么分别调用这两个对象的hashCode方法时不一定要产生不同的数值。只是程序员应该知道，对于不等的对象若能产生不同的哈希值，有助于提高哈希表的性能。



