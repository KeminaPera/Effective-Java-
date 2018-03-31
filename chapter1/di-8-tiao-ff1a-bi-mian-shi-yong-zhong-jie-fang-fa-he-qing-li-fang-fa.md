### 第8条：避免使用终结方法和清理方法

**Finalizers are unpredictable, often dangerous, and generally unnecessary. **Their use can cause erratic behavior, poor performance, and portability problems. Finalizers have a few valid uses, which we’ll cover later in this item, but as a rule, you should avoid them. As of Java 9, finalizers have been deprecated, but they are still being used by the Java libraries. The Java 9 replacement for finalizers is cleaners. **Cleaners are less dangerous than finalizers, but still unpredictable, slow, and generally unnecessary.**

**终结方法（Finalizer）是不可预知的，很多时候是危险的，而且一般情况下是不必要的。**使用它们会导致程序行为不稳定，性能降低还有移植问题。终结方法只有少数几种用途，我们将会本条目后面谈到。但根据经验，我们应该避免使用终结方法。在Java 9中，终结方法已经被遗弃了，但它们仍被Java类库使用，相应用来替代终结方法的是清理方法（cleaner）。**比起终结方法，清理方法相对安全点，但仍是不可以预知的，运行慢的，而且一般情况下是不必要的。**

C++ programmers are cautioned not to think of finalizers or cleaners as Java’s analogue of C++ destructors. In C++, destructors are the normal way to reclaim the resources associated with an object, a necessary counterpart to constructors. In Java, the garbage collector reclaims the storage associated with an object when it becomes unreachable, requiring no special effort on the part of the programmer. C++ destructors are also used to reclaim other nonmemory resources. In Java, a try-with-resources or try-finally block is used for this purpose \(Item 9\).

C++程序员要注意的是，不要把Java的终结方法或清理方法看成是C++析构器（destructor）。在C++中，析构器是回收对象占用资源的一种常规方式。而在Java中，垃圾回收器在对象变得不可到达的时候才回收对象占用的资源，不用程序员做额外的工作。C++的析构器也可以用来回收其它非内存资源。相应地，Java中使用try-with-resources或者try-finally代码块来实现这个目的（条目9）。

One shortcoming of finalizers and cleaners is that there is no guarantee they’ll be executed promptly \[JLS, 12.6\]. It can take arbitrarily long between the time that an object becomes unreachable and the time its finalizer or cleaner runs. This means that you should **never do anything time-critical in a finalizer or cleaner.**For example, it is a grave error to depend on a finalizer or cleaner to close files because open file descriptors are a limited resource. If many files are left open as a result of the system’s tardiness in running finalizers or cleaners, a program may fail because it can no longer open files.

终结方法和清理方法的一个缺点是无法保证它们及时地被执行\[JLS, 12.6\]。一个对象从变得不可到达开始到它的终结方法和清理方法被执行，中间可能会经过任意长的时间。这意味着，我们**不应该在终结方法和清理方法中做对时间有严格要求的任务。**例如，依赖终结方法或者清理方法来关闭文件资源是个严重的错误，因为打开文件的描述符是个有限的资源。如果在一段程序中很多文件都因为系统延迟执行终结方法或清理方法而停留在打开状态，那么当这段程序再打开一个文件时就会失败。

The promptness with which finalizers and cleaners are executed is primarily a function of the garbage collection algorithm, which varies widely across implementations. The behavior of a program that depends on the promptness of finalizer or cleaner execution may likewise vary. It is entirely possible that such a program will run perfectly on the JVM on which you test it and then fail miserably on the one favored by your most important customer.

及时地执行终结方法和清理方法是垃圾回收算法的一个主要功能。而在不同的JVM实现中，垃圾回收算法会有很大的不同。如果一个程序的行为依赖于终结方法和清理方法的执行时间点，那么在不同的JVM中这些行为也将同样会不同。那样的话完全有可能这样的一段程序在你的测试用的JVM上运行得很好，而在你最重要的客户的JVM上却根本无法运行。



