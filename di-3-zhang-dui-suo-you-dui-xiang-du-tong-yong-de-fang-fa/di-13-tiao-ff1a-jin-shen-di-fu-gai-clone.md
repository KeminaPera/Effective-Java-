### 第13条：谨慎地覆盖clone

The Cloneable interface was intended as a mixin interface\(Item 20\) for classes to advertise that they permit cloning. Unfortunately, it fails to serve this purpose. Its primary flaw is that it lacks a clone method, and Object’s clone method is protected. You cannot, without resorting to reflection\(Item 65\), invoke clone on an object merely because it implements Cloneable. Even a reflective invocation may fail, because there is no guarantee that the object has an accessible clone method. Despite this flaw and many others, the facility is in reasonably wide use, so it pays to understand it. This item tells you how to implement a well-behaved clone method, discusses when it is appropriate to do so, and presents alternatives.

Clonable接口的目的是为类提供mixin接口（mixin interface，条目20），表明它们是允许克隆的。但不幸的是，它并没有成功地达到这个目的。它的主要缺陷是，它缺少一个clone方法，而且Object的clone方法是受保护的。如果不借助于反射（reflection，条目65），我们无法仅仅是因为一个对象实现了Clonable接口就调用它的clone方法。即使是采用反射的调用方式也会失败，因为谁也不能保证这个对象有可访问的clone方法。但抛开这样那样的缺陷，这种办法仍被广泛使用着，所以还是值得我们进一步去了解它。本条目将会告诉我们如何实现一个行为良好的clone方法，讨论什么时候适合这么做，并展示一些替代性的做法。

So what does Cloneable do, given that it contains no methods? It determines the behavior of Object’s protected clone implementation: if a class implements Cloneable, Object’s clone method returns a field-by-field copy of the object; otherwise it throws CloneNotSupportedException. This is a highly a typical use of interfaces and not one to be emulated. Normally, implementing an interface says something about what a class can do for its clients. In this case, it modifies the behavior of a protected method on a superclass.

所以既然Cloneable接口不包含任何方法，那么它是拿来干嘛的？它决定了Object中受保护的clone方法的实现的行为：如果一个类实现了Cloneable接口，Object的clone方法将返回一个属性逐一拷贝的对象，否则它就抛出一个CloneNotSupportedException异常。这是接口一种及其典型的用法，也不值得被效仿。通常情况下，实现一个接口意味着这个类能为客户端做某些事情。但对于Cloenable的情况，它改变了父类受保护方法的行为。

Though the specification doesn’t say it, **in practice, a class implementing Cloneable is expected to provide a properly functioning public clone method.** In order to achieve this, the class and all of its superclasses must obey a complex, unenforceable, thinly documented protocol. The resulting mechanism is fragile, dangerous, and extralinguistic: it creates objects without calling a constructor.

虽然在规范中没有明说，但**实际中，一个实现了Cloneable的类通常被期望能提供一个运转良好的公有clone方法。**为了做到这一点，该类和它所有的父类都必须遵守一个复杂，不可实施的，并且基本没有文档说明的协议。由此产生的机制是脆弱，危险而且不受语言影响的：它不用调用构造器就创建了对象。

The general contract for the clone method is weak. Here it is, copied from the Object specification :

clone方法的通用约定是非常弱的，下面是来自Object规范的约定内容：

Creates and returns a copy of this object. The precise meaning of “copy” may depend on the class of the object. The general intent is that, for any object x, the expression _x.clone\(\) != x \_will be true, and the expression \_x.clone\(\).getClass\(\) == x.getClass\(\)_ will be true, but these are not absolute requirements. While it is typically the case that _x.clone\(\).equals\(x\)_ will be true, this is not an absolute requirement.

创建和返回对象的一个拷贝。“拷贝”的精确含义依赖于对象所属的类。一般的含义是，对于任意对象x，表达式_x.clone\(\) != x_将会是true，表达式_x.clone\(\).getClass\(\) == x.getClass\(\)_也将会是true，但这些并不是绝对的要求。虽然通常情况下，_x.clone\(\).equals\(x\)_将会是true，但在也不是个绝对的要求。

By convention, the object returned by this method should be obtained by calling _super.clone_. If a class and all of its superclasses \(except Object\) obey this convention, it will be the case that _x.clone\(\).getClass\(\) == x.getClass\(\)_. 

按照惯例，clone方法返回的对象应该通过调用_super.clone_获得。如果一个类及其所有的父类（除了Object）都遵循了这个惯例，那么就可以做到_x.clone\(\).getClass\(\) == x.getClass\(\)。_

By convention, the returned object should be independent of the object being cloned. To achieve this independence, it may be necessary to modify one or more fields of the object returned by _super.clone_ before returning it.

按照惯例，clone方法返回的对象应该独立于被克隆的对象。为了得到这个独立性，在方法返回之前，可能需要对_super.clone_返回对象的一个或多个属性进行修改。

This mechanism is vaguely similar to constructor chaining, except that it isn’t enforced: if a class’s clone method returns an instance that is not obtained by calling super.clone but by calling a constructor, the compiler won’t complain, but if a subclass of that class calls super.clone, the resulting object will have the wrong class, preventing the subclass from clone method from working properly. If a class that overrides clone is final, this convention may be safely ignored, as there are no subclasses to worry about. But if a final class has a clone method that does not invoke super.clone, there is no reason for the class to implement Cloneable, as it doesn’t rely on the behavior of Object’s clone implementation.

这种机制有点类似于构造器的调用链，只是这种机制不是强制执行的：如果一个类的clone方法返回了一个实例，但这个实例不是通过调用super.clone获得而是通过调用构造器获得，编译器也不会抱怨，但如果这个类的一个子类调用了super.clone，那么返回的对象将包含错误的类，使得通过clone方法获得的子类不能正常运转。如果一个覆盖了clone方法的类是final型的，这个惯例可以安全地被忽视，因为这个类不会有子类。但如果一个final型的类有一个并不调用super.clone的clone方法时，那么也没有理由为这个类实现Cloneable接口，因为它并不依赖于Object的clone实现的行为。



