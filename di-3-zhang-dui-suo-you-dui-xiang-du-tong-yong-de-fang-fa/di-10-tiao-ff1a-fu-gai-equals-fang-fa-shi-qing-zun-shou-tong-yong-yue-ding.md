### 第10条：覆盖equals方法时请遵守通用约定

Overriding the equals method seems simple, but there are many ways to get it wrong, and consequences can be dire. The easiest way to avoid problems is not to override the equals method, in which case each instance of the class is equal only to itself. This is the right thing to do if any of the following conditions apply:

覆盖equals方法看起来好像挺简单的，但其实有许多覆盖方式会导致错误，并且会导致严重的后果。避免这些问题最简单的方式是不覆盖equals方法，在这种情况下，类的每个实例只跟它自己等价。如果满足以下任一条件，那么我们做的就是所期望的：

• Each instance of the class is inherently unique. This is

true for classes such as Thread that represent active entities rather

than values. The equals implementation provided by Object has

exactly the right behavior for these classes.

• There is no need for the class to provide a “logical

equality” test. For example, java.util.regex.Pattern could have

overridden equals to check whether two Pattern instances

represented exactly the same regular expression, but the designers

didn’t think that clients would need or want this functionality.

Under these circumstances, the equals implementation inherited

from Object is ideal.

• A superclass has already overridden equals, and the

superclass behavior is appropriate for this class. For

example, most Set implementations inherit

their equals implementation from AbstractSet, List implementations

from AbstractList, and Map implementations from AbstractMap.

• The class is private or package-private, and you are

certain that its equals method will never be invoked. If you

are extremely risk-averse, you can override the equals method to

ensure that it isn’t invoked accidentally:

