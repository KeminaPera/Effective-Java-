### 第3章 对所有对象都通用的方法

ALTHOUGH Object is a concrete class, it is designed primarily for extension. All of its nonfinal methods \(equals, hashCode, toString, clone, and finalize\) have explicit general contracts because they are designed to be overridden. It is the responsibility of any class overriding these methods to obey their general contracts; failure to do so will prevent other classes that depend on the contracts \(such as HashMap and HashSet\) from functioning properly in conjunction with the class.

虽然Object是个具体的类，但它主要是被设计来继承的。Object的所有非final方法（equals，hashCode，toString，clone和finalize）都有明确的通用约定（general contracts），因为这些方法就是设计来被覆盖的。任何类在覆盖这些方法时，都应该遵守这些通过约定。若某个类不这么做，那么将阻止其它依赖这些约定的类（如HashMap和HashSet）与这个类的正常运转。

This chapter tells you when and how to override the nonfinal Object methods. The finalize method is omitted from this chapter because it was discussed in Item 8. While not an Object method, Comparable.compareTo is discussed in this chapter because it has a similar character.

本章将告诉我们何时并如何去覆盖Object对象的非final方法。由于我们在条目8已经讨论过finalize方法（清理方法），故在此不做讨论。虽然Comparable.compareTo不属于Object对象的方法，但由于它有类似的特征，所以我们也会在本章进行讨论。

