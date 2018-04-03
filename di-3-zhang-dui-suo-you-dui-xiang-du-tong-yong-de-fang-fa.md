### 第3章 对所有对象都通用的方法

ALTHOUGH Object is a concrete class, it is designed primarily for extension. All of its nonfinal methods \(equals, hashCode, toString, clone, and finalize\) have explicit general contracts because they are designed to be overridden. It is the responsibility of any class overriding these methods to obey their general contracts; failure to do so will prevent other classes that depend on the contracts \(such as HashMap and HashSet\) from functioning properly in conjunction with the class.

虽然Object是个具体的类，但它主要是被设计来继承的。Object的所有非final方法（equals，hashCode，toString，clone和finalize）都有明确的通用约定（general contracts），因为这些方法就是设计来被覆盖的。任何类在覆盖这些方法时，都应该遵守这些通过约定。

This chapter tells you when and how to override the nonfinal Object methods. The finalizemethod is omitted from this chapter because it was discussed in Item 8. While notan Object method, Comparable.compareTo is discussed in this chapter because it has a similar character.

